Status ``/status``
===========================

``/status`` is a service endpoint to check BEdita service status: environment,
software versions, filesystem, database connection are checked.

In normal **production mode** no details are given about software versions and about warnings or failures,
whereas in **debug mode** more details are shown.

Only **GET** method is available

    **Example request**:

    .. sourcecode:: http

      GET /status HTTP/1.1
      Accept: application/json

    **Expected response**:

    .. sourcecode:: http

      HTTP/1.1 200 Success
      Content-Type: application/json

        {
            "links": {
                "self": "http://example.org/home",
                "home": "http://example.org/home"
            }

            "meta": {
                "status": {
                    "environment": "ok"
                }
            }
        }

In debug mode more informations will be displayed, including software versions and plugins loaded

    **Debug mode response**:

    .. sourcecode:: http

      HTTP/1.1 200 Success
      Content-Type: application/json

        {
            "links": {
                "self": "http://example.org/home",
                "home": "http://example.org/home"
            }

            "meta": {
                "status": {
                    "environment": "ok",
                    "errors": [ ],
                    "debug": true,
                    "plugins": [
                        "BEdita/API",
                        "BEdita/Core",
                        "DebugKit",
                        "Migrations"
                    ],
                    "versions": {
                        "BEdita": "4.0.0-alpha"
                    }
                }
            }
        }
