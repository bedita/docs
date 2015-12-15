Posters ``/posters``
====================

.. include:: /fragments/endpoint_dev_warning.rst

Get the image representation of object *object_id* as thumbnail url
--------------------------------------------------------------------

.. http:get:: /posters/(object_id)

   Get the thumbnail url of an image representation of the object *object_id*.
   The thumbnail returned depends from the object type of *object_id* and from
   its relations, in particular:

   1. if object *object_id* has a 'poster' relation with an image object then it
      returns a thumbnail of that image
   2. else if the object is an image then it returns a thumbnail of the object
   3. else if the object has an 'attach' relation with an image object then
      it returns a thumbnail of that image

   :reqheader Authorization: optional :term:`access token`

   :param int|string object_id: identify a BEdita object. It can be the object id or the object unique name (nickname)
   :queryparam int width: the thumbnail width
   :queryparam int height: the thumbnail height

   :resheader Content-Type: application/json

   :status 200: Success
   :status 401: The object *object_id* is protected and the request is not authorized
   :status 403: The request is authorized but without sufficient permission to access object *object_id*
   :status 404: Object *object_id* not found

   **Example request**:

   .. sourcecode:: http

      GET /posters/5 HTTP/1.1
      Host: example.com
      Accept: application/json, text/javascript
      Content-Type: application/json

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 Success
      Host: example.com
      Accept: application/json, text/javascript
      Content-Type: application/json

      {
       "api": "posters",
       "data": {
         "id": 5,
         "uri": "https://media.server/path/to/thumb/thumbnail.jpg"
       },
       "method": "get",
       "params": [],
       "url": "https://example.com/api/posters/5"
      }

Get a collection of image representations
------------------------------------------

The ``/posters`` endpoint can be used also to retrieve a collection of image representations.

.. http:get:: /posters

    If called with ``id`` query string parameter the response will contain a collection of the requested `posters`

    .. important::

        Note that when ``id`` query string is used, no other parameters is valid but :term:`access token`.

    The response will be an array of posters as shown below.

    :reqheader Authorization: optional :term:`access token`

    :query id: a comma separated list of object ids. **See the important note above**.
        The max number of ids you can request is defined by ``ApiBaseController::$paginationOptions['maxPageSize']``

    :resheader Content-Type: application/json

    :status 200: Success
    :status 400: Malformed request
    :status 401: The request is not authorized to access to protected publication
    :status 403: The request is authorized but without sufficient permissions to access to protected publication

    **Example request**:

    .. sourcecode:: http

        GET /posters HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
           "api": "posters",
           "data": [
               {
                 "id": 1,
                 "uri": "https://media.server/path/to/thumb/thumbnail-1.jpg"
               },
               {
                 "id": 2,
                 "uri": "https://media.server/path/to/thumb/thumbnail-2.jpg"
               },
               {
                 "id": 3,
                 "uri": "https://media.server/path/to/thumb/thumbnail-3.jpg"
               },
               {
                 "id": 4,
                 "uri": "https://media.server/path/to/thumb/thumbnail-4.jpg"
               },
               {
                 "id": 5,
                 "uri": "https://media.server/path/to/thumb/thumbnail-5.jpg"
               }
           ],
           
           
            "method": "get",
            "params": {
                "id": "1,2,3,4,5"
            },
            "url": "https://example.com/api/posters"
        }

