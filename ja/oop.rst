クラスとオブジェクト
====================
Zephir はオブジェクト指向プログラミングを推奨しているため、
あなたの拡張機能はクラスやメソッドだけをエクスポートすることが可能で、
実行時エラーは多くの場合警告やエラーの代わりに例外を発生させます。


クラス
------
全ての Zephir のファイルはクラスかインタフェースを実装する必要があります。
クラスの構造は PHP のクラスによく似ています。:

.. code-block:: zephir

    namespace Test;

    /**
     * サンプルクラスです
     */
    class MyClass
    {

    }

クラス修飾子
^^^^^^^^^^^^
下記のクラス修飾子がサポートされます:

*Final*: クラスがこの修飾子を持っている場合は、継承できません。

.. code-block:: zephir

    namespace Test;

    /**
     * このクラスは他のクラスから継承できません
     */
    final class MyClass
    {

    }

*Abstract*: クラスがこの修飾子を持っている場合は、インスタンス化すことができません

.. code-block:: zephir

    namespace Test;

    /**
     * このクラスはインスタンス化できません
     */
    abstract class MyClass
    {

    }

メソッドの実装
--------------
"function" キーワードでメソッドが実現します。
Zephir ではメソッドの可視性修飾子が PHP から利用可能な形で明示されている必要があります。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        public function myPublicMethod()
        {
            // ...
        }

        protected function myProtectedMethod()
        {
            // ...
        }

        private function myPrivateMethod()
        {
            // ...
        }
    }

メソッドは必須または任意のパラメータを受け取る事ができます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        /**
         * 全て必須パラメータ
         */
        public function doSum1(a, b)
        {
            return a + b;
        }

        /**
         * 'a' は必須で、'b' は初期値ありの任意
         */
        public function doSum2(a, b = 3)
        {
            return a + b;
        }

        /**
         * 両方のパラメータが任意
         */
        public function doSum3(a = 1, b = 2)
        {
            return a + b;
        }

        /**
         * パラメータは必須で、整数型でなければならない
         */
        public function doSum4(int a, int b)
        {
            return a + b;
        }

        /**
         * 型と初期値の指定
         */
        public function doSum4(int a = 4, int b = 2)
        {
            return a + b;
        }
    }

任意の null 値のパラメータ
^^^^^^^^^^^^^^^^^^^^^^^^^^
Zephir では、変数値の型が、変数宣言時の型のままであることを保証します。
この保証によって、Zephir では変数値が null の場合に、最も近い値に変換します。:

.. code-block:: zephir

    public function foo(int a = null)
    {
        echo a; // もし "a" が引き継がれなければ、0 を表示する。
    }

    public function foo(boolean a = null)
    {
        echo a; // もし "a" が引き継がれなければ、false を表示する。
    }

    public function foo(string a = null)
    {
        echo a; // もし "a" が引き継がれなければ、空文字列を表示する。
    }

    public function foo(array a = null)
    {
        var_dump(a); // もし "a" が引き継がれなければ、空配列を表示する。
    }

可視性のサポート
^^^^^^^^^^^^^^^^

* Public: "public" とマークされたメソッドが PHP拡張モジュールに出力され、拡張モジュール内から同様に PHP からも参照できるようになります。

* Protected: "protected" とマークされたメソッドが PHP拡張モジュールに出力され、拡張モジュール内から同様に PHP からも参照できるようになります。しかし、protected メソッドはクラス内またはこのクラスを継承したクラス内からのみ呼び出すことができます。

* Private: "private" は PHP拡張モジュールには出力されません。private メソッドはそのクラス内からのみ参照することができます。

サポートされる修飾子
^^^^^^^^^^^^^^^^^^^^

* Final: この修飾子を持っているメソッドは、オーバーライドできません。

* Deprecated: "deprecated" とマークされたメソッドが呼び出された場合、E_DEPRECATED エラーが発生します。

Getter/Setter ショートカット
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
C# のように、Zephir では get/set/toString ショートカットを使うことができます。
これはプロパティへの setter/getter を明示的に実装することなく簡単に実現する機能です。

例えば、ショートカットを使わないコードは下記のようになります。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {
        protected myProperty;

        protected someProperty = 10;

        public function setMyProperty(myProperty)
        {
            this->myProperty = myProperty;
        }

        public function getMyProperty()
        {
            return this->myProperty;
        }

        public function setSomeProperty(someProperty)
        {
            this->someProperty = someProperty;
        }

        public function getSomeProperty()
        {
            return this->someProperty;
        }

        public function __toString()
        {
            return this->myProperty;
        }

     }

同じ内容を、ショートカットを使うと下記のようになります。:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        protected myProperty {
            set, get, toString
        };

        protected someProperty = 10 {
            set, get
        };

    }

これらのメソッドはコンパイル時に実メソッドとして出力されますが、
これらを１つ１つ記述する必要はありません。

