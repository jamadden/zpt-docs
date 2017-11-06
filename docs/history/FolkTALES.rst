========================================================
 Obsolete Template Attribute Language Expression Syntax
========================================================

.. note:: This is the former syntax. See :doc:`../tales` for the new
          syntax.

TALES is the recommended syntax for :doc:`../tal` expressions. It is
an extensible syntax that allows you to define any number of
expression types and use them together. A TALES implementation has a
set of expression types with associated handlers. It is the handlers
that determine the form and meaning of each type of expression.

A TALES expression is a string with an optional type prefix, which is
a simple name followed by a colon. A simple name is a string
containing only letters, numbers, and underscores ('_'). If an
expression has a type prefix, the simple name is the expression type,
otherwise the expression type is 'standard'. Examples::

    python: 1 + 2
    standard:/a/b/c
    /a/b/c
    str:Hello, ${username}

To evaluate a TALES expression, you determine the expression type
(removing the type prefix, if any), look up the handler, and pass the
expression to the handler along with a reference to the evaluator. The
handler can use this reference to evaluate subexpressions.

Presentation Templates
======================

The implementation of :doc:`PresentationTemplates` uses TAL with TALES, and
defines a standard set of expression types.  The 'standard' type
of expression is the *Path Expression*.

Path Expressions
----------------

A path expression consists of one or more strings separated by
slashes. The first string is the **context name**, and must be a
simple name. The remaining **path segments** may contain any character
except slash ('/'). For example::

         request/cookies/oatmeal
         template
         here/Oh, my &@#*^! What a terrible name!/foo
         /path/from/the/root

When a Presentation Template evaluates a path expression, it looks up
the context name in a registry to get a starting object. If there are
path segments, it traverses from the current object to the next object
using each path segment in turn. If the resulting object is callable,
it is called. There will be a syntax (prefixing the path with
'nocall:', for instance) for returning the raw object.

Since context names are the starting places from which you can find
various objects and values, they are the fundamental building block of
path expressions.

Context Names in Presentation Templates
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PresentationTemplates define the following context names:

- The null context name (when a path starts with a slash) refers to
  the Zope root object.
- *here* - the object to which the template is being applied.
- *container* - the template's container object.
- *template* - the template itself.
- *request* - the zope publishing request object.
- *node* - an object representing the current XML node.  This can
  be used to access attributes, or children.
- *nothing* - special TAL singleton value (Python 'None').

Other Expression Types
----------------------

- *local* - treat the text as a path expression, looking the
  context name up in the local variable namespace.
- *global* - treat the text as a path expression, looking the
  context name up in the global variable namespace.
- *var* - treat the text as a path expression, looking the
  context name up in first the local, then the global variable
  namespaces.
- *loop* - treat the text as a path expression, looking the
  context name up in the loop variable namespace.
- *python* - interpret the text as restricted Python code.
- *str* - interpret the text as a literal string.
- *not* - interpret the text as a full expression, and return
  the boolean negation of its value.  In other words, 'not:' can
  be followed by any expression, for example 'not:var:x'.

Potential Future Expression Types
---------------------------------
- *perl*
- *xpath*

Comments
========


gvanrossum (Jan 29, 2001 8:19 pm; Comment #1)  --
           - *var* - treat the text as a path expression, looking the
             context name up in first the local, then the global variable
             namespaces.

  Do we really need this as well as *local*? I'd suggest to use these
  semantics for *local*. These are the semantics used by Python (first
  look up locals, then globals) and they work very well.

 Note: I believe that the specs here don't clarify that local
 variables defined in outer scopes remain visible in inner scopes
 unless redefined in an inner scope. That is of course an essential
 property -- and it is the semantics chosen by Python 2.1 for nested
 scopes. (Making the globals the outermost scope also suggests that
 having both *var* and *local* is redundant.)

evan (Jan 30, 2001 3:07 pm; Comment #3)  --
   Do we really need this as well as *local*?

 *local* allows us to explicitly state that we expect a local
 variable, and want a name error if it isn't there. *var* will
 presumably be the most commonly used namespace. Global are the
 outermost scope, but they are also explicitly accessible through
 *global* regardless of intervening *local* definitions.
