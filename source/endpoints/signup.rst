Signup ``/signup``
==================

``/signup`` is the endpoint used to allow users to directly register as project user.

Of course users can be created using :doc:`users` endpoint, but with ``/signup`` you may allow autonomous user registration.

Signup works in two steps:
 * signup request: a new user is created, but not activated or verified - therefore the new user may or may not perform some actions in your application
 * signup activation: through a UUID hash actual activation and verification of user's email is done


.. _signup-request:

Signup request
--------------

This request allows unverified user signup via **POST** that will usually be called anonymously.

Minimum required data are: ``username``, ``password`` and ``email``.

An ``activation_url`` is also required in order to activate the user.
An activation email is then sent to the user containing the actual activation URL that will have this form:

    ``{activation_url}?uuid={uuid}&redirect_url={redirect_url}``

Where ``{uuid}`` is a system generated hash, and ``{redirect_url}`` is an optional parameter passed in ``/signup`` request.

In your ``activation_url`` page you have to read the ``uuid`` query parameter and then proceed to `Signup activation`.

In this first step a user with status ``draft`` will be created, user verification job is also added to ``async_jobs``: after 24h user verification with provided uuid will expire.

.. http:post:: /signup

    Perform user signup request.

    :form username: Username of user, must be unique.
    :form password: Password of user.
    :form email: User email, must be unique.
    :form activation_url: Activation URL that will be sent via email.
    :form redirect_url: Optional redirect url that will be added to activation URL as parameter.
    :status 202: Successful user creation. User data will be displayed in response.
    :status 400: Bad request if username or email have already been used by other users.

    **Example request**:
    Since this is not a :term:`JSON API` request you MUST use ``Content-Type: application/json``

    .. sourcecode:: http

        POST /signup HTTP/1.1
        Host: api.example.com
        Accept: application/vnd.api+json
        Content-Type: application/json

        {
            "username": "johannadoe",
            "password": "j0h4nn4d0e",
            "email": "johannadoe@nowhere.xx"
            "activation_url": "http://myactivationsys.xx?dum=my",
            "redirect_url": "app://xx?dum=my"
        }

    **Example response**:

    Some fields are not displayed for brevity.

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/vnd.api+json

        {
            "data": {
                "id": "1234",
                "type": "users",
                "attributes": {
                    "username": "johannadoe",
                    "name": null,
                    "surname": null,
                    "email": "johannadoe@nowhere.xx",
                    "status": "draft",
                    "uname": "user-johannadoe",
                    "title": null,
                    "description": null,
                },
                "meta": {
                    "blocked": false,
                    "last_login": null,
                    "last_login_err": null,
                    "num_login_err": 0,
                    "verified": null,
                    "locked": false,
                    "created": "2017-07-20T08:48:25+00:00",
                    "modified": "2017-07-20T08:48:25+00:00",
                },
                "relationships": {
                    "roles": {
                        "links": {
                            "related": "http://api.example.com/users/1234/roles",
                            "self": "http://api.example.com/users/1234/relationships/roles"
                        }
                    }
                }
            },
            "links": {
                "self": "http://api.example.com/signup",
                "home": "http://api.example.com/home"
            }
        }



.. _signup-activation:

Signup activation
-----------------

User verification and activation are done via a simple **POST** like in the following example that should be invoked in your
**activation url** page after reading the passed **uuid** parameter.

On success an HTTP 202 status code is returned with an empty body.

.. http:post:: /signup/activation

    Perform user signup activation.

    :form uuid: UUID of signup activation.
    :status 202: Successful activation.
    :status 404: Not found, if provided UUID is incorrect or expired.

    **Example request**:
    Since this is not a :term:`JSON API` request you MUST use ``Content-Type: application/json``

    .. sourcecode:: http

        POST /signup/activation HTTP/1.1
        Content-Type: application/json

        {
            "uuid": "96b0b9fe-17fa-4cf8-bffa-1cd506421227"
        }

