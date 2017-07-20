Authentication ``/auth``
========================

``/auth`` endpoint is responsible for login related actions.
With it a user is able to login and to get user login data.

Different actions are supported:

* standard **login** - providing username and password and without ``Authorization`` header
* token **renew** - with ``Authorization`` header and without username and password
* **whoami** - get logged user profile data
* credential **change** request - request to update password
* credential **change** update - actual password update action using secret hash

.. _auth-login:

Login
-----

.. http:post:: /auth

    Perform login.

    :form username: Username of user to be logged in. To be omitted when renewing token.
    :form password: Password of user to be logged in. To be omitted when renewing token.
    :reqheader Authorization: *(Optional)* Use only in token renew, prefixed with ``Bearer``.
    :status 200: Login successful.
    :status 401: Unauthorized user, or invalid renew token.
    :resjson meta.jwt: The JSON Web Token to be used to authenticate (in header :http:header:`Authorization`).
    :resjson meta.renew: The renew token, to be used to reiterate login process when JWT expires.

    **Example request (login with username and password)**:
    since this is not a :term:`JSON API` request you MUST use ``Content-Type: application/json``
    or ``Content-Type: application/x-www-form-urlencoded``, see example below.

    .. sourcecode:: http

        POST /auth HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json
        Content-Type: application/json

        {
            "username" : "bedita",
            "password" : "bedita"
        }

    Same request using classic ``Content-Type: application/x-www-form-urlencoded``

    .. sourcecode:: http

        POST /auth HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json
        Content-Type: application/x-www-form-urlencoded

        username=bedita&password=bedita


    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/vnd.api+json

        {
            "links": {
                "self": "http://example.com/auth",
                "home": "http://example.com/home"
            },
            "meta": {
                "jwt": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJiZWRpdGEiLCJibG9ja2VkIjpmYWxzZSwibGFzdF9sb2dpbiI6IjIwMTYtMDgtMDFUMTM6MTk6MzkrMDAwMCIsImxhc3RfbG9naW5fZXJyIjpudWxsLCJudW1fbG9naW5fZXJyIjowLCJjcmVhdGVkIjoiMjAxNi0wOC0wMVQxMzoxOToyOSswMDAwIiwibW9kaWZpZWQiOiIyMDE2LTA4LTAxVDEzOjE5OjI5KzAwMDAiLCJyb2xlcyI6W10sImlzcyI6Imh0dHA6XC9cLzEwLjAuODMuNDo4MDgwIiwiaWF0IjoxNDcwMDU4NTE3LCJuYmYiOjE0NzAwNTg1MTcsImV4cCI6MTQ3MDA2NTcxN30.rGcCEKiYjETnkaKVgG5-gJxIMXALVaZ4MeV5aKbWtQE",
                "renew": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC8xMC4wLjgzLjQ6ODA4MCIsImlhdCI6MTQ3MDA1ODUxNywibmJmIjoxNDcwMDU4NTE3LCJzdWIiOjEsImF1ZCI6Imh0dHA6XC9cLzEwLjAuODMuNDo4MDgwXC9hdXRoIn0.mU3QToPvc0uY-XQRhDA1F2hfpRjjT2ljSerKQygk2T8"
            }
        }

    **Example request (token renewal)**:

    .. sourcecode:: http

        POST /auth HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json
        Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC8xMC4wLjgzLjQ6ODA4MCIsImlhdCI6MTQ3MDA1ODUxNywibmJmIjoxNDcwMDU4NTE3LCJzdWIiOjEsImF1ZCI6Imh0dHA6XC9cLzEwLjAuODMuNDo4MDgwXC9hdXRoIn0.mU3QToPvc0uY-XQRhDA1F2hfpRjjT2ljSerKQygk2T8

    **Example response**: (*same as above, with new JWT and renew token*)

.. _auth-whoami:

Who Am I?
---------

