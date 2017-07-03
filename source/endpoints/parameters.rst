Common GET parameters
=====================

Many HTTP query parameters in BEdita4 API GET method calls are common and may be used to manipulate API response and get only the data you need in your application.

Common parameters described here may be used in all API endpoints that handle entities/objects directly.
They are available in all endpoints except: ``/auth``, ``/home`` and ``/admin``


.. _get-pagination:

Pagination
-----------

  * ``page`` - select page number to retrieve
  * ``page_size`` - define page size; default page size and maximum possible size are defined via configuration parameter (defaults are 20 and 100) see :ref:`configuration-pagination`

Examples:

   *  ``/objects?page=2`` - get page number 2
   *  ``/objects?page_size=42`` - impose 42 as page size in response
   *  ``/objects?page_size=42&page=3`` - get page number 3 using 42 as page size

In every GET response containing lists pagination data is available in a special section under ``meta / pagination``

   **Example response**:

   Suppose we have 125 items available for our request, using 20 as ``page_size``, selecting page number 7 we get

    .. sourcecode:: javascript

        {
            "links": {
                ...
            },
            "data": {
                ...
            },
            "meta": {
                "pagination": {
                    "count": 125,
                    "page": 7,
                    "page_count": 7,
                    "page_items": 5,
                    "page_size": 20
                }
            }
        }

Where, as you may easily guess:

 * ``count`` is the total number of items in response
 * ``page`` is the current page number
 * ``page_count`` is the total number of pages
 * ``page_items`` is the numer of items displayed in this page (maximum is ``page_size``)
 * ``page_size`` is the page size, max number of items for each page


Field selection
---------------

 * ``fields`` - decide which fields are displayed in a response, comma separeted list


Search
------

  * ``q`` or ``query`` - perform a natural language search


Sort
----

  * ``sort`` - sort items using a field in ascending or descending order, descending order is obtained prepending minus ``-`` to field name


Examples:

   *  ``/users?sort=username`` return users alphabetically by username
   *  ``/users?sort=-username`` return users alphabetically from last to first by username (reverse order)
   *  ``/roles?sort=-id`` roles from last to first id

Filters
-------

[TBD]
