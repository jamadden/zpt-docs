==========================
 Eliminate Path Modifiers
==========================

.. from https://github.com/zopefoundation/zpt-docs/blob/master/src/EliminatePathModifiers.stx

.. highlight:: html

The path modifiers of TALES 1.2 were introduced in an attempt to
provide simple spellings for two common capabilities:

1. Choose between a path value and the default text, based on a boolean test.
2. Deal well with invalid paths.

Several people (including the implementor) have found path modifiers
to be confusing and inadequate. In a three hour meeting between Jim
Fulton, Guido van Rossum, and Evan Simpson, the following proposed
changes were worked out.

Features That Have Been Implemented
===================================

* Add the alternation operator ``|`` to path expressions.

  A path expression can now contain multiple path separated by ``|``.  The value
  of the expression is the value of the first path that exists (proceeding
  from left to right). Spaces surrounding a ``|`` are not significant.  If none
  of the paths exist, then the result is the same as if only the final path
  appeared in the expression.

  For example::

    <title tal:replace="request/title | here/title">Untitled</title>

* Add new builtin variable ``default`` that can be used to keep default text.

  The ``default`` variable, like the ``nothing`` variable, contains a special
  value that affects TAL statements.  Where ``nothing`` causes
  the ``replace``, ``content``, and ``attributes`` statements to delete their
  targets, ``default`` causes them to leave the existing target text alone.
  It is most useful when combined with the alternation operator.

* Add ``path`` and ``exists`` functions to Python expressions.

  We decided to relegate more complex logic that tests for path existence
  to Python expressions, and make Python expressions more capable of dealing
  with paths.

  The ``path`` function takes a string, evaluates it in exactly the same way
  that the path expression type would, and returns the result.  If the string
  doesn't resolve to an existing path, the result is an error object.

  The ``exists`` function takes a string, tries to treat it as a path, and
  returns true if it is a valid path, false if it isn't.

  Examples::

    <div tal:condition="python:exists('a/b/c')"
         tal:replace="a/b/c/d" />
    <div tal:replace="python:path('a/b') + 1" />

Features That Are Not Implemented
=================================

The following items have not yet been implemented:

* Eliminate path modifiers.

* Spell ``nocall`` by adding a trailing slash to a path.

  This means that ``modules/string/join/`` will get you the ``join`` function,
  instead of an error about calling it with too few arguments.

* Provide an alternation operator ``||`` which works like ``|``, except that
  it treats the preceding path as non-existent if its value is false.  Whether
  this use-case is significant enough to merit its own syntax is unknown.

As alternatives to these, we could keep only the path modifiers ``if``
and ``nocall``. With ``if``, we can write "(if) a | b" instead of ``a
|| b``.

Comments
========

wleftwich (May 17, 2001 1:04 pm; Comment #2) *Editor Remark Requested* --

 Giving the trailing slash a special meaning seems like asking for
 trouble, since the mental model (mine anyway) is a filesystem path.
 (I have a problem with the double slash in XSLT also.)

 How about moving nocall into a python function instead?

gotcha (May 18, 2001 3:48 am; Comment #3)  --
 I totally support the comment on the trailing slash...

 I think also it would be far too easy not to see a missing trailing slash...
