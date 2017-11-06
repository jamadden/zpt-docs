=================================
 TALES Specification Version 1.2
=================================

This specification supercedes :doc:`TALESSpecification10`. It
incorporates :doc:`ModuleAccessInTALES`, :doc:`ActOnlyIfPathExists`,
and :doc:`PathExpressionVariants`.

The *Template Attribute Language Expression Syntax* describes
expressions that may be used to supply :doc:`TAL` and :doc:`METAL`
with data. TALES is *one* possible expression syntax for these
languages, but they are not bound to this definition. Similarly, TALES
could be used in a context having nothing to do with :doc:`TAL` or
:doc:`METAL`.

TALES expressions are described below with any delimiter or quote
markup from higher language layers removed, since this is how
expression strings must be passed to the TALES engine. Here is the
basic definition of TALES syntax::

  Expression  ::= [type_prefix ':'] String
  type_prefix ::= Name

*See [EBNF] for rules and terminals.*
Here are some simple examples::

  a/b/c
  path:a/b/c
  nothing
  path:nothing
  python: 1 + 2
  string:Hello, ${username}

The optional *type prefix* determines the semantics and syntax of the
*expression string* that follows it. A given implementation of TALES
can define any number of expression types, with whatever syntax you
like. It also determines which expression type is indicated by
omitting the prefix.

Several expression types are required:

Required Type Prefixes
======================

 - *not* - evaluate the expression string (recursively) as a full
   expression, and returns the boolean negation of its value. If the
   expression supplied does not evaluate to a boolean value, *not* will
   issue a warning and *coerce* the expression's value into a boolean type
   based on the following rules:

   1. integer 0 is *false*
   2. integer > 0 is *true*
   3. an empty string or other sequence is *false*
   4. a non-empty string or other sequence is *true*
   5. a *non-value* (e.g. void, None, Nil, NULL, etc) is *false*
   6. all other values are implementation-dependent.

   If no expression string is supplied, an error should be generated.

 - *path* - interpret the expression string as the path to some
   *object*. The syntax and semantics of paths warrant their own
   section and thus are described below (see `Path Expressions`) If no
   expression string is supplied, the result is interpreted as the
   value *nothing*.

 - *string* - interpret the expression string as text. If no expression
   string is supplied the resulting string is *empty*. The string can
   contain variable substitutions of the form '$name' or '${name}', where
   'name' is an expression of type 'path'.  The escaped string value of
   the path expression is inserted into the string. To prevent a '$' from
   being interpreted this way, it must be escaped as '$$'.

    Syntax::

     string_expression ::= ( plain_string | [varsub] )*
     varsub            ::= ( '$' Path ) | ( '${' Path '}' )
     plain_string      ::= ( '$$' | non_dollar )*
     non_dollar        ::= any character except '$'


Optional Type Prefixes
======================

 - *python* - interpret the expression string as restricted Python
   code. This code must be a legitimate python expression.

Path Expressions
================

A path expression consists of a **path**, preceded by an optional set
of **path modifiers** in parenthesis. A **path** consists of one or
more non-empty strings separated by slashes. The first string must be
a variable name, and the remaining strings, the **path segments**, may
contain letters, digits, spaces, and the punctuation characters
underscore, dash, period, comma, and tilde.

The **path modifiers** may include 'if', 'exists', and 'nocall' in any
combination.

Here is the syntax::

       Path      ::= [ modifiers ] variable [ '/' URL_Segment ]*
       modifiers ::= '(' [ 'if' | 'exists' | 'nocall' ]* ')'
       variable  ::= Name

For example::

       request/cookies/oatmeal
       template
       nothing
       (exists) here/some-file 2001_02.html.tar.gz/foo
       (if nocall) root/to/branch

When a TALES path expression is evaluated, it first looks up the
variable name. If there are path segments, it traverses from the
current object (starting with the variable value) to the next object
using each path segment in turn. If the resulting object is callable,
and the path modifier 'nocall' was not used, the object is called. The
semantics of traversal (and what it means to be callable) are
implementation-dependent.

If a traversal step fails, a traversal error is raised unless the
'exists' modifier is used. When a path expression has both the 'if'
and 'exists' modifier, as in '(if exists) a/b/c', the only effect is
not cancel the action (see :doc:`TAL`) in case of a traversal error.
Used by itself, 'exists' causes the expression to evaluate to '1' if
traversal succeeds, or '0' if it fails. The 'if' modifier can also be
used alone, in which case it does not handle traversal errors, but
cancels the action if the path value is false.

Since every path must start with a variable name, you need a set of
starting variables that you can use to find other objects and values.
PresentationTemplates define the variable names listed below. Since
variable names are looked up first in locals, then in globals, then in
this list, these names act just like builtins in Python; They are
always available, but they can be shadowed by a global or local
variable declaration. You can always access the builtin names
explicitely by prefixing them with *standard*. (e.g. standard/root,
standard/standard, etc).

Builtin Names in Presentation Templates
=======================================
- *nothing* - special singleton value used by TAL to represent
  a *non-value* (e.g. void, None, Nil, NULL).
- *options* - the *keyword* arguments passed to the template.
- *repeat* - the 'repeat' variables (see :doc:`TAL`).
- *standard* - the list of standard names (this list).

Optional Names in Presentation Templates
========================================
- *root* - the system's top-most object.
- *here* - the object to which the template is being applied.
- *container* - the template's container object.
- *template* - the template itself.
- *request* - the publishing request object.
- *modules* - a collection of modules providing additional functions.