.. http:get:: /auth

    Get logged user profile data.

    :reqheader Authorization: Use token prefixed with ``Bearer``.
    :status 200: Get operation successful.
    :status 401: Unauthorized user, user not logged.
    :resjson data: User profile data

    **Example request (token from previous login example)**:

    .. sourcecode:: http

        GET /auth HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json
        Authorization: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJiZWRpdGEiLCJibG9ja2VkIjpmYWxzZSwibGFzdF9sb2dpbiI6IjIwMTYtMDgtMDFUMTM6MTk6MzkrMDAwMCIsImxhc3RfbG9naW5fZXJyIjpudWxsLCJudW1fbG9naW5fZXJyIjowLCJjcmVhdGVkIjoiMjAxNi0wOC0wMVQxMzoxOToyOSswMDAwIiwibW9kaWZpZWQiOiIyMDE2LTA4LTAxVDEzOjE5OjI5KzAwMDAiLCJyb2xlcyI6W10sImlzcyI6Imh0dHA6XC9cLzEwLjAuODMuNDo4MDgwIiwiaWF0IjoxNDcwMDU4NTE3LCJuYmYiOjE0NzAwNTg1MTcsImV4cCI6MTQ3MDA2NTcxN30.rGcCEKiYjETnkaKVgG5-gJxIMXALVaZ4MeV5aKbWtQE

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/vnd.api+json

        {
          "data": {
                "id": "2",
                "type": "users",
                "attributes": {
                    "username": "gustavo",
                    "blocked": false,
                    "last_login": "2016-10-06T08:17:36+00:00",
                    "last_login_err": null,
                    "num_login_err": 0,
                    "name": "Gustavo",
                    "surname": "Supporto"
                }
            },
            "links": {
                "self": "http://example.com/auth",
                "home": "http://example.com/home"
            },
        }

    **Note**: some fields in previous example are missing for brevity in user *"attributes"*.

.. _auth-change:

Credentials change
------------------

Authentications credential change works in two steps:
 * a credential change request action
 * an actual credential change using a secret hash

Only use case currently supported is ``password`` change.

After a request action an email is sent to requesting user containing a URL with a secret hash to actually perform the change.

.. http:post:: /auth/change

    Request a credential change.

    :form contact: Email of user requesting credenditals change.
    :form change_url: Change URL that will be sent via email.
    :status 204: No content on operation success.
    :status 400: On malformed or missing input data.
    :status 404: If no user is found.

    **Example request**:
    Since this is not a :term:`JSON API` request you MUST use ``Content-Type: application/json``

    .. sourcecode:: http

        POST /auth/change HTTP/1.1
        Content-Type: application/json

        {
            "contact": "{my email}",
            "change_url": "{change url}"
        }


A ``change_url`` is required in order to create the URL that will be sent to the user in this form:

    .. sourcecode:: http

        {change_url}?uuid={uuid}

Where ``{uuid}`` is a system generated hash that will expire after 24h.

In your ``change_url`` page you will have to read the ``uuid`` query parameter and proceed to actual change performing the following request.


.. http:patch:: /auth/change

    Perform a credential (password) change.

    :form uuid: Secret UUID sent via email in ``change_url``.
    :form password: New user password.
    :form login: Optional boolean parameter, if ``true`` a login is also performed.
    :status 200: On operation success.
    :status 400: On malformed or missing input data.
    :status 404: Not found if provided UUID is incorrect or expired.

    **Example request**:
    Since this is not a :term:`JSON API` request you MUST use ``Content-Type: application/json``

    .. sourcecode:: http

        PATCH /auth/change HTTP/1.1
        Content-Type: application/json

        {
            "uuid": "{uuid}",
            "password": "{new password}",
            "login": true
        }

Response will contain user data as in previous :ref:`auth-whoami` request.

If ``"login"`` is true a login is also performed and :term:`JWT` :term:`access token` and :term:`refresh token` tokens are returned in ``"meta"`` section for immediate use. This key is optional, if missing ``"login": false`` is assumed.
