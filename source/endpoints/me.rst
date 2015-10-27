User profile ``/me``
====================

.. include:: /fragments/endpoint_dev_warning.rst

Obtain information about authenticated user
-------------------------------------------

.. http:get:: /me

    Return information about current authenticated user

    :reqheader Authorization: (**required**) :term:`access token`

    :resheader Content-Type: application/json

    :status 200: Success
    :status 401: The request is not authorized
    :status 404:
