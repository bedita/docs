Objects ``/objects``
====================

Get an object
-------------

.. http:get:: /objects/(object_id)

    Get an object detail.

    .. include:: /fragments/objects_related_endpoint_desc.rst

    .. note::

        Note that the response data fields can change depending of
        BEdita object type exposed and configuration.

    **Example request**:

    .. sourcecode:: http

        GET /objects/15 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "objects",
            "data": {
                "object": {
                    "id": 15,
                    "start_date": "2015-01-08T00:00:00+0100",
                    "end_date": null,
                    "subject": null,
                    "abstract": null,
                    "body": "This is the body text",
                    "object_type_id": 22,
                    "created": "2015-01-30T10:04:49+0100",
                    "modified": "2015-05-08T12:59:49+0200",
                    "title": "hello world",
                    "nickname": "hello-world",
                    "description": "the description",
                    "valid": true,
                    "lang": "eng",
                    "rights": "",
                    "license": "",
                    "creator": "",
                    "publisher": "",
                    "note": null,
                    "comments": "off",
                    "publication_date": "2015-01-08T00:00:00+0100",
                    "languages": {
                        "ita": {
                            "title": "ciao mondo"
                        }
                    },
                    "relations": {
                        "attach": {
                            "count": 8,
                            "url": "https://example.com/api/objects/15/relation/attach"
                        },
                        "seealso": {
                            "count": 2,
                            "url": "https://example.com/api/objects/15/relation/seealso"
                        }
                    },
                    "object_type": "Document",
                    "authorized": true,
                    "free_access": true,
                    "custom_properties": {
                        "bookpagenumber": "12",
                        "number": "8"
                    },
                    "geo_tags": [
                        {
                            "id": 68799,
                            "object_id": 218932,
                            "latitude": 44.4948179,
                            "longitude": 11.33969,
                            "address": "via Rismondo 2, Bologna",
                            "gmaps_lookats": {
                                "zoom": 16,
                                "mapType": "k",
                                "latitude": 44.4948179,
                                "longitude": 11.33969,
                                "range": 44052.931589613
                            }
                        }
                    ],
                    "tags": [
                        {
                            "label": "tag one",
                            "name": "tag-one"
                        },
                        {
                            "label": "tag two",
                            "name": "tag-two"
                        }
                    ],
                    "categories": [
                        {
                            "id": 266,
                            "area_id": null,
                            "label": "category one",
                            "name": "category-one"
                        },
                        {
                            "id": 323,
                            "area_id": null,
                            "label": "category two",
                            "name": "category-two"
                        }
                    ]
                }
            },
            "method": "get",
            "params": [],
            "url": "https://example.com/api/objects/15"
        }

    .. note::

        Every object can have relations with other objects. The count of objects
        related is in ``data.object.relations.<relation_name>`` where there are
        ``count`` (the number of related objects) and ``url`` fields. The ``url``
        is the complete API request url to get the object related, for example
        https://example.com/api/objects/15/relations/attach

    If *object_id* corresponds to a **section** or a **publication** then the
    response will contain ``data.object.children`` with the total count of
    children, count of contents, count of sections and the related url.

    .. code-block:: json

        {
            "children": {
                "count": 14,
                "url": "https://example.com/api/objects/1/children",
                "contents": {
                    "count": 12,
                    "url": "https://example.com/api/objects/1/contents"
                },
                "sections": {
                    "count": 2,
                    "url": "https://example.com/api/objects/1/sections"
                }
            }
        }

Get a collection of objects
---------------------------

The ``/objects`` endpoint can be used to retrieve a collection of objects.

Get publication's descendants
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects

    Return a paginated list of objects that are descendants of the
    related publication configured in ``app/config/frontend.ini.php``.
    The response will be an array of objects as shown below.

    :reqheader Authorization: optional :term:`access token`

    :resheader Content-Type: application/json

    :status 200: Success
    :status 400: Malformed request
    :status 401: The request is not authorized to access to protected publication
    :status 403: The request is authorized but without sufficient permissions to access to protected publication

    **Example request**:

    .. sourcecode:: http

        GET /objects HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    For readability the fields of objects are limited to "title" but they are similar to :http:get:`/objects/(object_id)` example

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "objects",
            "data": {
                "objects": [
                    {
                        "id": 2,
                        "title": "title one"
                    },
                    {
                        "id": 3,
                        "title": "title two"
                    },
                    {
                        "id": 4,
                        "title": "title three"
                    },
                    {
                        "id": 5,
                        "title": "title four"
                    },
                    {
                        "id": 6,
                        "title": "title five"
                    }
                ]
            },
            "method": "get",
            "paging": {
                "page": 1,
                "page_size": 5,
                "page_count": 5,
                "total": 50,
                "total_pages": 10
            },
            "params": [],
            "url": "https://example.com/api/objects/1/children"
        }


