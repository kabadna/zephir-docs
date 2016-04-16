基本構文
========
この章では、ファイル構成や名前空間、変数宣言やその他の構文規則やいくつかの概念について説明します。

ファイル内のコードの構造と名前空間
----------------------------------
PHPでは、特定の構造を持たない任意のファイルにコードを書くことができます。
Zephirでは、全てのファイルはクラス（それも１つのクラス）を含む必要があります。
全てのクラスは名前空間を持ち、ディレクトリ構造はクラス名と使用される名前空間名と
一致する必要があります。

例えば、各ファイルのクラスは次のような構造を考慮する必要があります。:

.. code-block:: sh

	mylibrary/
		router/
			exception.zep # MyLibrary\Router\Exception
		router.zep # MyLibrary\Router

mylibrary/router.zep 内のクラスです。:

.. code-block:: zephir

	namespace MyLibrary;

	class Router
	{

	}

mylibrary/router/exception.zep 内のクラスです。:

.. code-block:: zephir

	namespace MyLibrary\Router;

	class Exception extends \Exception
	{

	}

Zephir では、規定の場所にファイルやクラスが存在しないか、逆に既定以外のファイルが
存在した場合、コンパイルエラーが発生します。

命令の分離
----------
すでに前の章のサンプルコードで、セミコロンが非常に少ないことにお気付きかもしれません。
あなたはステートメントと式を分離するために、 Java や C/C++ や PHP などの言語のように
セミコロンを使用することができます。:

.. code-block:: zephir

	myObject->myMethod(1, 2, 3); echo "world";

コメント
--------
Zephir では１行コメントである // と、複数行コメントである /* ... \*/ といった
'C'/'C++' のコメント書式をサポートしています。:

.. code-block:: c

	// これは単一行コメントです

	/**
	 * 複数行コメントです
	 */

ほとんどの言語ではコメントというのは単にコンパイラ／インタプリタに無視されるだけの文字列ですが、
Zephir での複数行コメントは Docblock として扱われ、生成されるコードにも出力されますので、
言語の一部なんです！

もし期待されている場所に Docblock が存在しない場合は、コンパイラは例外を投げます。

変数宣言
--------
Zephir では、使用される全ての変数は宣言されなければなりません。
このプロセスはコンパイラにとって、最適化と検証を行う上で非常に重要な情報を提供します。
変数名は一意でなければならず、また予約語を使用することはできません。

.. code-block:: zephir

	// 同一ステートメント内での同じ型の変数の宣言
	var a, b, c;

	// それぞれの変数を別々の行で宣言
	var a;
	var b;
	var c;

変数には必要に応じて互換性のある型の初期値をもつことができますし、
思うがままに変数に新しい値をセットすることができます。:

.. code-block:: zephir

	// 初期値をもつ変数を宣言
	var a = "hello", b = 0, c = 1.0;
	int d = 50; bool some = true;

変数名は大文字・小文字を区別し、下記の変数は別のものです。:

.. code-block:: zephir

	// 別々の変数になる
	var somevalue, someValue, SomeValue;

変数のスコープ
--------------
メソッド内で宣言された全ての変数は、その変数が宣言されたメソッド内で有効です。:

.. code-block:: zephir

    namespace Test;

    class MyClass
    {

        public function someMethod1()
        {
            int a = 1, b = 2;
            return a + b;
        }

        public function someMethod2()
        {
            int a = 3, b = 4;
            return a + b;
        }

    }

スーパーグローバル
------------------
Zephir はグローバル変数をサポートしておらず、PHPユーザーランドからグローバル変数への
アクセスは許可されていません。
しかし、あなたは下記のように PHP のスーパーグローバル変数にはアクセス可能です。:

.. code-block:: zephir

	// _POST から値を取得する
	let price = _POST["price"];

	// _SERVER の値を読み込む
	let requestMethod = _SERVER["REQUEST_METHOD"];

ローカルシンボルテーブル
------------------------
PHPの全てのメソッドやコンテキストは、非常に動的な方法で変数を記述することが可能な
シンボルテーブルを持っています。:

.. code-block:: php

	<?php

	$b = 100;
	$a = "b";
	echo $$a; // prints 100

全ての変数が低レベルの変数にまでコンパイルされるために Zephir ではこの機能は実装しておらず、
特定のコンテキスト内に存在しない変数を知る方法はありません。
もしあなたが現在のPHPのシンボルテーブルに変数を作成したい場合は、下記の構文を使用できます。:

.. code-block:: zephir

	// PHP の変数 $name に値をセットする
	let {"name"} = "hello";

	// PHP の変数 $price に値をセットする
	let name = "price";
	let {name} = 10.2;
