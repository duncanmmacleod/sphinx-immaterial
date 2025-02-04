JSON domain
===========

This theme defines a ``json`` domain that supports documenting `JSON schemas
<https://json-schema.org/specification.html>`__.

Prerequisite
------------

This extension requires the `PyYAML <https://pypi.org/project/PyYAML/>`__
package; you must either add it as a dependency directly, or add ``json`` extra
feature of this package as a dependency:

.. code-block:: shell

   pip install sphinx-immaterial[json]


Configuration
-------------

To use this domain, add :python:`sphinx_immaterial.json_domain` to the list of
:python:`extensions` in :file:`conf.py` and set the :confval:`json_schemas`
configuration option to a list of glob patterns specifying the JSON schema
definition files.

.. code-block:: python

    extensions = [
        # other extensions...
        "sphinx_immaterial.json_domain",
    ]

    json_schemas = [
        "**/*_schema.yml",
    ]

.. confval:: json_schemas

   List of :py:obj:`glob` patterns matching JSON schema definition files
   relative to the documentation source directory.

   Both JSON and YAML format are supported.

   The top-level schema in each file must specify an ``$id``.

.. confval:: json_schema_validate

   Indicates whether to validate the JSON schemas specified by
   :confval:`json_schemas`.

   Requires that the `jsonschema <https://pypi.org/project/jsonschema/>`__
   package is installed; you can either add it as a dependency directly, or
   depend on the ``jsonschema_validation`` extra feature of this package:

   .. code-block:: shell

      pip install sphinx-immaterial[json,jsonschema_validation]

All of the `object-description-options` also apply to this domain.  Any
top-level schemas have an object type of ``json:schema`` and any members have an
object type of ``json:subschema``.

Usage
-----

.. rst:directive:: json:schema

   Generates documentation for a single JSON schema specified by its ``$id``.

   .. note::

      It is not possible to refer to a schema without an ``$id``.

   The ``title`` property and the ``description`` properties are parsed as
   reStructedText, with a `default_role` of :rst:role:`json:schema`.

   If :objconf:`generate_synopses` is not disabled, the synopsis is generated
   from the ``title`` property.  If there is no ``title`` property, no synopsis
   is generated.

   .. rst:directive:option:: noindex

      Flag option to exclude the schema from search results and the table of
      contents.  Also disables cross linking.

      This option implies :rst:dir:`exclude_from_toc`.

   .. rst:directive:option:: title

      Specifies a title to use for the schema instead of the ``$id``.

   .. rst:directive:option:: toc_title

      Specifies the title to use in the table of contents instead of the
      :rst:dir:`title` or ``$id``.

      Only has an effect if :objconf:`include_in_toc` is :python:`True`.

   .. rst:directive:option:: exclude_from_toc

      Excludes the schema from the table of contents.

      This overrides :objconf:`include_in_toc`.

.. rst:role:: json:schema

   Cross-links to a JSON schema defined using :rst:dir:`json:schema`, or one of
   its properties.

   .. rst-example::

      A complete schema is specified by its ``$id``: :json:schema:`Pet`.

      An individual member of a schema is specified using dot notation:
      :json:schema:`Pet.name`.

   Similar to the Python domain, the target may start with ``~`` to shorten the
   link text to just the last component:

   .. rst-example::

      :json:schema:`~Pet.name`

   If used within the ``title`` or ``description`` of a :rst:dir:`json:schema`,
   cross-references are resolved relative to the current schema.  For example,
   within the "description" for the ``MySchema.member`` schema:

   .. code-block:: rst

      :json:schema:`.other_member`

   will look first for ``MySchema.member.other_member``, then
   ``MySchema.other_member``, then ``other_member``.  This is the same behavior
   as for the Python domain.  If the initial ``.`` is removed, then search order
   is reversed.  For example: ``:json:schema:`other_member``` will look first
   for ``other_member``, then ``MySchema.other_member``, then
   ``MySchema.member.other_member``.

Examples
--------

Inheritance example
^^^^^^^^^^^^^^^^^^^

.. literalinclude:: inheritance_schema.yml
   :language: yaml
   :caption:

.. rst-example::

   .. json:schema:: Pet
   .. json:schema:: Dog
   .. json:schema:: Cat

Other example
^^^^^^^^^^^^^

.. literalinclude:: index_transform_schema.yml
   :language: yaml
   :caption:

.. rst-example::

   .. json:schema:: IndexTransform
   .. json:schema:: OutputIndexMap
   .. json:schema:: IndexInterval
