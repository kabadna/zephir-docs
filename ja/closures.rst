クロージャー
------------
Zephir ではクロージャーや匿名関数を使用できます。これらは PHP と互換性があり、
PHP のユーザー領域に返すことができます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Functional
    {

        public function map(array! data)
        {
            return function(number) {
                return number * number;
            };
        }
    }

Zephir 内で直接実行し、パラメータとして他の関数やメソッドを引き継ぐこともできます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Functional
    {

        public function map(array! data)
        {
            return data->map(function(number) {
                return number * number;
            });
        }
    }

クロージャを定義するための短い構文が使用できます。:

.. code-block:: zephir

    namespace MyLibrary;

    class Functional
    {

        public function map(array! data)
        {
            return data->map( number => number * number );
        }
    }
