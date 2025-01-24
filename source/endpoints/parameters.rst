Common GET parameters
=====================

Many HTTP query parameters in BEdita API GET method calls are common and may be used to manipulate API response and get only the data you need in your application.

Common parameters described here may be used in all API endpoints that handle entities/objects directly.

They are available in all endpoints except few special ones like: ``/auth``, ``/home``, ``/signup`` and ``/status``. These endpoints are designed to handle particular use cases where lists of resources or objects are not directly handled.

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

  * ``q`` or ``filter[query]`` - perform a natural language search using a filter, see :ref:`filter-search` below


Sort
----

  * ``sort`` - sort items using a field in ascending or descending order, descending order is obtained prepending minus ``-`` to field name


Examples:

   *  ``/users?sort=username`` return users alphabetically by username
   *  ``/users?sort=-username`` return users alphabetically from last to first by username (reverse order)
   *  ``/roles?sort=-id`` roles from last to first id

.. _get-filters:

Filters
-------

Filters are one the most powerful and versatile tools in **BEdita API** in order to retrieve only resources or objects you really need in  your application.

Filter expressions
^^^^^^^^^^^^^^^^^^

Generally every filter in a query string will have following syntax:

  1. ``filter[{item}]={value}`` the simplest form, a filter item should be equal to some value
  2. ``filter[{item}][{op}]={value}`` is a logical operation on filter value, where ``{op}`` can have following values:

    * ``neq``, ``ne``, ``'!=`` or ``<>`` negation operator, meaning that filter item shoud not be equal to ``{{value}}``
    * ``lt`` or ``<`` less than operator, meaning that filter item shoud be less than ``{{value}}``
    * ``lte``, ``le`` or ``<=`` less than or equal operator; filter item shoud be less than or equal to ``{{value}}``
    * ``gt`` or ``>`` greater than operator; filter item shoud be greater than ``{{value}}``
    * ``gte``, ``ge`` or ``>=`` greater than or equal operator; filter item should be greater than or equal to ``{{value}}``

  3. ``filter[{item}][]={value}`` array sintax: add a value to an array representing possible values of a filter item

Filter expressions can be combined using ``&`` separator as many times as you want, limited only by the URL size.

Too many ``filter`` combinations may of course result in unwanted or meaningless results, use them with caution :)

.. _filter-field:

Field filter
^^^^^^^^^^^^

The simplest and most common filter: retrieve only resources that have a field equal to some value, or greater/less than some value.

Examples:

   *  ``/users?filter[name]=Gustavo`` get users that have *Gustavo* as first ``name``
   *  ``/objects?filter[id][gt]=100`` return users with ``id`` greater than 100

.. _filter-search:

Search Query filter
^^^^^^^^^^^^^^^^^^^

Simple text search may be performed with a query filter

   *  ``/objects?filter[query]=gustavo`` get objects that have *gustavo* in some of their textual fields
   *  ``/objects?q=gustavo`` convience alias for the preceeding filter query - ``filter[query]=..`` or ``q=..`` are identical

**Note**: currently only raw text search is performed, more sophisticated actual natural language search will be available in a future release

.. _filter-type:

Type filter
^^^^^^^^^^^

Type filters are used to select some :term:`object` types, typically used in ``/objects`` endpoint

   *  ``/objects?filter[type]=events`` select only objects of type *events*
   *  ``/objects?filter[type][ne]=documents`` all object types except *documents*
   *  ``/objects?filter[type][]=locations&filter[type][]=profiles`` select only *locations* and *profiles*

Geo filter
^^^^^^^^^^

Geo filters are able to retrieve results on objects of type ``location`` or on types extending ``locations`` using geo coordinates.

   *  ``/locations?filter[geo][center]=44.4944183,11.3464055`` retrieve locations ordered by proximity to a given ``center`` point expressed in terms of latitude and longitude; each item will show in ``meta.extra.distance`` the distance in meters to the ``center`` point
   *  ``/locations?filter[geo][center]=44.4944183,11.3464055&filter[geo][radius]=5000`` same as the above filter, but results are limited in a radius of 5km

**Note**: in order to work this filter **requires** that the underlying database supports geo-spatial functions like ``ST_GeomFromText``, this is true for **MySQL 5.7** or **PostGIS** for instance.

Date ranges filter
^^^^^^^^^^^^^^^^^^

Date ranges are entities used in some objects like ``events`` to indicate start and end dates.

With this filter you are able to retrieve objects using conditions on date time intervals.

  *  ``/events?filter[date_ranges][start_date][gt]=2017-08-01`` events starting after ``2017-08-01``
  *  ``/events?filter[date_ranges][end_date][le]=2017-08-15`` events with end date lesser than or equal to ``2017-08-15``
  *  ``/events?filter[date_ranges][start_date][gt]=2017-07-01&filter[date_ranges][end_date][lt]=2017-07-30`` events starting and ending between ``2017-07-01`` and ``2017-07-30``
