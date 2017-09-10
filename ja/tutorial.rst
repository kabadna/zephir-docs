チュートリアル
==============
Zephirと本書は、より簡単にC言語の拡張モジュールを作成したいPHP開発者のためになることを
意図しています。

我々はあなたが１つ以上の他のプログラム言語の経験があると想定しており、PHPやC言語や
Javascriptなどの言語と比較しながら説明します。
もしあなたがこれらの言語のいずれかをご存知なら、我々は多くの新たな機能や相違点と同様に
Zephirの機能の類似点についても論じることになります。

インストールチェック
--------------------
もしZephirのインストールに成功したなら、コンソールから下記のコマンドが実行できます。:

.. code-block:: bash

    $ zephir help

もしうまくいっていれば、画面に下記のようなヘルプが表示されるでしょう。:

.. code-block:: bash

     _____              __    _
    /__  /  ___  ____  / /_  (_)____
      / /  / _ \/ __ \/ __ \/ / ___/
     / /__/  __/ /_/ / / / / / /
    /____/\___/ .___/_/ /_/_/_/
             /_/

    Zephir version 0.9.4a-dev

    Usage:
        command [options]

    Available commands:
        stubs               Generates extension PHP stubs
        install             Installs the extension (requires root password)
        version             Shows the Zephir version
        compile             Compile a Zephir extension
        api [--theme-path=/path][--output-directory=/path][--theme-options={json}|/path]Generates a HTML API
        init [namespace]    Initializes a Zephir extension
        fullclean           Cleans the generated object files in compilation
        builddev            Generate/Compile/Install a Zephir extension in development mode
        clean               Cleans the generated object files in compilation
        generate            Generates C code from the Zephir code
        help                Displays this help
        build               Generate/Compile/Install a Zephir extension

    Options:
        -f([a-z0-9\-]+)     Enables compiler optimizations
        -fno-([a-z0-9\-]+)  Disables compiler optimizations
        -w([a-z0-9\-]+)     Turns a warning on
        -W([a-z0-9\-]+)     Turns a warning off

拡張モジュールのスケルトン
--------------------------
我々が最初にしなければならないことは、拡張モジュールが動くための基本構成を持った
スケルトンを生成することです。
今回は "utils" という名前の拡張モジュールを作りましょう。:

.. code-block:: bash

    $ zephir init utils

これで現在のディレクトリに "utils" という名前のディレクトリが作られます。:

.. code-block:: bash

    utils/
       ext/
       utils/

ディレクトリ「ext/」には、拡張モジュールを作るためにコンパイラが使うコードを含んでいます。
もう一方の「utils」ディレクトリは拡張モジュールと同じ名前です。
私たちはZephirのコードをここに書きます。

私たちがコードをコンパイルするためには、「utils」ディレクトリに移動する必要があります。

.. code-block:: bash

    $ cd utils
    $ ls
    ext/ utils/ config.json

このディレクトリには「config.json」という名前のファイルがあります。
このファイルには私たちがZephirや拡張モジュールの動作を変更するための設定情報が含まれます。

最初のクラスの作成
------------------
Zephirはオブジェクト指向拡張モジュールを作るよう設計されています。
効率的に開発を始めるために、私たちは拡張モジュールに最初のクラスを作成する必要があります。

多くの言語やツールと同様、まずやってみたいことはZephirで「hello world」を作って、
全て申し分ないことを確認することです。
それでは最初のクラスは、「hello world」を表示するメソッドを持った、「Utils\\Greeting」と
しましょう。

このクラスのコードは「utils/utils/greeting.zep」に置かなければなりません。:

.. code-block:: zephir

    namespace Utils;

    class Greeting
    {

        public static function say()
        {
            echo "hello world!";
        }

    }

それでは、Zephirにコンパイルして拡張モジュールを作成するよう指示しましょう。:

.. code-block:: bash

    $ zephir build

まず初回だけは、このクラスを拡張モジュールとして出力するために必要なコードや
設定といった多くの内部コマンドを生成します。うまくいけばあなたは最後に下記のような
メッセージを見ることになります。:

.. code-block:: php

    ...
    Extension installed!
    Add extension=utils.so to your php.ini
    Don't forget to restart your web server

上の段階で、拡張モジュールをインストールするためにあなたは root パスワードを
入力する必要があるかもしれません。
最終的に、拡張モジュールがPHPに読み込まれるためには php.ini に追記する必要が
あります。これは「 extension=utils.so 」と追記します。

最初のテスト
------------
あなたの php.ini に拡張モジュールが追加された今、拡張モジュールが適切に
ロードされているかどうかを下記のコマンドを実行して確認してください。:

.. code-block:: bash

    $ php -m
    [PHP Modules]
    Core
    date
    libxml
    pcre
    Reflection
    session
    SPL
    standard
    tokenizer
    utils
    xdebug
    xml

適切にロードされた拡張モジュールの一覧の中に、"utils" が表示されているはずです。
それでは PHP から呼び出して“hello world”を表示してみましょう。
これを実現するために、あなたは先ほど作った static メソッドを呼び出すシンプルな
PHPファイルを作る事ができます。:

.. code-block:: php

    <?php

    echo Utils\Greeting::say(), "\n";

おめでとうございます！あなたはPHPで初めての拡張モジュールを実行したのです。

実用的なクラス
--------------
"Hello World"クラスは環境さえ正しければ正しく動きましたが、
今度はもっと実用的なクラスを作ってみましょう。

我々が拡張モジュールに追加する最初の実用的なクラスは、
ユーザーにフィルタリング機能を提供します。
このクラスは "Utils\\Filter" と呼ばれ、ソースコードは
"utils/utils/filter.zep" に配置します。

このクラスの基本スケルトンは下記の通りです。:

.. code-block:: zephir

    namespace Utils;

    class Filter
    {

    }

このクラスは、ユーザーが文字列から不要な文字を取り除くのを助けるフィルタリング
メソッドを含んでいます。
最初のメソッドは "alpha" と呼ばれ、その目的は英字だけを取り出すことです。
まずは文字列を取り出して全ての文字を標準出力に表示しましょう。:

.. code-block:: zephir

    namespace Utils;

    class Filter
    {

        public function alpha(string str)
        {
            char ch;

            for ch in str {
                echo ch, "\n";
            }
        }
    }

このメソッドを呼び出すには:

.. code-block:: php

    <?php

    $f = new Utils\Filter();
    $f->alpha("hello");

するとこのように表示されます:

.. code-block:: bash

    h
    e
    l
    l
    o

文字列中の全ての文字を単純にチェックして、特定の文字をフィルタリングして
別の文字列を作る事ができます。:

.. code-block:: zephir

    class Filter
    {

        public function alpha(string str) -> string
        {
            char ch; string filtered = "";

            for ch in str {
                if (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z') {
                    let filtered .= ch;
                }
            }

            return filtered;
        }
    }

完全なメソッドは下記のようにテストすることができます。:

.. code-block:: php

    <?php

    $f = new Utils\Filter();
    echo $f->alpha("!he#02l3'121lo."); // prints "hello"

下記のスクリーンキャストでは、今回のチュートリアルで説明した作成方法を見ることが
できます。:

.. raw:: html

   <div align="center"><iframe src="//player.vimeo.com/video/84180223" width="500" height="313" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe></div>

結論
----
これはとてもシンプルなチュートリアルで、ご覧のとおり Zephir を使った拡張モジュールの
作成を始めるのは非常に簡単です。
私たちは あなたが Zephir によって提供されるさらなる機能を発見できるように、マニュアルを
読み続けることをお勧めします！
