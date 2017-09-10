例外
====
Zephir は PHP に似た挙動と機能を提供する、非常に低レベルの例外を実装しています。

例外が投げられると、「catch」ブロックは例外を捕捉し、開発者が適切な処理を提供することが
可能です。

.. code-block:: zephir

    var e;
    try {

        // ここで例外を投げます
        throw new \Exception("This is an exception");

    } catch \Exception, e {

        // 例外を処理する
        echo e->getMessage();
    }

Zephir では、そのブロック内で生成された例外を無視して何もしない "try" ブロックを提供します。:

.. code-block:: zephir

    try {
        throw new \Exception("This is an exception");
    }

もし例外をキャッチした時に例外変数が必要ないなら、引き継がなくても構いません:

.. code-block:: zephir

    try {

        // ここで例外を投げます
        throw new \Exception("This is an exception");

    } catch \Exception {

        // 例外を処理する
        echo "An exception occur!";
    }


１つのキャッチブロックで複数の型の例外をキャッチすることができます。:

.. code-block:: zephir

    var e;
    try {

        // ここで例外を投げます
        throw new \Exception("This is an exception");

    } catch \RuntimeException|\Exception, e {

        // 例外を処理する
        echo e->getMessage();
    }

Zephir では、定数や静的型付き変数を例外クラスのメッセージのように投げることができます。:

.. code-block:: zephir

    throw "Test"; // throw new \Exception("Test");
    throw 't'; // throw new \Exception((string) 't');
    throw 123; // throw new \Exception((string) 123);
    throw 123.123; // throw new \Exception((string) 123.123);

Zephir の例外は、 PHP と同様に例外がどこで発生したかを知ることができます。
Exception::getFile() と Exception::getLine() は、例外が投げられた Zephir コード内の位置を返します。:

.. code-block:: html

    Exception: The static method 'someMethod' doesn't exist on model 'Robots'
    File=phalcon/mvc/model.zep Line=4042
    #0 /home/scott/test.php(64): Phalcon\Mvc\Model::__callStatic('someMethod', Array)
    #1 /home/scott/test.php(64): Robots::someMethod()
    #2 {main}
