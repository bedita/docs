Poster ``/poster``
==================

.. include:: /fragments/endpoint_dev_warning.rst

Get the image representation of object *object_id* as thumbnail url
--------------------------------------------------------------------

.. http:get:: /poster/(object_id)

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

      GET /poster/5 HTTP/1.1
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
       "api": "poster",
       "data": {
         "id": 5,
         "uri": "https://media.server/path/to/thumb/thumbnail.jpg"
       },
       "method": "get",
       "params": [],
       "url": "https://example.com/api/poster/5"
      }
