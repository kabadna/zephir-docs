静的解析 (Static Analysis)
==========================
Zephir のコンパイラは、コンパイルされたコードの静的解析を提供します。
この機能の背後にあるアイディアは、開発者が潜在的な問題を見つけて
予期せぬ挙動を回避するのを助けることです。

条件付き未割当変数
^^^^^^^^^^^^^^^^^^
代入の静的解析は、変数が代入される前に使用されているかどうかを調べます。:

.. code-block:: zephir

	class Utils
	{
 		public function someMethod(b)
 		{
   			string a; char c;

			if b == 10 {
				let a = "hello";
			}

			// この時点で「a」は初期化されていない可能性があります
			for c in a {
				echo c, PHP_EOL;
			}
		}
	}

上記の例では一般的な状況を示しています。
変数“a”は、“b”が 10 の場合にのみ割り当てられ、
変数の値を使おうにも初期化されていない場合があります。
Zephir はこれを検知し、変数を空文字列で自動的に初期化し、
開発者への警告メッセージを生成します。:

.. code-block:: html

	Warning: Variable 'a' was assigned for the first time in conditional branch,
 	consider initialize it in its declaration in
	/home/scott/test/test/utils.zep on 21 [conditional-initialization]

		for c in a {

このようなエラーを見つけるのには時に手間がかかりますが、
静的解析はプログラマーが事前にバグを見つけるのを助けます。

到達不能コードの削除
^^^^^^^^^^^^^^^^^^^^
Zephir はコード内の到達不能な分岐について開発者に通知し、
到達不能コードの削除を行います。
ここでいう削除とは、生成されるバイナリから実行されないコードを削除することを意味しています。:

.. code-block:: zephir

	class Utils
	{
 		public function someMethod(b)
 		{
   			if false {
				// これは実行されません
				echo "hello";
			}
		}
	}