戻り値の型のヒント
^^^^^^^^^^^^^^^^^^
クラスおよびインターフェースのメソッドは、「戻り値の型のヒント」を持つ事ができます。
これらはアプリケーションのアプリケーションのエラーをあなたにお知らせするために、
コンパイラに有益な追加情報を提供します。次の例を考えてみましょう。:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        public function getSomeData() -> string
        {
            // 戻り値（boolean）が期待される型（文字列）ではないため
            // コンパイルエラーになります
            return false;
        }

        public function getSomeOther() -> <App\MyInterface>
        {
            // 返されるオブジェクトが期待されるインターフェース
            // （App\MyInterface）を実装していないため、コンパイル
            // エラーになります
            return new App\MyObject;
        }

        public function process()
        {
            var myObject;

            // 型ヒントがコンパイラに、 myObject が App\MyInterface を
            // 実装したクラスのインスタンスであることを伝える
            let myObject = this->getSomeOther();

            // コンパイラは、 App\MyInterface に メソッド "someMethod" が
            // 実装されているかをチェックする
            echo myObject->someMethod();
        }

    }

メソッドは戻り値として、複数種類の型を持つ事ができます。
もし複数種類の型を定義する場合、「 | (パイプ)」演算子でこれらの型を区切ります。

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        public function getSomeData(a) -> string | bool
        {
            if a == false {
                return false;
            }
            return "error";
        }
    }

戻り値の型: Void
^^^^^^^^^^^^^^^^
メソッドには‘void’を付けることもできます。
これはメソッドがどのようなデータも戻さないという意味です。:

.. code-block:: zephir

    public function setConnection(connection) -> void
    {
        let this->_connection = connection;
    }

これが何の役に立つのでしょう？
コンパイラはプログラムがこれらのメソッドからの戻り値を期待しているかどうかを検知して、
コンパイルエラーが発生させることができるからです。:

.. code-block:: zephir

    let myDb = db->setConnection(connection);
    myDb->execute("SELECT * FROM robots"); // ここでコンパイルエラーが発生します

厳格または柔軟なパラメータのデータ型
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Zephir では、メソッドの各パラメータのデータ型を指定することができます。
デフォルトでは これらのデータ型には柔軟性があり、間違った（ただし互換性のある）データ型の値が
引き継がれても、 Zephir は透過的に期待する値に変換しようとします。:

.. code-block:: zephir

    public function filterText(string text, boolean escape=false)
    {
        //...
    }

上記のメソッドが呼び出されると、下記のように動作します。:

.. code-block:: php

    <?php

    $o->filterText(1111, 1); // OK
    $o->filterText("some text", null); // OK
    $o->filterText(null, true); // OK
    $o->filterText("some text", true); // OK
    $o->filterText(array(1, 2, 3), true); // FAIL

しかし、間違った型を引き継ぐことはしばしばバグに繋がり、
特定のAPIの間違った使用は予期せぬ結果を招くでしょう。
あなたはパラメータに厳格なデータ型を設定することで、
自動変換を禁止することができます。:

.. code-block:: zephir

    public function filterText(string! text, boolean escape=false)
    {
        //...
    }

これで間違った型での呼び出しのほとんどで、不正なデータ型が引き継がれたと
例外が発生します。:

.. code-block:: php

    <?php

    $o->filterText(1111, 1); // FAIL
    $o->filterText("some text", null); // OK
    $o->filterText(null, true); // FAIL
    $o->filterText("some text", true); // OK
    $o->filterText(array(1, 2, 3), true); // FAIL

どのパラメータを厳格にし、どのパラメータが柔軟でなければならないかを指定することで、
開発者は期待した通りの動作を得ることができるのです。

読み込み専用パラメータ
^^^^^^^^^^^^^^^^^^^^^^
キーワード 'const' は、パラメータを読み込み専用にすることができます。
これは `定数の正確性 <http://en.wikipedia.org/wiki/Const-correctness>`_ を尊重する上で役立ちます。
この属性を付けられたパラメータは、メソッド内で変更することができません。:

.. code-block:: zephir

    namespace App;

    class MyClass
    {
        // "a" は読み込み専用
        public function getSomeData(const string a)
        {
            // コンパイルエラーになります
            let a = "hello";
        }
    }

パラメータが読み込み専用として宣言されている場合、
コンパイラが安全とみなしてこれらの変数をさらに最適化することができます。

プロパティの実装
----------------
クラスのメンバー変数は「プロパティ」と呼ばれます。
デフォルトでは、プロパティは PHP のプロパティのように動作します。
プロパティは PHP拡張モジュールに出力され、PHPコードから参照可能です。
プロパティは、 PHP では通常外部から利用可能な可視性修飾子になりますが、
Zephir では可視性修飾子の明示的な指定が必須です。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        public myProperty1;

        protected myProperty2;

        private myProperty3;

    }

クラス内の static 以外のプロパティには、「 -> (オブジェクト演算子)」を使って、
「 this->property 」（property はプロパティ名）というようにアクセスできます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected myProperty;

        public function setMyProperty(var myProperty)
        {
            let this->myProperty = myProperty;
        }

        public function getMyProperty()
        {
            return this->myProperty;
        }
    }

