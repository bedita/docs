Uploading files
===============

The BEdita REST API allows to upload files and link it to BEdita objects.
The process of upload follow the schema:

::

    +--------+                                                   +---------+
    |        |--(A)-- POST /files/:object_type/:filename ------->|         |
    |        |                                                   |         |
    |        |<-(B)------------- 200 Success --------------------|         |
    |        |             {"upload_token": "hash"}              |         |
    | Client |                                                   |   API   |
    |        |                                                   |         |
    |        |--(C)----- POST /objects with upload_token ------->|         |
    |        |                                                   |         |
    |        |<-(D)------------- 201 Created --------------------|         |
    +--------+                                                   +---------+

where:

(A) The client send a request to upload a file
(B) The API responds with :http:statuscode:`200` and an upload token
(C) The client finalize the upload request creating a BEdita object
    and using the upload token received for link the new object to
    the file uploaded
(D) The API responds with  :http:statuscode:`201`

.. glossary::
    :sorted:

    Upload token
        An opaque token issued from API server that refers to a file uploaded
        from an authenticated user and for a specific object type.
        The token has a an expiration date. If used after that expiration date
        the request will be invalid. 