.. _get-a-list-of-children:

Get object's children
~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/children

    Return the paginated children of object *object_id*.
    The object has to be a section or the publication.

    .. include:: /fragments/objects_related_endpoint_desc.rst

Get object's children of type *section*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/sections

    Return the paginated children of object *object_id*.
    The object has to be a section or the publication.
    The children are just sections (*section BEdita object type*)

    .. include:: /fragments/objects_related_endpoint_desc.rst

Get object's children of type *contents*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/contents

    Return the paginated children of object *object_id*.
    The object has to be a section or the publication.
    The children are other than sections.

    .. include:: /fragments/objects_related_endpoint_desc.rst

Get object's descendants
~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/descendants

    Return the paginated children of object *object_id*.
    The object has to be a section or the publication.
    The children are other than sections.

    .. include:: /fragments/objects_related_endpoint_desc.rst

Get object's siblings
~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/siblings

    Return the paginated siblings of object *object_id*.

    .. include:: /fragments/objects_related_endpoint_desc.rst


.. _get-relations-count:

Get relations count
~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/relations

    Returns a summary of relations information about object *object_id*.
    It shows every relation with the **count** and the **url** to get the related objects detail.

    .. include:: /fragments/objects_related_endpoint_desc.rst

    **Example request**:

    .. sourcecode:: http

        GET /objects/15/relations HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "api": "objects",
            "data": {
                "attach": {
                    "count": 1,
                    "url": "https://example.com/api/objects/1/relations/attach"
                },
                "seealso": {
                    "count": 2,
                    "url": "https://example.com/api/objects/1/relations/seealso"
                }
            },
            "method": "get",
            "params": [],
            "url": "https://example.com/api/objects/1/relations"
        }

.. _get-relations-objects_detail:

Get the related objects detail
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/relations/(string:relation_name)

    Return the paginated collection of objects related by *relation_name* to object *object_id*.

    .. include:: /fragments/objects_related_endpoint_desc.rst

.. _get-the-relation-detail:

Get the relation detail between objects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/relations/(string:relation_name)/(int:related_id)

    Returns the relation detail between object *object_id* and *related_id*.

    :reqheader Authorization: optional :term:`access token`

    .. include:: /fragments/objects_common_params.rst

    :param string relation_name: the name of the relation that ties *object_id* and *related_id*
    :param int related_id: the object id of the related object

    :resheader Content-Type: application/json

    .. include:: /fragments/objects_common_status.rst


    **Example request**:

    .. sourcecode:: http

        GET /objects/15/relations/attach/23 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript


    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
          "api": "objects",
          "data": {
            "priority": 3,
            "params": {
              "label": "here the label"
            }
          },
          "method": "get",
          "params": [],
          "url": "https://example.com/api/objects/1/relations/attach/2"
        }

Get the child position
~~~~~~~~~~~~~~~~~~~~~~

.. http:get:: /objects/(object_id)/children/(int:child_id)

    Return the position (``priority`` key) of *child_id* relative to
    all children of parent object *object_id*

    :reqheader Authorization: optional :term:`access token`

    .. include:: /fragments/objects_common_params.rst

    :param int child_id: the object id of the child of object *object_id*

    :resheader Content-Type: application/json

    .. include:: /fragments/objects_common_status.rst

    **Example request**:

    .. sourcecode:: http

        GET /objects/1/children/2 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
          "api": "objects",
          "data": {
            "priority": 3
          },
          "method": "get",
          "params": [],
          "url": "https://example.com/api/objects/1/children/2"
        }

.. _create_object:

Create an object
----------------

