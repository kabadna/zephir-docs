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
	$ sudo apt-get install git gcc make re2c php5 php5-json php5-dev libpcre3-dev

ZephirはPHPで書かれていますので、あなたはPHPの最新バージョンをインストールしておく必要があり、
それはあなたのコンソールで利用可能でなければなりません。:

.. code-block:: bash

	$ php -v
	PHP 5.6.5 (cli) (built: Jan 24 2015 20:04:31)
	Copyright (c) 1997-2014 The PHP Group
	Zend Engine v2.6.0, Copyright (c) 1998-2014 Zend Technologies
    	with Zend OPcache v7.0.4-dev, Copyright (c) 1999-2014, by Zend Technologies

また、PHPと一緒にPHPの開発ライブラリがインストールされていることを確認してください。:

.. code-block:: bash

	$ phpize -v
	Configuring for:
	PHP Api Version:         20131106
	Zend Module Api No:      20131226
	Zend Extension Api No:   220131226

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
