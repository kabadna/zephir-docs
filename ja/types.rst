型
==
Zephir には動的な型と静的な型の両方があります。
この章では、サポートされている型とその挙動に着目します。:

動的な型
--------
動的な変数は PHP のようで、値をセットしても、制限なく異なる型の値をセットしなおすことができます。

当的な変数はキーワード 'var' で宣言され、その挙動は PHP と似ています。:

.. code-block:: zephir

    var a, b, c;

    // 変数の初期値セット
    let a = "hello", b = false;

    // 値の変更
    let a = 10, b = "140";

    // 計算の実行
    let c = a + b;

型は8種類あります。:

+---------------+---------------------------------------------------------------------------+
| 型            | 説明                                                                      |
+---------------+---------------------------------------------------------------------------+
| boolean       | 論理型は真偽の値を表します。値は ‘true’/‘false’のいずれかです。           |
+---------------+---------------------------------------------------------------------------+
| integer       | 整数値。値の範囲は環境に依存します。                                      |
+---------------+---------------------------------------------------------------------------+
| float/double  | 浮動小数。値の範囲は環境に依存します。                                    |
+---------------+---------------------------------------------------------------------------+
| string        | 文字列は1バイトを1文字とする文字の連続です。                              |
+---------------+---------------------------------------------------------------------------+
| array         | 配列は順序付けられたマップです。マップはキーに値が関連付けられる型です。  |
+---------------+---------------------------------------------------------------------------+
| object        | PHPのようなオブジェクトの抽象化                                           |
+---------------+---------------------------------------------------------------------------+
| resource      | 外部リソースへの参照を保持します。                                        |
+---------------+---------------------------------------------------------------------------+
| null          | 特別な NULL 値は、値のない変数を表します。                                |
+---------------+---------------------------------------------------------------------------+

これらの型に関する詳細は、`PHPのマニュアル`_ を参照してください。

Boolean
^^^^^^^
ブーリアン型は真偽値を表します。ここには「true」または「false」を指定できます。

.. code-block:: zephir

    var a = false, b = true;

Integer
^^^^^^^
整数値。整数値の範囲は環境に依存し、通常（32ビット符号有）の最大値は約2億になります。
64ビット環境での最大値は通常 9E18 (9×10の18乗)です。
PHPは符号なし整数をサポートしませんので、Zephir もこの制限を持ちます。:

.. code-block:: zephir

    var a = 5, b = 10050;

Integer 桁あふれ
^^^^^^^^^^^^^^^^
PHPとは対照的に、Zephir は自動的な桁あふれのチェックを行いません。
もしあなたが大きな数値を返すような処理を行う場合は、それを格納するためにC言語のように
'unsigned long' や 'float' に格納することができます。:

.. code-block:: zephir

    unsigned long my_number = 2147483648;

Float/Double
^^^^^^^^^^^^
浮動小数点数 ("floats", "double", "実数"として知られる)。
浮動小数点数は０以上の数字と、ピリオドに続く０桁以上の数字で表現されます。
float の大きさはプラットフォーム依存です。ただし、通常はおよそ 10 進数で 14 桁の精度があり、
最大値は ~1.8e308 (これは 64ビット IEEE フォーマットです) となります。

.. code-block:: zephir

    var number = 5.0, b = 0.014;

浮動小数点数の精度は有限です。
システムに依存しますが、Zephir は PHP と同様に通常 IEEE 754 倍精度フォーマットを使います。
この形式は、1.11e-16 のオーダーでの丸め処理で誤差が発生します。 

String
^^^^^^
文字列は文字の連続で、１文字は１バイトに相当します。
PHP と同様に Zephir では Unicode をネイティブにサポートしていないため、
256 個の異なる文字を使用可能です。

.. code-block:: zephir

    var today = "friday";

Zephir では、文字列の指定には(C言語のように)二重引用符（「"」）だけが使用でき、
単一引用符は文字用として予約されています。

