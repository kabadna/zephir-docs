設定ファイル
============
全ての Zephir による拡張機能は config.json という設定ファイルを持っています。
このファイルはあなたが拡張機能を作ったりビルドしたりするたびに Zephir に読み込まれ、
開発者は拡張機能の動作やコンパイラの動作を変更することができます。

このファイルでの設定は `JSON <http://ja.wikipedia.org/wiki/JSON>`_ 形式で記述します。:

.. code-block:: json

    {
        "namespace": "test",
        "name": "Test Extension",
        "description": "My amazing extension",
        "author": "Tony Hawk",
        "version": "1.2.0"
    }

このファイルに定義された設定は、Zephir の初期設定よりも優先されます。

次の設定がサポートされます。:

namespace
^^^^^^^^^
拡張機能の名前空間。これは正規表現で言えば「 :code:`[a-zA-Z0-9_]+` 」で表せるシンプルな
識別子でなければなりません。:

.. code-block:: json

    {
        "namespace": "test"
    }

name
^^^^
拡張機能名。アスキー文字のみを含むことができます。:

.. code-block:: json

    {
        "namespace": "test"
    }

description
^^^^^^^^^^^
拡張機能の説明。あなたの拡張機能を説明する文字列。:

.. code-block:: json

    {
        "description": "My amazing extension"
    }

author
^^^^^^
拡張機能を開発した会社、開発者、機関など。:

.. code-block:: json

    {
        "author": "Tony Hawk"
    }

version
^^^^^^^
拡張機能のバージョン。正規表現「 [0-9]+\.[0-9]+\.[0-9]+ 」の書式に従う必要があります。:

.. code-block:: json

    {
        "version": "1.2.0"
    }

warnings
^^^^^^^^
このプロジェクトでコンパイラ警告を有効にするか無効にするか。:

.. code-block:: json

    {
        "warnings": {
            "unused-variable": true,
            "unused-variable-external": false,
            "possible-wrong-parameter": true,
            "possible-wrong-parameter-undefined": false,
            "nonexistent-function": true,
            "nonexistent-class": true
        }
    }

optimizations
^^^^^^^^^^^^^
このプロジェクトでコンパイラによる最適化を有効にするか無効にするか。:

.. code-block:: json

    {
        "optimizations": {
            "static-type-inference": true,
            "static-type-inference-second-pass": true,
            "local-context-pass": false
        }
    }

globals
^^^^^^^
拡張機能でグローバル変数が利用可能です。
詳細については :doc:`グローバル変数 <globals>` の章をご覧ください。

.. code-block:: json

    {
        "globals": {
            "my_setting_1": {
                "type": "bool",
                "default": true
            },
            "my_setting_2": {
                "type": "int",
                "default": 10
            }
        }
    }

info
^^^^
phpinfo() セクションです。詳細は :doc:`phpinfo() <phpinfo>` の章をご覧ください。

.. code-block:: json

    {
        "info": [
            {
                "header": ["Directive", "Value"],
                "rows": [
                    ["setting1", "value1"],
                    ["setting2", "value2"]
                ]
            }
        ]
    }

extra-cflags
^^^^^^^^^^^^
コンパイル時に追加したいコンパイルオプションを指定します。:

.. code-block:: json

    {
        "extra-cflags": "-I/usr/local/Cellar/libevent/2.0.21_1/include"
    }

extra-libs
^^^^^^^^^^
コンパイル時に追加したいライブラリを指定します。:

.. code-block:: json

    {
        "extra-libs": "-L/usr/local/Cellar/libevent/2.0.21_1/lib -levent"
    }

extra-sources
^^^^^^^^^^^^^
コンパイル時に追加したい外部プログラムを指定します。:

.. code-block:: json

    {
        "extra-sources": ["utils/pi.c"]
    }

ディレクトリはあなたのプロジェクトの `ext` フォルダからの相対パスとして検索します。

optimizer-dirs
^^^^^^^^^^^^^^
あなたの自作のオプティマイザのあるディレクトリ。:

.. code-block:: json

    {
        "optimizer-dirs": ["optimizer-dirs"]
    }

ディレクトリはあなたのプロジェクトのルートフォルダからの相対パスとして検索します。

package-dependencies
^^^^^^^^^^^^^^^^^^^^
ライブラリの依存関係を宣言します。
（バージョンのチェックは :code:`pkg-config` によって行われます ）

.. code-block:: json

    {
        "package-dependencies": {
            "openssl": "*",
            "libpng": ">= 0.1.0",
            "protobuf": "<= 2.6.1"
        }
    }

バージョンの演算子は  :code:`=`、 :code:`>=`、 :code:`<=`、 :code:`*` をサポートしています。
