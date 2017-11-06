==========================
 Path Expression Variants
==========================

In TALES 1.0, there are two optional expression types which are
variations on the path expression type. The 'exists' type returns a
boolean value indicating whether the path is defined, and 'nocall'
resolves the path without trying to render the resulting value.

These don't need to clutter up the expression type namespace. Instead,
since the root of a path is always a simple name and cannot contain
spaces, we can make variant path expressions by prefixing the path
with the modifier name, separated by a space. For example::

    exists request/arg
    nocall modules/ZTUtils/TreeMaker
    if exists request/opt_arg

These can also be written with an explicit type, of course, as in::

    path:exists request/arg