文字列では以下のエスケープシーケンスを指定することができます。:

+---------------+---------------------------------------------------------------------------+
| 記述          | 意味                                                                      |
+---------------+---------------------------------------------------------------------------+
| \\t           | 水平タブ                                                                  |
+---------------+---------------------------------------------------------------------------+
| \\n           | ラインフィード                                                            |
+---------------+---------------------------------------------------------------------------+
| \\r           | キャリッジリターン                                                        |
+---------------+---------------------------------------------------------------------------+
| \\ \\         | バックスラッシュ                                                          |
+---------------+---------------------------------------------------------------------------+
| \\"           | 二重引用符                                                                |
+---------------+---------------------------------------------------------------------------+

.. code-block:: zephir

    var today = "\tfriday\n\r",
        tomorrow = "\tsaturday";

Zephir での文字列は、PHPのような変数のパースをサポートしていません。
代わりに文字列の連結を使用します。:

.. code-block:: zephir

    var name = "peter";

    echo "hello: " . name;

Arrays
^^^^^^
Zephir での配列の実装は、基本的にはPHPと同じで、さまざまな使い道にあわせて最適化された
順番付けられたマップです。
これは配列としてだけでなく、リスト (ベクター)、ハッシュテーブル (マップの実装の一つ)、
辞書、コレクション、スタック、キュー等として使用することが可能です。 
配列の値が他の配列やツリーや多次元配列になる事も可能です。

配列を定義するための構文は、PHPとは若干異なります。:

.. code-block:: zephir

    // 配列を定義するためには大括弧を使います
    let myArray = [1, 2, 3];

    // ハッシュキーを定義するためにはコロンを使用します
    let myHash = ["first": 1, "second": 2, "third": 3];

整数値をキーとして使うこともできます:

.. code-block:: zephir

    let myHash = [0: "first", 1: true, 2: null];
    let myHash = ["first": 7.0, "second": "some string", "third": false];

Objects
^^^^^^^
Zephir では PHPのオブジェクトをインスタンス化し、クラス定数を読んだり
メソッド操作したりすることができます。:

.. code-block:: zephir

    let myObject = new stdClass(),
        myObject->someProperty = "my value";

静的な型
--------
静的な型はC言語で利用可能ないくつかの変数の型を宣言し、開発者が使用することを可能にします。
変数は一度宣言されると動的型付けのように型を変更することはできません。
しかし、このような変数はコンパイラによって最適化率が向上します。
下記の型がサポートされています。:

+------------------+---------------------------------------------------------------------------------+
| 型               | 説明                                                                            |
+------------------+---------------------------------------------------------------------------------+
| boolean          | 論理型は真偽の値を表します。値は true / false のいずれかです。                  |
+------------------+---------------------------------------------------------------------------------+
| integer          | 符号付整数値。サイズは少なくとも16ビット。                                      |
+------------------+---------------------------------------------------------------------------------+
| unsigned integer | 符号なし整数値。サイズは少なくとも16ビット。                                    |
+------------------+---------------------------------------------------------------------------------+
| char             | 基本的な文字セットを含む、マシンのアドレス可能な最小単位。                      |
+------------------+---------------------------------------------------------------------------------+
| unsigned char    | char と同じサイズですが、符号なしであることが保証されています。                 |
+------------------+---------------------------------------------------------------------------------+
| long             | Long は符号付整数値。サイズは少なくとも32ビット。                               |
+------------------+---------------------------------------------------------------------------------+
| unsigned long    | long と同じですが符号なし。                                                     |
+------------------+---------------------------------------------------------------------------------+
| float/double     | 倍精度浮動小数点型。サイズは環境に依存します。                                  |
+------------------+---------------------------------------------------------------------------------+
| string           | string は文字の連続で、１文字は１バイトに相当します。                           |
+------------------+---------------------------------------------------------------------------------+
| array            | ハッシュマップやディクショナリ、コレクションやスタックとして使用できる構造体。  |
+------------------+---------------------------------------------------------------------------------+

