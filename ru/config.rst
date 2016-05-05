Конфигурационный файл
=====================
Every Zephir extension has a configuration file called config.json. This file is read by Zephir every time you build or generate the extension and
it allows the developer to modify the extension or compiler behavior.

This file use `JSON <http://en.wikipedia.org/wiki/JSON>`_ as configuration format:

.. code-block:: json

    {
        "namespace": "test",
        "name": "Test Extension",
        "description": "My amazing extension",
        "author": "Tony Hawk",
        "version": "1.2.0"
    }

Settings defined in this file override any factory default setting provided by Zephir.

The following settings are supported:

namespace
^^^^^^^^^
The namespace of the extension, it must be a simple identifier respecting the regular expression: [a-zA-Z0-9\_]+:

.. code-block:: json

    {
        "namespace": "test"
    }

name
^^^^
Extension name, can only contain ascii characters:

.. code-block:: json

    {
        "namespace": "test"
    }

description
^^^^^^^^^^^
Extension description, any text describing your extension:

.. code-block:: json

    {
        "description": "My amazing extension"
    }

author
^^^^^^
Company, developer, institution, etc. that have developed the extension:

.. code-block:: json

    {
        "author": "Tony Hawk"
    }

version
^^^^^^^
Extension version, must follow the regular expression: [0-9]+\.[0-9]+\.[0-9]+:

.. code-block:: json

    {
        "version": "1.2.0"
    }

warnings
^^^^^^^^
Compiler warnings enabled or disabled in the current project:

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
Compiler optimizations enabled or disabled in the current project:

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
Extension globals available. Check the :doc:`extension globals <globals>` chapter for more information.

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
phpinfo() sections. Check the :doc:`phpinfo() <phpinfo>` chapter for more information.

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
Any additional flags you want to add to the compilation process:

.. code-block:: json

    {
        "extra-cflags": "-I/usr/local/Cellar/libevent/2.0.21_1/include"
    }

extra-libs
^^^^^^^^^^
Any additional libraries you want to add to the compilation process:

.. code-block:: json

    {
        "extra-libs": "-L/usr/local/Cellar/libevent/2.0.21_1/lib -levent"
    }

package-dependencies
^^^^^^^^^^^^^^^^^^^^
Declare library dependencies (version check by :code:`pkg-config`)

.. code-block:: json

    {
        "package-dependencies": {
            "openssl": "*",
            "libpng": ">= 0.1.0",
            "protobuf": "<= 2.6.1"
        }
    }

Version operator supports :code:`=`, :code:`>=`, :code:`<=`, and :code:`*`
