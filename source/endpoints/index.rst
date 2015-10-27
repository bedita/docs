API reference
=============

A :doc:`frontend app enabled to consume REST API </setup>` exposes a set of default
endpoints.

.. note::

    Every :http:method:`post` request can send the payload as ``x-www-form-urlencoded`` or ``application/json``.
    For readability all examples will use ``Content-type: application/json``.

.. toctree::
    :maxdepth: 2

    auth
    objects
    poster
    me

.. seealso::

    To an index of all API requests see `HTTP Routing Table <../http-routingtable.html>`_
