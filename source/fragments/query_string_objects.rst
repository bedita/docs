:query string filter[object_type]: the object type or a comma separated list of
    object types requested
:query string filter[query]: used for fulltext search
:query string embed[relations]: used for embedding related objects in ``relations`` key.
    For example ``embed[relations]=attach|3,seealso|2`` will embed 3 objects related by "attach"
    and 2 related by "seealso" to main object. If no number is specified then only one relation
    will be embed i.e. ``embed[relations]=poster`` is the same of ``embed[relations]=poster|1``.
    See the :ref:`example <embed-relations-example>`.
:query int page: the page requested
:query int page_size: the page dimension