.. http:post:: /objects

    Create a new BEdita object type.

    .. include:: /fragments/objects_writable_important.rst

    The request body has to be a JSON as

    .. code-block:: json

        {
            "data": {}
        }

    where inside ``"data"`` are placed all fields to save.
    User has to be :doc:`authenticated </endpoints/auth>`
    and ``"data": {}`` must contain: ``object_type`` i.e. the
    object type you want to create at least a parent (``parents`` key)
    accessible (with right permission for user authorized) on publication
    tree or at least a relation (``relations`` key) with another object
    reachable (where *reachable* means an accessible object on tree or
    related to an accessible object on tree).

    Required keys in JSON are shown below.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjson string data.object_type: (**required**) the object type to create
    :reqjson array data.parents: (**required** if ``data.relations`` with conditions specified below missing) a list of parents.
        Parents must be accessible (with right permission for user authorized) on publication
        tree
    :reqjson object data.relations: (**required** if ``data.parents`` with conditions specified above missing)
        an object of relations where the keys are the relations' names. Every relation contains an array of objects of ``related_id``
        and optionally of relation params

        .. code-block:: json

            {
                "name1": [
                    {
                        "related_id": 1
                    },
                    {
                        "related_id": 2,
                        "params": {
                            "name_param_one": "value param one",
                            "name_param_two": "value param two"
                        }
                    }
                ],
                "name2": [
                    {
                        "related_id": 3
                    }
                ]
            }

    :resheader Content-Type: application/json
    :resheader Location: The url to the resource just created ``https://example.com/objects/(object_id)``
    :status 201: Success, the object was created. Return the object detail as in :http:get:`/objects/(object_id)`
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

    **Example request**:

    .. sourcecode:: http

        POST /objects HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": {
                "title": "My title",
                "object_type": "event",
                "description": "bla bla bla",
                "parents": [1, 34, 65],
                "relations": {
                    "attach": [
                        {
                            "related_id": 12,
                            "params": {
                                "label": "foobar"
                            }
                        },
                        {
                            "related_id": 23
                        }
                    ],
                    "seealso": [
                        {
                            "related_id": 167
                        }
                    ]
                },
                "categories": ["name-category-one", "name-category-two"],
                "tags": ["name-tag_one", "name-tag-two"],
                "geo_tags": [
                    {
                        "title": "geo tag title",
                        "address": "via ....",
                        "latitude": 43.012,
                        "longitude": 10.45
                    }
                ],
                "date_items": [
                    {
                        "start_date": "2015-07-08T15:00:35+0200",
                        "end_date": "2015-07-08T15:00:35+0200",
                        "days": [0,3,4]
                    },
                    {
                        "start_date": "2015-09-01T15:00:35+0200",
                        "end_date": "2015-09-30T15:00:35+0200"
                    }
                ]
            }
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 201 Created
        Content-Type: application/json

        {
          "api": "objects",
          "data": {
              "id": 45,
              "title": "My title",
              "object_type": "event",
              "description": "bla bla bla"
          },
          "method": "post",
          "params": [],
          "url": "https://example.com/api/objects"
        }

    The response payload contains the created object detail. *In the example above only some fileds are shown*.

    dates must be in ISO 8601 format.

Update an object
----------------

.. http:post:: /objects

    Update an existent object.

    .. include:: /fragments/objects_writable_important.rst

    :http:method:`post` request can be also used to **update an existent object**. In that case the
    object ``id`` has to be passed in ``"data"`` (as :ref:`creating object <create_object>`) and ``object_type`` can be omitted.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjson string data.id: (**required**) the id of the object to update

    :resheader Content-Type: application/json
    :status 200: Success, the object was updated. Return the object detail as in :http:get:`/objects/(object_id)`
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

Create or update relations between objects
------------------------------------------

.. http:post:: /objects/(object_id)/relations/(string:relation_name)

    Create relations *relation_name* between *object_id* and other objects.
    If the relation between objects already exists then it will be updated.

    Request data must be an array of relation data or only a
    relation data if you need to save only one relation.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjsonarr string related_id: (**required**) the related object id
    :reqjsonarr string params: (**optional**) it depends from relation type
    :reqjsonarr string priority: (**optional**) is the position of the relation.
        Relation with lower priority are shown before.

    :resheader Content-Type: application/json
    :resheader Location: If at least a new relation was created (:http:statuscode:`201`).
        The url to :ref:`collection of related objects <get-relations-objects_detail>`
    :status 200: Success but no new relation was created.
    :status 201: Success and at least a new relation was created.
        Return the object detail as in :http:get:`/objects/(object_id)`
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

    **Example request to create/update only one relation**:

    .. sourcecode:: http

        POST /objects/3/relations/attach HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": {
                "related_id": 34,
                "priority": 3
            }
        }

    **Example request to create/update a bunch of relations**:

    .. sourcecode:: http

        POST /objects/3/relations/attach HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": [
                {
                    "related_id": 15,
                    "params": {
                        "label": "my label"
                    }
                },
                {
                    "related_id": 28
                }
            ]
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 201 Created
        Host: example.com
        Location: https://example.com/objects/3/relations/attach
        Accept: application/json, text/javascript
        Content-Type: application/json

    The response body will be the same as :http:get:`/objects/(object_id)/relations/(string:relation_name)`


