コンパイラ警告
==============
コンパイラは、コードを改善したり潜在的なエラーを回避できる状況が発生した時に、警告を発します。

警告はコマンドラインパラメータで有効にすることも、config.json に追記して永続的に
有効または無効にすることもできます。:

警告を有効にするには、先頭に「-w」を付けた名前をパラメータとして渡します。:

.. code-block:: bash

    zephir -wunused-variable -wnonexistent-function

警告を無効にするには、先頭に「-W」を付けた名前をパラメータとして渡します。:

.. code-block:: bash

    zephir -Wunused-variable -Wnonexistent-function

下記の警告がサポートされます。:

unused-variable
^^^^^^^^^^^^^^^
宣言された変数がメソッド内で使用されていない場合に発生します。
この警告はデフォルトで有効になっています。

.. code-block:: zephir

    public function some()
    {
        var e; // 宣言されたが使われていない

        return false;
    }

unused-variable-external
^^^^^^^^^^^^^^^^^^^^^^^^
宣言されたパラメータがメソッド内で使用されていない場合に発生します。

.. code-block:: zephir

    public function sum(a, b, c) // c は使われていない
    {
        return a + b;
    }

possible-wrong-parameter-undefined
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
間違ってパラメータの型でメソッドが呼ばれた時に発生します。:

.. code-block:: zephir

    public function some()
    {
        return this->sum("a string", "another");  // 間違ったパラメータが引き継がれた
    }

    public function sum(int a, int b)
    {
        return a + b;
    }

nonexistent-function
^^^^^^^^^^^^^^^^^^^^
コンパイル時に存在しない関数が呼び出された時に発生します。:

.. code-block:: zephir

    public function some()
    {
        someFunction(); // この関数は存在しない
    }

nonexistent-class
^^^^^^^^^^^^^^^^^
コンパイル時に存在しないクラスが使われた時に発生します。:

.. code-block:: zephir

    public function some()
    {
        var a;

        let a = new \MyClass(); // MyClass は存在しない
    }

non-valid-isset
^^^^^^^^^^^^^^^
配列でもオブジェクトでもない値に対して 'isset' 操作が行われていることを
コンパイラが検出した時に発生します。:

.. code-block:: zephir

    public function some()
    {
        var b = 1.2;
        return isset b[0]; // 数値型の変数 'b' を配列として使用した
    }

non-array-update
^^^^^^^^^^^^^^^^
配列ではない値に対して配列の更新が行われていることをコンパイラが検出した時に発生します。:

.. code-block:: zephir

    public function some()
    {
        var b = 1.2;
        let b[0] = true; // 変数 'b' は配列として使えない
    }

non-valid-objectupdate
^^^^^^^^^^^^^^^^^^^^^^
非オブジェクトに対してオブジェクト更新操作が行われていることをコンパイラが検出した時に発生します。:

.. code-block:: zephir

    public function some()
    {
        var b = 1.2;
        let b->name = true; // 変数 'b' はオブジェクトではない
    }

non-valid-fetch
^^^^^^^^^^^^^^^
配列でもオブジェクトでもない値に対して 'fetch' 操作が行われていることを
コンパイラが検出した時に発生します。:

.. code-block:: zephir

    public function some()
    {
        var b = 1.2, a;
        fetch a, b[0]; // 数値型の変数 'b' が配列として使われている
    }

invalid-array-index
^^^^^^^^^^^^^^^^^^^
配列の添え字に無効な値が使われていることをコンパイラが検出した時に発生します。:

.. code-block:: zephir

    public function some(var a)
    {
        var b = [];
        let a[b] = true;
    }
