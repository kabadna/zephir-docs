制御構造
========
Zephir は C言語や PHP などといった類似の言語に存在する制御構造の、
簡略化されたセットを実装しています。

条件文
------

If 文
^^^^^
'if' 文は、式が true と評価された時に処理を実行します。
中カッコは必須で、'if' 文はオプションの 'else' を持つ事ができます。
'if'/'else' はセットで連鎖させることができます。:

.. code-block:: zephir

    if false {
        echo "false?";
    } else {
        if true {
            echo "true!";
        } else {
            echo "neither true nor false";
        }
    }

'elseif' 句も使用可能です。:

.. code-block:: zephir

    if a > 100 {
        echo "to big";
    } elseif a < 0 {
        echo "to small";
    } elseif a == 50 {
        echo "perfect!";
    } else {
        echo "ok";
    }

評価する式のカッコはオプションです。:

.. code-block:: zephir

    if a < 0 { return -1; } else { if a > 0 { return 1; } }

Switch 文
^^^^^^^^^
'switch' は事前に定義されたリテラル値に対して式を評価して、対応する 'case' ブロックか
さもなくば 'default' ブロックを実行します。:

.. code-block:: zephir

    switch count(items) {

        case 1:
        case 3:
            echo "odd items";
            break;

        case 2:
        case 4:
            echo "even items";
            break;

        default:
            echo "unknown items";
    }

繰り返し
--------

While 文
^^^^^^^^
'while' は、条件が true と評価される限り繰り返し処理をおこないます。:

.. code-block:: zephir

    let counter = 5;
    while counter {
        let counter -= 1;
    }

Loop 文
^^^^^^^
'while' に加えて、'loop' は永久ループを作成するために使うことができます。:

.. code-block:: zephir

    let n = 40;
    loop {
        let n -= 2;
        if n % 5 == 0 { break; }
        echo x, "\n";
    }

For 文
^^^^^^
'for' は配列や文字列を横断することを可能にする制御構造です。:

.. code-block:: zephir

    for item in ["a", "b", "c", "d"] {
        echo item, "\n";
    }

ハッシュのキーは次のようにして得ることができます。:

.. code-block:: zephir

    let items = ["a": 1, "b": 2, "c": 3, "d": 4];

    for key, value in items {
        echo key, " ", value, "\n";
    }

'for' ループは、逆の順序で配列や文字列を横断するように指示することができます。:

.. code-block:: zephir

    let items = [1, 2, 3, 4, 5];

    for value in reverse items {
        echo value, "\n";
    }

'for' は文字列変数を横断するために使うことができます。:

.. code-block:: zephir

    string language = "zephir"; char ch;

    for ch in language {
        echo "[", ch ,"]";
    }

逆順を指定すると:

.. code-block:: zephir

    string language = "zephir"; char ch;

    for ch in reverse language {
        echo "[", ch ,"]";
    }

整数値の範囲を横断する標準的な 'for' は下記のように書く事ができます。:

.. code-block:: zephir

    for i in range(1, 10) {
        echo i, "\n";
    }

変数の未使用の警告を避けるために、 'for' 文ではプレースホルダ "_" を使用して
変数名を置き換えることによって、無名変数にすることができます。:

.. code-block:: zephir

    // キーは使うが値は使わない
    for key, _ in data {
        echo key, "\n";
    }

Break 文
^^^^^^^^
'break' は、現在の 'while', 'for', 'loop' 文の実行を終了します。:

.. code-block:: zephir

    for item in ["a", "b", "c", "d"] {
        if item == "c" {
            break; // for 文を終了する
        }
        echo item, "\n";
    }

Continue 文
^^^^^^^^^^^
'continue' はループ構文内で使用され、現在のループ内処理の残りの部分をスキップし、
また条件評価してから次の繰り返し処理を実行します。

.. code-block:: zephir

    let a = 5;
    while a > 0 {
        let a--;
        if a == 3 {
            continue;
        }
        echo a, "\n";
    }

Require
-------
'require' 文は指定された PHP ファイルを動的に読み込んで評価します。
Zephir によって読み込まれたファイルは、Zend Engine によって通常の PHP ファイル
として解釈されることに注意してください。
'require' は Zephir のファイルを実行時に読み込むことはできません。

.. code-block:: zephir

    if file_exists(path) {
        require path;
    }

Let
---
'Let' 文は変数、プロパティ、配列を変化させるのに使います。
変数はデフォルトでは不変であり、この命令によって可変になります。:

.. code-block:: zephir

    let name = "Tony";           // 単純な変数
    let this->name = "Tony";     // オブジェクトのプロパティ
    let data["name"] = "Tony";   // 配列の要素
    let self::_name = "Tony";    // 静的(static)プロパティ

また、この命令で変数値の加算/減算を行う事もできます。:

.. code-block:: zephir

    let number++;           // 変数値の加算
    let number--;           // 変数値の減算
    let this->number++;     // オブジェクトプロパティ値の加算
    let this->number--;     // オブジェクトプロパティ値の減算

複数の変更も、１つの 'let' 文で行う事ができます。:

.. code-block:: zephir

    let price = 1.00, realPrice = price, status = false;
