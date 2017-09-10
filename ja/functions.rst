関数の呼び出し
--------------
PHP にはあなたの拡張機能内で使用できる豊富な関数ライブラリがあります。
PHP の関数を呼び出すには、Zephir コード内で普通に関数を呼び出すだけです。:

.. code-block:: zephir

    namespace MyLibrary;

    class Encoder
    {

        public function encode(var text)
        {
            if strlen(text) != 0 {
                return base64_encode(text);
            }
            return false;
        }
    }

PHP の内部関数ではなくても、PHP のユーザー領域で定義されている関数を呼び出すこともできます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Encoder
    {

        public function encode(var text)
        {
            if strlen(text) != 0 {
                if function_exists("my_custom_encoder") {
                    return my_custom_encoder(text);
                } else {
                    return base64_encode(text);
                }
            }
            return false;
        }
    }

全ての PHP 関数は、動的変数だけを受け取り／返すことに注意してください。
もしあなたが静的変数をパラメータとして引き渡したら、関数呼び出しのための
ブリッジとして一時的な動的変数が自動的に使用されます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Encoder
    {

        public function encode(string text)
        {
            if strlen(text) != 0 {
                // 静的型付けされた 'text' をパラメータとして渡すために
                // 暗黙の動的変数が作成されます。
                return base64_encode(text);
            }
            return false;
        }
    }

同様に、関数は適切なキャストなしには静的変数に直接割り当てられないような、
動的な型の値を返します。:

.. code-block:: zephir

    namespace MyLibrary;

    class Encoder
    {

        public function encode(string text)
        {
            string encoded = "";

            if strlen(text) != 0 {
                let encoded = (string) base64_encode(text);
                return "(" . encoded . ")";
            }
            return false;
        }
    }

Zephir では、次のように動的に関数を呼び出すこともできます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Encoder
    {

        public function encode(var callback, string text)
        {
            return {callback}(text);
        }
    }
