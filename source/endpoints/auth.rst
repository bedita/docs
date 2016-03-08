Authentication ``/auth``
========================

It used to retrieve an :term:`access token` to access protected items, renew
:term:`access token` and remove permissions. The :term:`access token` is a `Json Web Token <http://jwt.io>`_
(`IETF <https://tools.ietf.org/html/rfc7519>`_). More info on :doc:`authentication </authentication>`

.. include:: /fragments/jwt_important.rst

Obtain an access token
----------------------

.. http:post:: /auth

    Authenticate an user to obtain an :term:`access token`.

    :reqjson string username: the username
    :reqjson string password: the password
    :reqjson string grant_type: `"password"`, the grant type to apply (password is the default, it can be ommitted)

    :resheader Content-Type: application/json
    :status 200: response contains :term:`access token` and :term:`refresh token`
    :status 400: when required parameters are missing or the request is malformed
    :status 401: when authentication fails

    **Example request**:

    .. sourcecode:: http

        POST /auth HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "username": "test",
            "password": "test",
            "grant_type": "password"
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "auth",
            "data": {
                "access_token": "eyJ0eXAi.....",
                "expires_in": 600,
                "refresh_token": "51a3f718e7abc712e421f64ea497a323aea4e76f"
                },
            "method": "post",
            "params": [ ],
            "url": "https://example.com/api/auth"
        }


    .. note::

        Once you received the access token you have to use it in every request
        that requires authentication. It can be used in HTTP header

        ::

            Authorization: Bearer <token>

        or as query string ``/api/endpoint?access_token=<token>``

Renew the access token
----------------------

If the access token was expired you need to generate a new one started
by refresh token. **In this case do not pass the expired access token**

.. http:post:: /auth

    Renew an `access_token`.

    :reqjson string refresh_token: the :term:`refresh token` to use to renew :term:`access token`
    :reqjson string grant_type: `"refresh_token"`, the grant type to apply

    :resheader Content-Type: application/json
    :status 200: Success, it responds with the new :term:`access token` and :term:`refresh token`
    :status 400: when required parameters are missing or the request is malformed
    :status 401: when :term:`refresh token` is invalid

    **Example request**:

    .. sourcecode:: http

        POST /auth HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "grant_type": "refresh_token",
            "refresh_token": "51a3f718e7abc712e421f64ea497a323aea4e76f"
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "auth",
            "data": {
                "access_token": "rftJasd3.....",
                "expires_in": 600,
                "refresh_token": "51a3f718e7abc712e421f64ea497a323aea4e76f"
                },
            "method": "post",
            "params": [ ],
            "url": "https://example.com/api/auth"
        }

Get the updated time to access token expiration
-----------------------------------------------

.. http:get:: /auth

    It returns the updated ``expires_in`` time for :term:`access token`

    :reqheader Authorization: the :term:`access token` as Bearer token

    :resheader Content-Type: application/json
    :status 200: no error, payload contains the updated ``expires_in`` value
    :status 400: the request is malformed
    :status 401: the :term:`access token` is invalid

    **Example request**:

    .. sourcecode:: http

        GET /auth HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "auth",
            "data": {
                "access_token": "rftJasd3.....",
                "expires_in": 48
            },
            "method": "get",
            "params": [ ],
            "url": "https://example.com/api/auth"
        }

Revoking a :term:`refresh token`
--------------------------------

In order to invalidate an :term:`access token` you need to remove it from client and revoke the :term:`refresh token`

.. http:delete:: /auth/(string:refresh_token)

    Revoke a :term:`refresh token`

    :reqheader Authorization: the :term:`access token` as Bearer token
    :param string refresh_token: the :term:`refresh token` to revoke

    :status 204: the refresh token was deleted
    :status 400: the request is malformed
    :status 401: the :term:`access token` is invalid or
    :status 404: the :term:`refresh token` was already revoked or not exists
