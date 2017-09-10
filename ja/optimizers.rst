カスタム オプティマイザ
=======================
Zephir の最も一般的な機能は、内部オプティマイザを使用しています。
「オプティマイザ」は、関数呼び出しのインターセプタのように機能します。
「オプティマイザ」は、PHP ユーザー領域の関数の呼び出しを、より高速でオーバーヘッドが低い
C 言語の呼び出しに置き換えることによってパフォーマンスを向上させます。

オプティマイザを作成するには、 'optimizers' ディレクトリにクラスを作成する必要があります。
以下の規約を使用する必要があります。:

+--------------------+----------------------------+----------------------------------------------------------+------------------+
| Function in Zephir | Optimizer Class Name       | Optimizer Path                                           | Function in C    |
+====================+============================+==========================================================+==================+
| calculate_pi       | CalculatePiOptimizer       | optimizers/CalculatePiOptimizer.php                      | my_calculate_pi  |
+--------------------+----------------------------+----------------------------------------------------------+------------------+

これは「オプティマイザ」の基本的な構造です。:

.. code-block:: php

    <?php

    namespace Zephir\Optimizers\FunctionCall;

    use Zephir\Call;
    use Zephir\CompilerException;
    use Zephir\CompilationContext;
    use Zephir\Optimizers\OptimizerAbstract;

    class CalculatePiOptimizer extends OptimizerAbstract
    {

        public function optimize(array $expression, Call $call, CompilationContext $context)
        {
            //...
        }

    }

オプティマイザの実装はあなたが書くコードの種類によって大きく異なります。
この例では、関数の呼び出しを C 言語の関数の呼び出しに書き換えています。
Zephir では、この関数を呼び出すコードは下記の通りです。:

.. code-block:: zephir

    let pi = calculate_pi(1000);

つまりオプティマイザはパラメータを１つしか持ちません。
後々問題が生じないように、パラメータのチェックをおこないましょう。:

.. code-block:: php

    <?php

    public function optimize(array $expression, Call $call, CompilationContext $context)
    {

        if (!isset($expression['parameters'])) {
            throw new CompilerException("'calculate_pi' requires one parameter", $expression);
        }

        if (count($expression['parameters']) > 1) {
            throw new CompilerException("'calculate_pi' requires one parameter", $expression);
        }

        //...
    }

ここに値を返さない関数を追加します。私たちの関数は計算された PI の値を返しますから、
計算された値を受け取るためには変数の型が正しいかをチェックする必要があるのです。:

.. code-block:: php

    <?php

    public function optimize(array $expression, Call $call, CompilationContext $context)
    {

        if (!isset($expression['parameters'])) {
            throw new CompilerException("'calculate_pi' requires one parameter", $expression);
        }

        if (count($expression['parameters']) > 1) {
            throw new CompilerException("'calculate_pi' requires one parameter", $expression);
        }

        /**
         * Process the expected symbol to be returned
         */
        $call->processExpectedReturn($context);

        $symbolVariable = $call->getSymbolVariable();
        if (!$symbolVariable->isDouble()) {
            throw new CompilerException("Calculated PI values only can be stored in double variables", $expression);
        }

        //...
    }

コンパイラが例外を投げない場合、返された値が double 型に格納されるかどうかを
チェックしています。

次に行うべきことは、関数に渡されるパラメータを処理することです。:

.. code-block:: php

    <?php

    $resolvedParams = $call->getReadOnlyResolvedParams($expression['parameters'], $context, $expression);

Zephir の慣習として、作成した関数のパラメータを変更しない事は重要です。
渡されたパラメータを変更する場合、Zephir は渡された定数のメモリを割り当てる必要があり、
getReadOnlyResolvedParams の代わりに getResolvedParams を使わなければなりません。

これらのメソッドによって返されるコードは有効なC言語のコードであり、
コード出力で使用して C 関数の呼び出しを生成できます。:

.. code-block:: php

    <?php

    //Generate the C-code
    return new CompiledExpression('double', 'calculate_pi( ' . $resolvedParams[0] . ')', $expression);

全てのオプティマイザは CompiledExpression インスタンスを返さなければなりません。
これによってコードの返す型と関連する C言語のコードをコンパイラに伝えます。

オプティマイザの完全なコードは下記の通り:

.. code-block:: php

    <?php

    namespace Zephir\Optimizers\FunctionCall;

    use Zephir\Call;
    use Zephir\CompilerException;
    use Zephir\CompilationContext;
    use Zephir\CompiledExpression;
    use Zephir\Optimizers\OptimizerAbstract;

    class CalculatePiOptimizer extends OptimizerAbstract
    {

        public function optimize(array $expression, Call $call, CompilationContext $context)
        {

            if (!isset($expression['parameters'])) {
                throw new CompilerException("'calculate_pi' requires one parameter", $expression);
            }

            if (count($expression['parameters']) > 1) {
                throw new CompilerException("'calculate_pi' requires one parameter", $expression);
            }

            /**
             * Process the expected symbol to be returned
             */
            $call->processExpectedReturn($context);

            $symbolVariable = $call->getSymbolVariable();
            if (!$symbolVariable->isDouble()) {
                throw new CompilerException("Calculated PI values only can be stored in double variables", $expression);
            }

            $resolvedParams = $call->getReadOnlyResolvedParams($expression['parameters'], $context, $expression);

            return new CompiledExpression('double', 'my_calculate_pi(' . $resolvedParams[0] . ')', $expression);
        }

    }

"my_calculate_pi" 関数の実装コードは C 言語で書かれており、拡張機能と一緒に
コンパイルする必要があります。

このコードは適切な場所にある「ext」ディレクトリに配置する必要があります。
これらのファイルが Zephir によって生成されたファイルと衝突しないことを確認してください。

このファイルには、Zend Engine のヘッダと関数の C 言語での実装が含まれていなければなりません。:

.. code-block:: c

    #ifdef HAVE_CONFIG_H
    #include "config.h"
    #endif

    #include "php.h"
    #include "php_ext.h"

    double my_calculate_pi(zval *accuracy) {
        return 0.0;
    }

このファイルは、 :doc:`config.json <config>` ファイルの特別なセクションに追加する必要があります。:

.. code-block:: javascript

    "extra-sources": [
        "utils/pi.c"
    ]

最後に設定オプション `optimizer-dirs` を使って、 Zephir があなたのオプティマイザを
見つけられるように場所を指定する必要があります。

この例の完全なソースコードは `こちら <https://github.com/phalcon/zephir-samples/tree/master/ext-optimizers>`_ をご覧ください。

