Home Document ``/home``
========================

``/home`` endpoint returns basic information about available API resources

Inspired by **Home Document for HTTP APIs** IETF draft http://mnot.github.io/I-D/json-home/
its purpose is to show avilable endpoints, content type formats and HTTP methods

This information depends on user permissions and endpoint configuration.

    **Example request**:

    .. sourcecode:: http

      GET /home HTTP/2
      Host: example.org
      Accept: application/json

    **Example response**:

    .. sourcecode:: http

      HTTP/2 200 Success
      Host: example.org
      Content-Type: application/json

        {
            "links": {
                "self": "http://example.org/home",
                "home": "http://example.org/home"
            },
            "meta": {
                "resources": {
                    "/users": {
                        "href": "http://example.org/users",
                        "hints": {
                        "allow": [
                            "GET"
                        ],
                        "formats": [
                            "application/json",
                            "application/vnd.api+json"
                        ]
                    },
                    "/roles": {
                        "href": "http://example.org/roles",
                        "hints": {
                        "allow": [
                            "GET"
                        ],
                        "formats": [
                            "application/json",
                            "application/vnd.api+json"
                        ]
                    },
                }
            }
        }