Replace relation data between objects
--------------------------------------

.. http:put:: /objects/(object_id)/relations/(string:relation_name)/(int:related_id)

    Replace the relation *relation_name* data between *object_id* and *related_id*.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjson string related_id: (**required**) the related object id
    :reqjson string params: (**optional**) it depends from relation type
    :reqjson string priority: (**optional**) is the position of the relation.
        Relation with lower priority are shown before.

    :resheader Content-Type: application/json
    :status 200: Success
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

    At least ``params`` or ``priority`` must be defined. If one of these is not passed it will be set to ``null``.

    **Example request**:

    .. sourcecode:: http

        PUT /objects/1/relations/attach/2 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": {
                "priority": 3,
                "params": {
                    "label": "new label"
                }
            }
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 Success
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

    The response body will be the same as :http:get:`/objects/(object_id)/relations/(string:relation_name)/(int:related_id)`

Add or edit children
--------------------

.. http:post:: /objects/(object_id)/children

    Add or edit children to area/section object type  identified by *object_id*

    Request data must be an array of child data or only a
    child data if you need to save only one child.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjsonarr string child_id: (**required**) the child object id
    :reqjsonarr string priority: (**optional**) is the position of the child on the tree.
        Relation with lower priority are shown before.

    :resheader Content-Type: application/json
    :resheader Location: If at least a new child was created (:http:statuscode:`201`)
        it contains the url to :ref:`collection of children objects <get-a-list-of-children>`.
    :status 200: Success but all objects already were children of *object_id*.
        The children position (``priority``) could be changed.
        Response body is the children detail :http:get:`/objects/(object_id)/children`
    :status 201: Success and at least a new child was added to parent *object_id*.
        Response body is the children detail :http:get:`/objects/(object_id)/children`.
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

    **Example request to add/edit many children**:

    .. sourcecode:: http

        POST /objects/3/children HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": [
                {
                    "child_id": 15,
                    "priority": 3
                },
                {
                    "child_id": 28
                }
            ]
        }

    **Example request to add/edit one child**:

    .. sourcecode:: http

        POST /objects/3/children HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": {
                "child_id": 34,
                "priority": 3
            }
        }

    The response body will be the same as :http:get:`/objects/(object_id)/children`

Update child position
---------------------

.. http:put:: /objects/(object_id)/children/(int:child_id)

    Change the child position inside the children of *object_id*.

    :reqheader Authorization: (**required**) :term:`access token`

    :reqjson string priority: (**required**) the position of child object id

    :resheader Content-Type: application/json
    :status 200: Success. Children position (``priority``) updated.
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized

    **Example request**:

    .. sourcecode:: http

        POST /objects/1/children/2 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript
        Content-Type: application/json

        {
            "data": {
                "priority": 5
            }
        }

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
          "api": "objects",
          "data": {
            "priority": 5
          },
          "method": "get",
          "params": [],
          "url": "https://example.com/api/objects/1/children/2"
        }

Delete an object
----------------

.. http:delete:: /objects/(object_id)

    Delete the object *object_id*

    :reqheader Authorization: (**required**) :term:`access token`

    :resheader Content-Type: application/json
    :status 204: The object was deleted.
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized
    :status 404: The object to delete not exists or it has already been removed

    **Example request**:

    .. sourcecode:: http

        DELETE /objects/15 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 204 No Content

Delete a relation between objects
---------------------------------

.. http:delete:: /objects/(object_id)/relations/(string:relation_name)/(int:related_id)

    Delete the relation *relation_name* between *object_id* and *related_id*

    :reqheader Authorization: (**required**) :term:`access token`

    :resheader Content-Type: application/json
    :status 204: The relation *relation_name* between *object_id* and *related_id* was deleted.
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized
    :status 404: The relation *relation_name* between *object_id* and *related_id*
        not exists or it has already been removed

    **Example request**:

    .. sourcecode:: http

        DELETE /objects/10/relations/seealso/36 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 204 No Content

Remove a child from a parent
----------------------------

.. http:delete:: /objects/(object_id)/children/(int:child_id)

    Remove *child_id* from *object_id* children

    :reqheader Authorization: (**required**) :term:`access token`

    :resheader Content-Type: application/json
    :status 204: *child_id* was removed from *object_id* children.
    :status 400: Required parameters are missing or the request is malformed
    :status 401: Request is not authorized
    :status 404: *child_id* not exists or it has already been removed from *object_id* children.

    **Example request**:

    .. sourcecode:: http

        DELETE /objects/1/children/3 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 204 No Content
