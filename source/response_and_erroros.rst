Response and Errors
===================

Response
--------

Usually the response of API query has the structure

.. code-block:: json

    {
        "api": "objects",
        "data": {},
        "method": "get",
        "params": [],
        "url": "https://example.com/api/v1/objects/1"
    }

where:

-  ``api`` is the endpoint called
-  ``data`` is an object containing all data requested
-  ``method`` is the HTTP verb used in the request
-  ``params`` contains all query url params used in the request
-  ``url`` is the complete url requested (full base url + basepath +
   endpoint + other)

To set ``data`` for response is available the method
``ApiBaseController::setData()`` that accepts an array as first
argument. A second argument permits to replace (default) or merge
present data with that passed.

Other meta data can be placed inside response object, for example
``paging`` useful to paginate results:

.. code-block:: json

    {
        "api": "objects",
        "data": {},
        "method": "get",
        "paging": {
            "page": 1,
            "page_size": 10,
            "page_count": 10,
            "total": 995,
            "total_pages": 100
        },
        "params": [],
        "url": "https://example.com/api/v1/objects/1/children"
    }

where:

-  ``page`` is the current page
-  ``page_size`` is the page dimension
-  ``page_count`` is the number of items inside current page
-  ``total`` if the count of all items
-  ``total_pages`` is the total pages available

.. note::

   If you need **to serve empty response body** to client you can use
   ``ApiBaseController::emptyResponse()`` that by default send a **204 No
   Content** HTTP status code. Pass another status code as first argument to send
   different status code.

``data`` fields format
~~~~~~~~~~~~~~~~~~~~~~

Every response in data is formatted in the right type usually according to database fields type.
So it can contain values as integers, floats, booleans and dates.

While other types are clear we need to clarify the format used for dates.
All reponse dates are formatted using `ISO-8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ standard combining
date and time, for example ``2016-11-14T15:54:01+01:00``.

The client should always send dates in that format or using the form with **Z** as zone designator to indicate UTC,
coming for example from javascript function ``Date().toISOString()``.

So the same date should be sent from client as ``2016-11-14T15:54:01+01:00`` or ``2016-11-14T14:54:01.640Z``.

.. tip::

    If you need to customize the format data types you could be interesed in reading :ref:`formatting-objects-cast-types`

Errors
------

Every time the API thrown an error the response will be similar to

.. code-block:: json

    {
        "error": {
            "status": 405,
            "code": null,
            "message": "Method Not Allowed",
            "details": "Method Not Allowed",
            "more_info": null,
            "url": "https://example.com/api/v1/foobar"
        }
    }

where:

-  ``status`` is the HTTP status code
-  ``code`` is the API error code (if exists)
-  ``message`` is the error message
-  ``details`` is the error detail
-  ``more_info`` can contain useful information for better understand the error.
-  ``url`` is the url that has responded with the error

.. _error-codes:

Error codes
~~~~~~~~~~~

Here is a list of the error codes thrown from the API. 

+-------------------------------------+-------------------------------------+-------------------------------+
|      Error Code                     |   Description                       | HTTP status code              |
+=====================================+=====================================+===============================+
| UPLOAD_MAX_FILESIZE_EXCEEDED        | Upload max file size exceeded       | :http:statuscode:`400`        |
+-------------------------------------+-------------------------------------+-------------------------------+
| UPLOAD_QUOTA_EXCEEDED               | Upload quota available exceeded     | :http:statuscode:`403`        |
+-------------------------------------+-------------------------------------+-------------------------------+
| UPLOAD_FILES_LIMIT_EXCEEDED         | Maximum number of files allowed     | :http:statuscode:`403`        | 
|                                     | exceeded                            |                               |
+-------------------------------------+-------------------------------------+-------------------------------+