プロパティは定数と互換性のある初期値を持つことができます。
これらはコンパイル時に評価することができなければならず、
評価するために実行時の情報に依存してはいけません。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected myProperty1 = null;
        protected myProperty2 = false;
        protected myProperty3 = 2.0;
        protected myProperty4 = 5;
        protected myProperty5 = "my value";
    }

プロパティの更新
^^^^^^^^^^^^^^^^
プロパティは '->' 演算子を使用してそれらにアクセスすることによって更新することができます。:

.. code-block:: zephir

    let this->myProperty = 100;

Zephir では、プログラムでプロパティにアクセスしていた場合に、それが存在するかを
チェックし、プロパティが定義されていなければコンパイラエラーになります。:

.. code-block:: php

    CompilerException: Property '_optionsx' is not defined on class 'App\MyClass' in /Users/scott/utils/app/myclass.zep on line 62

          this->_optionsx = options;
          ------------^

もしあなたがコンパイラのチェックを回避したいか、プロパティを動的に作成したい場合は、
プロパティ名を引用符で囲むことができます。:

.. code-block:: zephir

    let this->{"myProperty"} = 100;

また、プロパティを更新するために単純な変数を使用することもでき、プロパティ名は
変数から取得されます。:

.. code-block:: zephir

    let someProperty = "myProperty";
    let this->{someProperty} = 100;

プロパティ値の読込み
^^^^^^^^^^^^^^^^^^^^
プロパティは '->' 演算子でアクセスして読み込むことができます。:

.. code-block:: zephir

    echo this->myProperty;

更新する時のように、プロパティは動的に読み込むことができます。:

.. code-block:: zephir

    // コンパイラチェックを避け、動的なユーザー定義プロパティを読み込む
    echo this->{"myProperty"};

    // 変数値を使って読み込む
    let someProperty = "myProperty";
    echo this->{someProperty}

クラス定数
----------
クラスには、拡張機能がコンパイルされると不変のままとなるクラス定数を含むことができます。

クラス定数はPHPから使用可能な PHP拡張モジュールに出力されます。

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        const MYCONSTANT1 = false;
        const MYCONSTANT2 = 1.0;
    }

クラス定数には、クラス名と静的演算子(::)を使ってアクセスできます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        const MYCONSTANT1 = false;
        const MYCONSTANT2 = 1.0;

        public function someMethod()
        {
            return MyClass::MYCONSTANT1;
        }
    }

メソッド呼び出し
----------------
メソッドは、オブジェクト演算子( -> )を使って呼び出すことができます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected function _someHiddenMethod(a, b)
        {
            return a - b;
        }

        public function someMethod(c, d)
        {
            return this->_someHiddenMethod(c, d);
        }
    }

静的メソッドには静的演算子(::)を使ってアクセスできます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        protected static function _someHiddenMethod(a, b)
        {
            return a - b;
        }

        public static function someMethod(c, d)
        {
            return self::_someHiddenMethod(c, d);
        }
    }

次のように動的にメソッドを呼び出すこともできます。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {
        protected adapter;

        public function setAdapter(var adapter)
        {
            let this->adapter = adapter;
        }

        public function someMethod(var methodName)
        {
            return this->adapter->{methodName}();
        }
    }

名前付きパラメータ
^^^^^^^^^^^^^^^^^^
Zephir は、名前やキーワードをもったパラメータを引数とするメソッド呼び出しを
サポートしています。
名前付きパラメータは、任意の順序でパラメータを渡したい場合や、
パラメータの意味を記述したい場合、もっと簡潔にパラメータを指定したい場合に
有効です。

次の例を見てみましょう。「 Image 」クラスには、４つのパラメータを受け取る
メソッドがあります。:

.. code-block:: zephir

    namespace Test;

    class Image
    {
        public function chop(width = 600, height = 400, x = 0, y = 0)
        {
            //...
        }
    }

メソッド呼び出しのスタンダードな方法を使うと:

.. code-block:: zephir

    i->chop(100); // width=100, height=400, x=0, y=0
    i->chop(100, 50, 10, 20); // width=100, height=50, x=10, y=20

名前付きパラメータを使うこともできます。:

.. code-block:: zephir

    i->chop(width: 100); // width=100, height=400, x=0, y=0
    i->chop(height: 200); // width=600, height=200, x=0, y=0
    i->chop(height: 200, width: 100); // width=100, height=200, x=0, y=0
    i->chop(x: 20, y: 30); // width=600, height=400, x=20, y=30

（コンパイル時に）コンパイラがこれらのパラメータの正しい順序が分からない場合、
これらは実行時に解決されます。その場合は最低限の余分なオーバーヘッドが
生じるかもしれません。:

.. code-block:: zephir

    let i = new {someClass}();
    i->chop(y:30, x: 20);
