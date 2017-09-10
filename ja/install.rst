インストール
============
Zephirのインストールは下記の手順でおこないます。:

前提条件
--------

Zephirを使ってPHP拡張モジュールを作るためには、下記のものが必要です。:

* gcc >= 4.x/clang >= 3.x
* re2c 0.13 以上
* gnu make 3.81 以上
* autoconf 2.31 以上
* automake 1.14 以上
* libpcre3
* PHP開発ヘッダとツール(php-devel)

Ubuntuを使用している場合は、必要なパッケージをこのようにインストールすることができます。:

.. code-block:: bash

	$ sudo apt-get update
	$ sudo apt-get install git gcc make re2c php7.0 php7.0-json php7.0-dev libpcre3-dev

ZephirはPHPで書かれていますので、あなたはPHPの最新バージョンをインストールしておく必要があり、
それはあなたのコンソールで利用可能でなければなりません。:

.. code-block:: bash

	$ php -v
	PHP 7.0.8 (cli) (built: Jun 26 2016 00:59:31) ( NTS )
	Copyright (c) 1997-2016 The PHP Group
	Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
    		with Zend OPcache v7.0.8, Copyright (c) 1999-2016, by Zend Technologies

また、PHPと一緒にPHPの開発ライブラリがインストールされていることを確認してください。:

.. code-block:: bash

	$ phpize -v
	Configuring for:
	PHP Api Version:         20151012
	Zend Module Api No:      20151012
	Zend Extension Api No:   320151012

あなたは必ずしも上記の表示を確認する必要はありませんが、Zephirでの開発にあたって
これらのコマンドが使えるようになっている事が重要です。

Zephirのインストール
--------------------

Zephirコンパイラは現在、Githubからクローンを作ります。:

.. code-block:: bash

	$ git clone https://github.com/phalcon/zephir

Zephirインストーラを実行します。（このコンパイルはパーサを作成します）:

.. code-block:: bash

	$ cd zephir
	$ ./install -c

インストール確認
----------------
Zephirが任意のディレクトリから実行できることを確認します。:

.. code-block:: bash

	$ zephir help
