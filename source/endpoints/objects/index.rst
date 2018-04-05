Objects ``/objects``
====================

``/objects`` base endpoint provides a set of methods dedicated to objects management.

`objects` is an abstract model for BEdita. Every object created through data modelling are instances of `objects`.

Main operations available on these endpoints:

* retrieve a list of objects via ``GET /objects``
* retrieve data for a single object via ``GET /objects/:id``
* delete an object via ``DELETE /objects/:id``

.. toctree::
    :caption: Objects endpoints reference

    list
    view
    delete
