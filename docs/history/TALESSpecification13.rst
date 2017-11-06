=================================
 TALES Specification Version 1.3
=================================

This specification supercedes :doc:`TALESSpecification12`.

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

*See :doc:`EBNF` for rules and terminals.*

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
  issue a warning and *coerce* the expression's value into a boolean
  type based on the following rules:

    1. integer 0 is *false*
    2. integer > 0 is *true*
    3. an empty string or other sequence is *false*
    4. a non-empty string or other sequence is *true*
    5. a *non-value* (e.g. void, None, Nil, NULL, etc) is *false*
    6. all other values are implementation-dependent.

  If no expression string is supplied, an error should be generated.

- *path* - interpret the expression string as the path to some
  *object*. The syntax and semantics of paths warrant their own section
  and thus are described below (see [Path Expressions]) If no expression
  string is supplied, the result is interpreted as the value *nothing*.

- *string* - interpret the expression string as text. If no expression
  string is supplied the resulting string is *empty*. The string can
  contain variable substitutions of the form '$name' or '${name}',
  where 'name' is an expression of type 'path'. The escaped string
  value of the path expression is inserted into the string. To prevent
  a '$' from being interpreted this way, it must be escaped as '$$'.
  Note that leading and trailing spaces in the expression are included
  in the value.

  Syntax::

     string_expression ::= ( plain_string | [ varsub ] )*
     varsub            ::= ( '$' Path ) | ( '${' Path '}' )
     plain_string      ::= ( '$$' | non_dollar )*
     non_dollar        ::= any character except '$'


Optional Type Prefixes
======================

- *python* - interpret the expression string as restricted Python
  code. This code must be a legitimate python expression.

Path Expressions
================

A path expression consists of one or more **paths** separated by
vertical bars (|). A **path** consists of one or more non-empty
strings separated by slashes. The first string must be a variable
name, and the remaining strings, the **path segments**, may contain
letters, digits, spaces, and the punctuation characters underscore,
dash, period, comma, and tilde.

Here is the syntax::

       PathExpr  ::= Path [ '|' Path ]*
       Path      ::= variable [ '/' URL_Segment ]*
       variable  ::= Name

For example::

       request/cookies/oatmeal
       nothing
       here/some-file 2001_02.html.tar.gz/foo
       root/to/branch | default

When a TALES path expression is evaluated, it attempts to traverse
each path, from left to right, until it succeeds or runs out of paths.
To traverse a path, it first fetches the object stored in the
variable. For each path segment, it traverses from the current object
to the subobject named by the path segment.

Once a path has been successfully traversed, the resulting object is
the value of the expression. If it is a callable object, such as a
method or class, it is called. The semantics of traversal (and what it
means to be callable) are implementation-dependent.

If a traversal step fails, evaluation immediately proceeds to the next
path.  If there are no further paths, an error results.

Since every path must start with a variable name, you need a set of
starting variables that you can use to find other objects and values.
PageTemplates define the variable names listed below. Since variable
names are looked up first in locals, then in globals, then in this
list, these names act just like builtins in Python; They are always
available, but they can be shadowed by a global or local variable
declaration. You can always access the builtin names explicitely by
prefixing them with *CONTEXTS*. (e.g. CONTEXTS/root, CONTEXTS/nothing,
etc).

Builtin Names in Page Templates
===============================
- *nothing* - special singleton value used by TAL to represent
  a *non-value* (e.g. void, None, Nil, NULL).
- *default* - special singleton value used by TAL to specify that
  existing text should not be replaced.
- *options* - the *keyword* arguments passed to the template.
- *repeat* - the 'repeat' variables (see RepeatVariable).
- *attrs* - a dictionary containing the initial values of the
  attributes of the current statement tag.
- *CONTEXTS* - the list of standard names (this list).  This can be
  used to access a builtin variable that has been hidden by a local
  or global variable with the same name.

Optional Names in Page Templates (used in Zope Page Templates)
==============================================================
- *root* - the system's top-most object.
- *here* - the object to which the template is being applied.
- *container* - the template's container object.
- *template* - the template itself.
- *request* - the publishing request object.
- *user* - the authenticated user object.
- *modules* - a collection through which all Python modules and
  packages can be accessed.  Some or many of these may not be
  usable in TALES, however, depending on the security policies
  of the template's implementation.


Comments
========


jschinnerer (Nov 2, 2001 3:59 am; Comment #2)  --
 **Relative Paths**

 Deceptively simple but not explicitly spelled out in the *Path
 Expressions* section. A *very* common filesystem command form
 *doesn't* work in ZPT paths and confused me for a bit. Key phrase is
 this one:


         Since every path must start with a variable name, ...


 so this works (to go up a node, then out another branch):

 'container/../down_one/got_it'

 but this does not:

 '../down_one/got-it'

hathawsh (Apr 19, 2002 3:24 pm; Comment #3)  --
 This spec should describe the following path expression syntax::

   <span tal:content="here/?myvar">

 The 'here/?myvar' expression is evaluated by traversing from 'here'
 to the name given by the value associated with 'myvar'. For example,
 if 'myvar' is set to "title", 'here/?myvar' is equivalent to
 'here/title'.

bwarsaw (Jun 3, 2002 1:29 pm; Comment #4)  --

   1. integer 0 is *false*
   2. integer > 0 is *true*

 what about integers < 0? :)

chrisw (Jul 18, 2002 3:04 am; Comment #5)  --
 where is the 'structure' prefix documented?
 I reckon it should probably be in here...


From djmaze Sun Nov 8 17:45:59 -0500 2009
  "path segments, may contain letters, digits, spaces, and the
  punctuation characters underscore, dash, period, comma, and tilde."
  Which dash are you talking about? http://en.wikipedia.org/wiki/Dash

  Or are you talking about Hyphen-minus (U+002D)?

From FredDrake Mon Nov 9 07:33:48 -0500 2009
  The hyphen-minus, which is what most programmers refer to as the dash.
