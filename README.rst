==============
Cerberus-docs
==============
|forthebadge made-with-python|

.. |forthebadge made-with-python| image:: http://ForTheBadge.com/images/badges/made-with-python.svg
   :target: https://www.python.org/

|Run tests|

.. |Run tests| image:: https://github.com/noda/cerberus-docs/actions/workflows/run-tests.yml/badge.svg

|Deploy docs to Github Pages|

.. |Deploy docs to Github Pages| image:: https://github.com/noda/cerberus-docs/actions/workflows/deploy-docs.yml/badge.svg

|Lint code|

.. |Lint code| image:: https://github.com/noda/cerberus-docs/actions/workflows/lint.yml/badge.svg

About
=====
Library for generating documentation for `Cerberus <https://docs.python-cerberus.org/en/stable/>`_ schemas.

You can find the latest, most up to date, documentation at our `doc site <https://noda.github.io/cerberus-docs/>`_.

Getting Started
================
Assuming that you have Python 3.7 or higher and ``virtualenv`` installed, set up your environment and install the required dependencies like this:

.. code-block:: sh

    $ git clone https://github.com/noda/cerberus-docs.git
    $ cd cerberus-docs
    $ virtualenv venv
    ...
    $ . venv/bin/activate
    $ python -m pip install -r requirements/requirements.txt


Using Cerberus-docs
===================
In order for cerberus-docs to be able to find the Cerberus schemas, every schema that should be documented must be wrapped by the CerberusSchema class.

To retrieve the original dict from the class, use the ``.to_schema()`` method.

.. code-block:: python

    from cerberus_docs import CerberusSchema

    class Foo:
        cerberus_schema = CerberusSchema({'name': {'type': 'string'}})

        def __init__(self):
            v = Validator(self.cerberus_schema.to_schema())

To run cerberus-docs use the command ``cerberus-docs`` and specify:

``--source-dir``: The directory with the source code. Cerberus-docs will find every python module in the directory tree and look for CerberusSchema classes. Defaults to the current working directory.

``--build-dir``: The directory where the generated documentation files will be saved. Defaults to the current working directory.

Example:

.. code-block:: sh

    $ cerberus-docs --source-dir ./myProject --build-dir ./myDocs
    Docs successfully generated.

Features
========

Current supported cerberus validation rules
-------------------------------------------

* required
* type
* allowed
* regex
* default
* schema
* meta

Comments in schemas
--------------------

Users can use the Cerberus meta validation rule with the 'description' key in order for cerberus-docs to generate the value as a comment in the documentation.

.. code-block:: py

    cerberus_schema = CerberusSchema({
        'my_field': {
            'type': 'string',
            'meta': {'description': 'Example comment'},
        },
    })

Generated input example
------------------------

Cerberus-docs generates an input example in yaml that will be appended to the documentation for every cerberus schemas .

Schema:

.. code-block:: py

    cerberus_schema = CerberusSchema{
        'name': {
            'required': True,
            'type': 'string',
            'meta': {'description': 'The first name of the person'},
        },
        'age': {
            'required': True,
            'type': 'integer',
        },
        'address': {
            'type': 'dict',
            'required': False,
            'schema': {
                'street': {
                    'type': 'string',
                    'required': True,
                },
                'zip_code': {
                    'type': 'string',
                    'required': True,
                },
                'city': {
                    'type': 'string',
                    'required': True,
                }
            },
        },
    })

Generated input example result:

.. code-block:: yaml

    address:
      city: str
      street: str
      zip_code: str
    age: 12345
    name: str

Example of complete generated documentation
--------------------------------------------

Schema:

.. code-block:: py

    schema = CerberusSchema{
        "version": {
            "required": True,
            "type": "string",
            "meta": {'description': "The version of the schema"},
            "allowed": ["v0", "v1"],
        },
        "type": {
            "required": True,
            "type": "string",
        },
        "spec": {
            "type": "dict",
            "required": False,
            "schema": {
                "sources": {
                    "type": "list",
                    "required": False,
                    "meta": {'description': "Available sources"},
                    "schema": {
                        "type": "dict",
                        "schema": {
                            "name": {
                                "type": "string",
                                "required": True,
                            },
                            "domain": {
                                "type": "string",
                                "required": True,
                            },
                        }
                    },
                }
            },
        },
    })

Result:

.. code-block::

    ## MyFile

    `version`: **[required]** string, one of;
      - v0
      - v1


        The version of the schema

    `type`: **[required]** string,

    `spec`: **[optional]** dict, [MyFileSpec](#MyFileSpec)


    ## MyFileSpec

    `sources`: **[optional]** list, [MyFileSpecSources](#MyFileSpecSources)


        Available sources


    ## MyFileSpecSources

    `name`: **[required]** string,

    `domain`: **[required]** string,


    ## Example Schema Input

    ```
    version: v0
    type: str
    spec:
      sources:
      - domain: str
        name: str
    ```

Running Tests
=============
You can run tests in all supported Python versions using ``tox``. By default,
it will run all of the unit tests, linters and coverage. Note that this requires that you have all supported
versions of Python installed, otherwise you must pass ``-e``.

.. code-block:: sh

    $ tox
    $ tox -e py37,py38

Enable Debug Logging
====================
To enable logging, add a handler to the `cerberus_docs` logger:

.. code-block:: python

    >>> import logging
    >>> logging.getLogger('cerberus_docs').addHandler(logging.StreamHandler())
    >>> logging.getLogger('cerberus_docs').setLevel(logging.DEBUG)

Requirements
============

Install requirements
--------------------------------
Required dependencies:

    pip install -r requirements/requirements.txt

Dev dependencies:

    pip install -r requirements/dev.txt

Documentation dependencies:

    pip install -r requirements/docs.txt

Update requirements
-------------------
If a dependency is to be added to the project, add it to the appropriate .in file and run:

    pip-compile requirements/path-to-file.in

Sync local virtual environment with requirements
------------------------------------------------
If you would like to sync your local virtual environment with the generated and locked requirements of the project, run:

    pip-sync requirements/path-to-file.in requirements/another-file.in

Test suite
==========

Run test suite locally
----------------------
Navigate to the root of the project and run:

    tox

Show list of commands
---------------------
Show the list of tox commands available to run individually:

    tox -a

Run tests
---------
    tox -e py37,py38,py39,py310

Run linters
-----------
    tox -e lint

Run Coverage
------------
    tox -e cov

Build the project
-----------------
    tox -e build

Publish the project
-------------------
Must have built the project prior to running this command.

Running the following command will by default publish the build to TestPyPi:

    tox -e publish

If you want to release it to be publically accessible in PyPi, use:

    tox -e publish -- --repository pypi

Pre-commit hooks
================

It is recommended that every developer working on this project activate pre-commit hooks.

Activate pre-commit hooks
-------------------------
    pre-commit install

Documentation
=============

The documentation is automatically built and deployed via github workflow.

Build documentation locally
---------------------------
    tox -e build-docs

This will create a docs/_build/html folder with an index.html file that can be opened in a browser.
