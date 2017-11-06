=========================
 Act Only If Path Exists
=========================


.. note:: This has been superceded by :doc:`EliminatePathModifiers`.

Problem
=======

Often you want to replace part of a template with information from the
environment that may not always be present. For example, you might
want to fill in data from the 'request' object, but provide default
content if 'request' doesn't contain the data.

Proposal
========

Allow a path expression to be marked so that it catches traversal
exceptions, and cancels the TAL or METAL action in which it is used.
If no exception is raised, continue normally.

When a statement is cancelled, the effect is the same as if the
statement did not exist. In the case of 'define' and 'attribute'
statements with more than one part, only the part containing the
cancelling expression is affected, but if all parts are cancelled, the
entire statment is cancelled.

A cancelled 'replace', 'attributes', 'content', 'repeat', or
'use-macro' statement leaves the text that would have been overwritten
alone. A cancelled 'define' statement does not define a variable.

Given the following statements::

    <span tal:replace="if exists request/x">No X</span>
    <span tal:condition="if exists request/x">X</span>
    <a href="/" tal:attribues="href if exists request/x;
       target if exists request/y">

A request without an 'x' or 'y' would produce::

    <span>No X</span>

    <a href="/">

A request with 'x' containing the string "X!" would produce::

    X!
    <span>X</span>
    <a href="X!">

Without the 'if exists', each statement would produce the same result
in the second case, but cause an error in the first.