Boolean
^^^^^^^
ブーリアン型は真偽値を表します。ここには「true」または「false」を指定できます。
動的宣言に対して、静的に宣言したブーリアン型はどんな値をセットしてもブーリアン(true か false)になります。:

.. code-block:: zephir

    boolean a;

    let a = true,
        a = 100, // 自動的に true にキャストされます
        a = null, // 自動的に false にキャストされます
        a = "hello"; // コンパイラが例外を出力します

Integer/Unsigned Integer
^^^^^^^^^^^^^^^^^^^^^^^^
整数値は動的宣言の integer に似ています。割り当てられた値は整数のままです。:

.. code-block:: zephir

    int a;

    let a = 50,
        a = -70,
        a = 100.25, // 自動的に 100 にキャストされます
        a = null, // 自動的に 0 にキャストされます
        a = false, // 自動的に 0 にキャストされます
        a = "hello"; // コンパイラが例外を出力します

符号なし整数値は整数に似ていますが、符号をもっていませんので、負の値をセットすることができません。:

.. code-block:: zephir

    let a = 50,
        a = -70, // 自動的に 70 にキャストされます
        a = 100.25, // 自動的に 100 にキャストされます
        a = null, // 自動的に 0 にキャストされます
        a = false, // 自動的に 0 にキャストされます
        a = "hello"; // コンパイラが例外を出力します

符号なし整数の最大値は通常の符号有整数の最大値の倍になりますので、
符号なし整数を符号有整数に代入すると、データの欠落が生じることがあります。:

.. code-block:: zephir

    uint a, int b;

    let a = 2147483648,
        b = a, // データ欠落の可能性

Long/Unsigned Long
^^^^^^^^^^^^^^^^^^
ロング変数は整数型の2倍の大きさですので、より大きな値をセットすることができ、
ロング変数に割り当てられた整数値は、地頭的にこの型にキャストされます。:

.. code-block:: zephir

    long a;

    let a = 50,
        a = -70,
        a = 100.25, // 自動的に 100 にキャストされます
        a = null, // 自動的に 0 にキャストされます
        a = false, // 自動的に 0 にキャストされます
        a = "hello"; // コンパイラが例外を出力します

符号なしロング型はロングに似ていますが、符号をもっていませんので
負の値をセットすることができません。:

.. code-block:: zephir

    let a = 50,
        a = -70, // 自動的に 70 にキャストされます
        a = 100.25, // 自動的に 100 にキャストされます
        a = null, // 自動的に 0 にキャストされます
        a = false, // 自動的に 0 にキャストされます
        a = "hello"; // コンパイラが例外を出力します

符号なしロング型は普通のロングの倍の大きさですので、符号なしロングを
ロングに代入すると、データの欠落が生じることがあります。:

.. code-block:: zephir

    ulong a, long b;

    let a = 4294967296,
        b = a, // データ欠落の可能性

Char/Unsigned Char
^^^^^^^^^^^^^^^^^^
文字変数は基本的な文字セットを格納することができる、マシンのアドレス可能な最小単位です。
全ての「char」変数は、文字列の各文字を表します。:

.. code-block:: zephir

    char ch, string name = "peter";

    let ch = name[2]; // 't' がセットされます
    let ch = 'Z'; // 文字定数は単一引用符で囲む必要があります。

String
^^^^^^
文字列は文字の連続で、１文字は１バイトに相当します。
PHP と同様に Unicode をネイティブにサポートしていないため、 256 個の異なる文字しか使用できません。

変数を文字列として宣言した場合には、その型は変更されません。:

.. code-block:: zephir

    string a;

    let a = "",
        a = "hello", // 文字列定数は二重引用符で囲む必要があります
        a = 'A', // 文字列の "A" に変換されます
        a = null; // 自動的に "" にキャストされます

.. _`PHPのマニュアル`: http://jp2.php.net/manual/ja/language.types.php
