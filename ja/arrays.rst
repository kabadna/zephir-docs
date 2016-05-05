配列
====
Zephir での配列操作は、PHP の `配列`_ を使用する方法を提供します。
配列は `ハッシュテーブル`_ の実装です。

配列変数の宣言
--------------
配列変数はキーワード「var」または「array」を使用して宣言することができます。:

.. code-block:: zephir

	var a = []; // 配列変数。型を変更することができます。
	array b = []; // 配列変数。実行時に型を変更することはできません。

配列を作る
----------
配列は要素を角カッコで囲んで作成します。:

.. code-block:: zephir

	// 空の配列を作る
	let elements = [];

	// 要素付きの配列を作る
	let elements = [1, 3, 4];

	// 異なる型の要素を持った配列を作る
	let elements = ["first", 2, true];

	// 多次元配列を作る
	let elements = [[0, 1], [4, 5], [2, 3]];

PHP のように、ハッシュや辞書がサポートされます。:

.. code-block:: zephir

	// 文字列キーのハッシュを作る
	let elements = ["foo": "bar", "bar": "foo"];

	// 数字がキーのハッシュを作る
	let elements = [4: "bar", 8: "foo"];

	// た文字列キーと数字キーが混在するハッシュを作る
	let elements = [4: "bar", "foo": 8];

配列を更新する
--------------
配列は PHP と同じように角カッコを使って更新します。:

.. code-block:: zephir

	// 文字列キーで配列を更新する
	let elements["foo"] = "bar";

	// 数字キーで配列を更新する
	let elements[0] = "bar";

	// 多次元配列を更新する
	let elements[0]["foo"] = "bar";
	let elements["foo"][0] = "bar";

要素を追加する
--------------
次のように配列の最後に要素を追加することができます。:

.. code-block:: zephir

	// 配列に要素を追加する
	let elements[] = "bar";

配列の要素を読み込む
--------------------
次のように配列の要素を読み込むことができます。:

.. code-block:: zephir

	// 文字列キー "foo" を使って要素を取り出す
	let foo = elements["foo"];

	// 数字キー 0 を使って要素を取り出す
	let foo = elements[0];

.. _配列: http://www.php.net/manual/ja/language.types.array.php
.. _`ハッシュテーブル`: http://ja.wikipedia.org/wiki/%E3%83%8F%E3%83%83%E3%82%B7%E3%83%A5%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB
