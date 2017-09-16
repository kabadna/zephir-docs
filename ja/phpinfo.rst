Phpinfo() セクション
====================
ほとんどの拡張機能と同様に、Zephir の拡張機能は phpinfo() で情報を表示することができます。
これらの情報は普通、ディレクティブや環境データに関するものです。

デフォルトでは、全ての Zephir 拡張機能は自動的に phpinfo() に
拡張機能のバージョンを表示する基本的なテーブルが追加されます。

config.json ファイルに次の設定を追加することで、さらに多くのディレクティブを追加できます。:

.. code-block:: javascript

    "info": [
        {
            "header": ["Directive", "Value"],
            "rows": [
                ["setting1", "value1"],
                ["setting2", "value2"]
            ]
        },
        {
            "header": ["Directive", "Value"],
            "rows": [
                ["setting3", "value3"],
                ["setting4", "value4"]
            ]
        }
    ]

これらの情報は下記のように表示されます。:

.. figure:: ../_static/img/info.png
    :align: center
