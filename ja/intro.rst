Zephirの紹介
============
Zephirは、実行可能なコンパイルコードをPHPで書きたいというPHP開発者の大きなニーズに対応した言語です。
それは動的・静的型付けに対応し、見た目はPHP開発者の見慣れたものになっています。

Zephirという名前は「Zend Engine/PHP/Intermediate」の略語です。
読み方は「ゼファー（zephyr）」と発音するとしていますが、実際にはZephirの作者は「ザフィア（zaefire_）」と
発音しています。

Hello World!
------------
全ての言語はそれぞれの「Hello World!」サンプルがありますが、Zephirでの入門例ではこの言語の
いくつかの重要な機能をご紹介します。

Zephirのコードはクラスに書かれる必要があります。この言語はオブジェクト指向のライブラリ/フレームワークを
作成することを意図しているので、クラスの外のコードは許可されていません。また、名前空間が必要です。:

.. code-block:: zephir

    namespace Test;

    /**
     * これがサンプルクラス
     */
    class Hello
    {
        /**
         * これがサンプルメソッド
         */
        public function say()
        {
            echo "Hello World!";
        }
    }

このクラスがコンパイルされると、透過的にgcc/clang/vc++でコンパイルされる下記のコードを生成します。:

.. code-block:: c

    #ifdef HAVE_CONFIG_H
    #include "config.h"
    #endif

    #include "php.h"
    #include "php_test.h"
    #include "test.h"

    #include "kernel/main.h"

    /**
     * これがサンプルクラス
     */
    ZEPHIR_INIT_CLASS(Test_Hello) {
        ZEPHIR_REGISTER_CLASS(Test, Hello, hello, test_hello_method_entry, 0);
        return SUCCESS;
    }

    /**
     * これがサンプルメソッド
     */
    PHP_METHOD(Test_Hello, say) {
        php_printf("%s", "Hello World!");
    }

実際のところ、Zephirを使用する開発者がC言語について知っている事も理解する事も求められていません。
ですがもしあなたがPHPの内部やC言語自体でコンパイラを使った経験があれば、
開発者としてZephirで作業する際により明確なシナリオが見えてくるでしょう。

Zephirのお味
------------
下記の例では詳細についてじっくり解説しますので、あなたはそこで何が行われているかを理解するでしょう。
目標はあなたにZephirでのプログラミングの感覚を掴んでもらうことです。
私たちは以降の章で説明している機能の内容を見ていきます。

下記の例では非常にシンプルに、配列の型をチェックするクラスとメソッドを実装します。

それでは詳細のコードを調べながら、Zephirの文法を学んでいきましょう。
わずか数行のコードですが内容は盛りだくさんです！ここでは一般的な考え方を説明します:

.. code-block:: zephir

    namespace Test;

    /**
     * MyTest (test/mytest.zep)
     */
    class MyTest
    {
        public function someMethod()
        {
            /* 変数は定義されなければなりません */
            var myArray;
            int i = 0, length;

            /* 配列を作ります */
            let myArray = ["hello", 0, 100.25, false, null];

            /* 配列の要素数を 'int' 変数にセットします */
            let length = count(myArray);

            /* 変数の型を表示します */
            while i < length {
                echo typeof myArray[i], "\n";
                let i++;
            }

            return myArray;
        }
    }

このメソッドでは、最初の行で 'var' と 'int' キーワードを使ってローカル変数が定義されます。
メソッド内の全ての変数は、それぞれの型で定義する必要があります。
この定義はオプションではなく、これによってコンパイラは、変数のタイプミスや
スコープ外での変数の利用といった普段実行時エラーで終了する内容をあなたに知らせてくれる
助けになります。

動的変数は、キーワード 'var'で宣言されています。
これらの変数は異なる他の型に再割り当てすることができます。
一方、整数型に静的型付けされた 'i' と 'length' という、実行時にこの型の値だけをもつ変数があります。

PHPとは対照的に、あなたは変数名の前にドル記号（$）を書く必要はありません。
ZephirではJava, C#, C++と同じコメント規則に従い、「//コメント」は行末まで、「/* コメント \*/」は
行を跨いだ範囲をコメントとします。

変数はデフォルトでは不変で、これはZephirがほとんどの変数の値が変わらないことを想定していることを
意味します。初期値を保持する変数は、コンパイラによって静的定数として最適化されます。
変数の値を変更する必要がある場合、キーワード 'let'を使用しなければなりません。:

.. code-block:: zephir

    /* 配列を作ります */
    let myArray = ["hello", 0, 100.25, false, null];

デフォルトでは配列はPHPのように動的で、異なる型の値を持つ事ができます。
PHPユーザーランドからの関数は、Zephirのコードで呼ばれます。例の中で関数 'count' が呼ばれていますが、
この配列の要素数は既に分かっているため、コンパイラはこの呼び出しを避けるように最適化します。:

.. code-block:: zephir

    /* 配列の要素数を 'int' 変数にセットします */
    let length = count(myArray);

フロー制御文のカッコはオプションです。使った方が快適であれば使うことができます。

.. code-block:: zephir

    while i < length {
        echo typeof myArray[i], "\n";
        let i++;
    }

PHPは動的変数のみで動作し、メソッドは常に動的変数を返します。
これは静的型付けされた変数が返された場合に、PHP側で使えるように動的型付け変数が返されることを意味します。
メモリはコンパイラによって自動的に管理されるため、C言語のようにあなたがメモリをアロケートしたり
解放したりする必要はなく、PHPと同じように動作する事にご注意ください。

.. _zaefire: http://translate.google.com/#en/en/zaefire
