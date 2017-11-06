==============================
 Variables Expansion In Paths
==============================

.. highlight:: html

Problem
=======

TALES expression syntax provides variable path expansion in strings
using the ``${path/to/variable}`` syntax. However, this syntax is
*not* allowed when constructing paths. (While this behavior can be
simulated using ``python:`` strings, it is not intuitive why variable
expansion should work in one case but not in another.)

For example, this works currently::

  <li tal:repeat="item here/queryIds">
    <a tal:attributes="href string:here/subfolder/${item/id}">Link</a>
  </li>

whereas this does not::

  <li tal:repeat="item here/queryIds">
    <span tal:content="path:here/subfolder/${item/id}/method" />
  </li>

Proposal
========

Allow for variable path expansion to occur when constructing paths.
The most flexible way to do this might be to simply treat paths as
strings, perform any expansions, and then traverse the resulting
expression. The new syntax for path would be expressed as::

  PathExpr  ::= Path [ '|' Path ]*
  Path      ::= string_expression

where string_expression is already defined in :doc:`../tal`
as::

  string_expression ::= ( plain_string | [ varsub ] )*
  varsub            ::= ( '$' Path ) | ( '${' Path '}' )
  plain_string      ::= ( '$$' | non_dollar )*
  non_dollar        ::= any character except '$'

This would allow nested expansions::

  <span tal:replace="path:here/${folder${item/id}}/method" />

as well as allowing the initial path variable to be, well, a
variable::

  <span tal:replace="${where}/object" />

Although if deemed undesirable, this usage could be disallowed while
still allowing path expansion generally.

Rationale for Rejection
=======================

(Explanation from JimFulton.)

First of all, I see no justification for the generality of full string
interpolation, with interpolated values treated as sub-expressions.
The example in the proposal doesn't require this generality. No one
has given any convincing use cases why this (full string
interpolation) is needed.

I do support making the current undocumented syntax official. The
current syntax allows individual path segments to be retrieved from
variables, as in::

  here/folder/?id

Characters in the path segment variable are given no special meaning.
This is especially useful if the object being traversed is a mapping
object. A mapping object may have keys that contain special
characters, like '/'s and '|'s. It's important that these characters
be used as data. Otherwise the mapping items would be inaccessible.
