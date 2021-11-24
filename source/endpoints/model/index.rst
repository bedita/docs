.. _api-modelling:

Model ``/model``
================

``/model`` base endpoint provides a set of methods dedicated to data modeling.

Unlike other endpoints ``/model`` is basically a prefix to the actual endpoints that handle object types, property types, properties and relations. You can see this endpoints collection as the **API Modeling** part of BEdita.

Main operations available on these endpoints:

* list, create, update and (de)activate object types via ``/model/object_types``
* define new property types using :term:`JSON SCHEMA` ``/model/property_types``
* handle object type properties with ``/model/properties``
* define relations between objects with ``/model/relations``
* retrieve :term:`JSON SCHEMA` of object types and resources from ``/model/schema``

.. toctree::
    :caption: Model endpoints reference

    object_types
    property_types
    properties
    relations
    schema

.. _api-model-naming:

Naming convention
-----------------

Main entities describing your data model must be in `lowered snake_case <https://en.wikipedia.org/wiki/Snake_case>`_ format as internal naming convention.

This applies to ``name`` attribute of object types (also ``singular`` see :ref:`api-model-object-types`), property types, properties and relations (also ``inverse_name``)
