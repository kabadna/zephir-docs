拡張機能のグローバル
--------------------
PHP 拡張機能では、拡張機能内のグローバルを定義する方法が提供されます。
グローバルの読み取り／書き込みは、他のどのグローバルメカニズム（静的メンバーなど）よりも
高速でなければなりません。
拡張機能のグローバルを使用して、あなたのライブラリの動作を変更するようなオプションを
設定することができます。

Zephir では、拡張機能のグローバルは int/bool/double/char などのシンプルなスカラー型に
限定されています。
string/arrays/objects/resources などの複雑な型はここでは許可されません。

config.json に次の構造体を追加することで、拡張機能のグローバルを有効にできます。:

.. code-block:: javascript

    {
        //...
        "globals": {
            "allow_some_feature": {
                "type": "bool",
                "default": true
            },
            "number_times": {
                "type": "int",
                "default": 10
            },
            "some_component.my_setting_1": {
                "type": "bool",
                "default": true
            },
            "some_component.my_setting_2": {
                "type": "int",
                "default": 100
            }
        }
    }

それぞれのグローバルは下記のような構造を持ちます。:

.. code-block:: javascript

    "<global-name>": {
        "type": "<some-valid-type>",
        "default": <some-compatible-default-value>
    }

複合的なグローバルは下記のような構造を持ちます。:

.. code-block:: javascript

    "<namespace>.<global-name>": {
        "type": "<some-valid-type>",
        "default": <some-compatible-default-value>
    }

どのメソッドの内部でも、組み込み関数 globals_get / globals_set を使って
拡張機能のグローバルを読み書きすることができます。：

.. code-block:: zephir

    globals_set("allow_some_feature", true);
    let someFeature = globals_get("allow_some_feature");

これらのグローバルを PHP から変更したい場合は、これを目的としたメソッドを
使うのはうまいやり方です。:

.. code-block:: zephir

    namespace Test;

    class MyOptions
    {

        public static function setOptions(array options)
        {
            boolean someOption, anotherOption;

            if fetch someOption, options["some_option"] {
                globals_set("some_option", someOption);
            }

            if fetch anotherOption, options["another_option"] {
                globals_set("another_option", anotherOption);
            }
        }
    }

globals_get / globals_set オプティマイザによって生成された C 言語のコードは
コンパイル時に解決される必要があるため、拡張機能のグローバルには動的にアクセスできません。:

.. code-block:: zephir

    let myOption = "someOption";

    // コンパイル時に例外が発生します
    let someOption = globals_get(myOption);
