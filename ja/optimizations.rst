最適化
======
Zephir のコードは非常に高水準なので、
C コンパイラはこのコードをさらに最適化する余地はないかもしれません。

Zephir は AOT コンパイラのおかげでコンパイル時にコードの最適化をおこない、
プログラムの実行速度を向上させたり、必要メモリ量を削減したりすることができます。

最適化を有効にするには、 -f から始まる引数を渡します。:

.. code-block:: bash

    zephir -fstatic-type-inference -flocal-context-pass

-fno- から始まる引数を渡すことで、警告を無効にすることができます。:

.. code-block:: bash

    zephir -fno-static-type-inference -fno-call-gatherer-pass

次の最適化指定がサポートされています。:

static-type-inference
^^^^^^^^^^^^^^^^^^^^^
このコンパイル パラメータは、コンパイラによる最適化で
静的/プリミティブ型に変換できる可能性のある動的変数を検索するために
非常に重要です。

次のコードは、動的変数を使用して数学的計算を行います。:

.. code-block:: zephir

	public function someCalculations(var a, var b)
	{
		var i = 0, t = 1;

		while i < 100 {
			if i % 3 == 0 {
				continue;
			}
			let t += (a - i), i++;
		}

		return i + b;
	}

変数 'a', 'b', 'i' は全て数学的演算で使用されているため、静的変数に変換することができます。
このパラメータによって、コンパイラは自動的にこのコードを次のように書き換えます。:

.. code-block:: zephir

	public function someCalculations(int a, int b)
	{
		int i = 0, t = 1;

		while i < 100 {
			if i % 3 == 0 {
				continue;
			}
			let t += (a - i), i++;
		}

		return i + b;
	}

このコンパイルパスを無効にすると、全ての変数は最適化なしで元々宣言されたままの型を維持します。

static-type-inference-second-pass
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
これによって、第１の型推論パスで集められたデータを基に処理を改善する
第２の型推論パスが有効になります。

local-context-pass
^^^^^^^^^^^^^^^^^^
このコンパイルパスはヒープに割り当てられる変数をスタックに移動します。
この最適化は、プログラムが必要とするメモリの参照回数を減らすことができます。

constant-folding
^^^^^^^^^^^^^^^^
定数の畳み込みは、コンパイル時に定数式を単純化するプロセスです。
この最適化を有効にすると、下記のコードが単純化されます。:

.. code-block:: zephir

	public function getValue()
	{
		return (86400 * 30) / 12;
	}

これが下記のように変換されます:

.. code-block:: zephir

	public function getValue()
	{
		return 216000;
	}

static-constant-class-folding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
この最適化は、コンパイル時にクラス定数の値を置換します。:

.. code-block:: zephir

	class MyClass
	{

		const SOME_CONSTANT = 100;

		public function getValue()
		{
			return self::SOME_CONSTANT;
		}
	}

これが下記のように変換されます:

.. code-block:: zephir

	class MyClass
	{

		const SOME_CONSTANT = 100;

		public function getValue()
		{
			return 100;
		}
	}

call-gatherer-pass
^^^^^^^^^^^^^^^^^^
このパスは同じメソッド内で関数またはメソッドが何回呼ばれたかをカウントします。
これによってコンパイラはインライン・キャッシュを導入し、
メソッドや関数の探索を避けることができます。:

.. code-block:: zephir

	class MyClass extends OtherClass
	{

		public function getValue()
		{
			this->someMethod();
            this->someMethod(); // このメソッドの呼び出しが速くなります
		}
	}
