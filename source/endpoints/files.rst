Files upload ``/files``
=======================

It is used to upload files that must be linked to an object,
generally multimedia objects as `image`, `video`, `audio`,...

The file upload flow is described in :doc:`../uploading_files` section.

.. warning::

    At the moment uploaded files are only linkable to new BEdita objects
    that support upload.
    You can't change file associated to an existing object.

Upload a file
-------------

.. http:post:: /files/(string:object_type)/(string:file_name)

    Upload a file related to an ``object_type`` and named ``file_name`` and obtain an :term:`upload token`.
    The ``object_type`` needs to validate some property of the file uploaded as the mime type.
    For example the `image` object type will accept ``image/jpeg`` but will reject ``video/mp4``.

    ``file_name`` should be the original file name **url encoded**.  

    .. include:: /fragments/objects_writable_important.rst

    User has to be :doc:`authenticated </endpoints/auth>`
    and the request body must contain the binary file data.

    :reqheader Authorization: (**required**) :term:`access token`
    :reqheader Content-Type: (**required**) the content type of the file
    :reqheader Content-Length: (**required**) the size of the file

    :resheader Content-Type: application/json
    :status 200: Success, the file was uploaded. The response body will contain the :term:`upload token`
    :status 400: Required parameters are missing or the request is malformed or some validation fails.
    :status 401: Request is not authorized
    :status 403: The quota available or the max number of files allowed is exceeded 
    :status 409: The file is already present

    **Example request**:

    .. sourcecode:: http

        POST /files/image/foo.jpg HTTP/1.1
        Host: example.com
        Accept: application/json
        Content-Type: image/jpeg
        Content-Length: 284

        raw image content

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 Success
        Content-Type: application/json

        {
          "api": "files",
          "data": {
              "upload_token": "80fdd2590b609d51873fe187d65f026e39748179"
          },
          "method": "post",
          "params": [],
          "url": "https://example.com/api/files/image/foo.jpg"
        }

    Once obtained the :term:`upload token` it must be used to finalize the upload action
    creating a new object linked to the file uploaded.
    You can do it using :http:post:`/objects` and passing the :term:`upload token` in the request
    data payload.

    **Example**

    .. sourcecode:: http

        POST /objects HTTP/1.1
        Host: example.com
        Accept: application/json
        Content-Type: application/json

        {
            "data": {
                "title": "Uploaded via API!",
                "object_type": "image",
                "upload_token": "80fdd2590b609d51873fe187d65f026e39748179",
                "parents": [1]
            }
        }

    .. sourcecode:: http

        HTTP/1.1 201 Created
        Content-Type: application/json

        {
          "api": "objects",
          "data": {
              "id": 57,
              "title": "Uploaded via API!",
              "object_type": "image",
              "name": "foo.jpg",
              "original_name": "foo.jpg",
              "mime_type": "image/jpeg",
              "uri": "https://assets.example.com/cd/df/foo.jpg",
              "file_size": 284,
              "width": 200,
              "height": 100
          },
          "method": "post",
          "params": [],
          "url": "https://example.com/api/objects"
        }

    .. note::

        In this example the image object created is located on publication tree.
        See :http:post:`/objects` to know the required paramters creating an object.