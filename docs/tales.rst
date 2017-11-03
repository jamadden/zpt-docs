================
 TALES Referenc
================

.. highlight:: html

The *Template Attribute Language Expression Syntax* (TALES) standard describes
expressions that supply TAL and METAL with data. TALES is *one* possible
expression syntax for these languages, but they are not bound to this
definition. Similarly, TALES could be used in a context having nothing to do
with TAL or METAL.

Introduction
============

TALES expressions are described below with any delimiter or quote markup from
higher language layers removed. Here is the basic definition of TALES
syntax:

.. code-block:: abnf

  Expression  ::= [type_prefix ':'] String
  type_prefix ::= Name

Here are some simple examples:

.. code-block:: text

  a/b/c
  path:a/b/c
  nothing
  path:nothing
  python: 1 + 2
  string:Hello, ${user/getUserName}

The optional *type prefix* determines the semantics and syntax of the
*expression string* that follows it. A given implementation of TALES can define
any number of expression types, with whatever syntax you like. It also
determines which expression type is indicated by omitting the prefix.

If you do not specify a prefix, Zope assumes that the expression is a *path*
expression.

TALES Expression Types
----------------------

These are the TALES expression types supported by Zope:

path expressions
  locate a value by its path.
exists expressions
  test whether a path is valid.
nocall expressions
   locate an object by its path.
not expressions
   negate an expression
string expressions
  format a string
python expressions
  execute a Python expression

Built-in Names
--------------

These are the names always available to TALES expressions in Zope:

:nothing: special value used by to represent a *non-value* (e.g. void,
		  None, Nil, NULL).
:default: special value used to specify that existing text should not be
		  replaced. See the documentation for individual TAL statements for details on
		  how they interpret *default*.
:options: the *keyword* arguments passed to the template. These are
		  generally available when a template is called from Methods and Scripts,
		  rather than from the web.
:repeat: the repeat variables; see the :ref:`tal:repeat documentation <tal-repeat>`.
:attrs: a dictionary containing the initial values of the attributes of
		the current statement tag.
:CONTEXTS: the list of standard names (this list). This can be used to
		   access a built-in variable that has been hidden by a local or global variable
		   with the same name.
:root: the system's top-most object: the Zope root folder.
:context: the object to which the template is being applied.
:container: The folder in which the template is located.
:template: the template itself.
:request: the publishing request object.
:user: the authenticated user object.
:modules: a collection through which Python modules and packages can be
		  accessed. Only modules which are approved by the Zope security policy can be
		  accessed.

Note the names ``root``, ``context``, ``container``, ``template``, ``request``, ``user``, and
``modules`` are optional names supported by Zope, but are not required by the
TALES standard.

TALES Exists expressions
========================

Syntax
------

Exists expression syntax:

.. code-block:: abnf

  exists_expressions ::= 'exists:' path_expression

Description
-----------

Exists expressions test for the existence of paths. An exists expression
returns true when the path expressions following it expression returns a value.
It is false when the path expression cannot locate an object.

Examples
--------

Testing for the existence of a form variable::

  <p tal:condition="not:exists:request/form/number">
    Please enter a number between 0 and 5
  </p>

Note that in this case you can't use the expression, `not:request/form/number`,
since that expression will be true if the `number` variable exists and is zero.

TALES Nocall expressions
========================

Syntax
------

Nocall expression syntax:

.. code-block:: abnf

  nocall_expression ::= 'nocall:' path_expression

Description
-----------

Nocall expressions avoid rendering the results of a path expression.

An ordinary path expression tries to render the object that it fetches. This
means that if the object is a function, Script, Method, or some other kind of
executable thing, then expression will evaluate to the result of calling the
object. This is usually what you want, but not always. For example, if you want
to put a DTML Document into a variable so that you can refer to its properties,
you can't use a normal path expression because it will render the Document into
a string.

Examples
--------

Using nocall to get the properties of a document::

  <span tal:define="doc nocall:context/aDoc"
        tal:content="string:${doc/getId}: ${doc/title}">
    Id: Title
  </span>

Using nocall expressions on a functions::

  <p tal:define="join nocall:modules/string/join">

This example defines a variable:: `join` which is bound to the `string.join`
function.

TALES Not expressions
=====================

Syntax
------

Not expression syntax:

.. code-block:: abnf

  not_expression ::= 'not:' expression

Description
-----------

Not expression evaluates the expression string (recursively) as a full
expression, and returns the boolean negation of its value. If the expression
supplied does not evaluate to a boolean value, *not* will issue a warning and
*coerce* the expression's value into a boolean type based on the following
rules:

1. the number 0 is *false*

2. positive and negative numbers are *true*

3. an empty string or other sequence is *false*

4. a non-empty string or other sequence is *true*

5. a #. *non-value*#. (e.g. void, None, Nil, NULL, etc) is *false*

6. all other values are implementation-dependent.

If no expression string is supplied, an error should be generated.

Zope considers all objects not specifically listed above as *false* to be
*true*.

Examples
--------

Testing a sequence::

  <p tal:condition="not:context/objectIds">
    There are no contained objects.
  </p>

TALES Path expressions
======================

Syntax
------

Path expression syntax:

.. code-block:: abnf

  PathExpr    ::= Path [ '|' Expression ]
  Path        ::= variable [ '/' PathSegment ]*
  variable    ::= Name
  PathSegment ::= ( '?' variable ) | PathChar+
  PathChar    ::= AlphaNumeric | ' ' | '_' | '-' | '.' | ',' | '~'

Description
-----------

A path expression consists of a *path* optionally followed by a vertical bar
(|) and alternate expression. A path consists of one or more non-empty strings
separated by slashes. The first string must be a variable name (a built-in
variable or a user defined variable), and the remaining strings, the *path
segments*, may contain letters, digits, spaces, and the punctuation characters
underscore, dash, period, comma, and tilde.

A limited amount of indirection is possible by using a variable name prefixed
with `?` as a path segment. The variable must contain a string, which replaces
that segment before the path is traversed.

For example::

  request/cookies/oatmeal
  nothing
  context/some-file 2009_02.html.tar.gz/foo
  root/to/branch | default
  request/name | string:Anonymous Coward
  context/?tname/macros/?mname

When a path expression is evaluated, Zope attempts to traverse the path, from
left to right, until it succeeds or runs out of paths segments. To traverse a
path, it first fetches the object stored in the variable. For each path
segment, it traverses from the current object to the sub-object named by the
path segment. Sub-objects are located according to standard Zope traversal rules
(via getattr, getitem, or traversal hooks).

Once a path has been successfully traversed, the resulting object is the value
of the expression. If it is a callable object, such as a method or template, it
is called.

If a traversal step fails, and no alternate expression has been specified, an
error results. Otherwise, the alternate expression is evaluated.

The alternate expression can be any TALES expression. For example::

  request/name | string:Anonymous Coward

is a valid path expression. This is useful chiefly for providing default
values, such as strings and numbers, which are not expressible as path
expressions. Since the alternate expression can be a path expression, it is
possible to "chain" path expressions, as in::

  first | second | third | nothing

If no path is given the result is *nothing*.

Since every path must start with a variable name, you need a set of starting
variables that you can use to find other objects and values. See the TALES
overview for a list of built-in variables. Variable names are looked up first
in locals, then in globals, then in the built-in list, so the built-in
variables act just like built-ins in Python; They are always available, but
they can be shadowed by a global or local variable declaration. You can always
access the built-in names explicitly by prefixing them with *CONTEXTS*. (e.g.
CONTEXTS/root, CONTEXTS/nothing, etc).

Examples
--------

Inserting a cookie variable or a property::

  <span tal:replace="request/cookies/pref | context/pref">
    preference
  </span>

Inserting the user name::

  <p tal:content="user/getUserName">
    User name
  </p>

TALES Python expressions
========================

Syntax
------

Python expression syntax::

  Any valid Python language expression

Description
-----------

Python expressions evaluate Python code in a security-restricted environment.
Python expressions offer the same facilities as those available in Python-based
Scripts and DTML variable expressions.

Security Restrictions
~~~~~~~~~~~~~~~~~~~~~

Python expressions are subject to the same security restrictions as
Python-based scripts. These restrictions include:


access limits
  Python expressions are subject to Zope permission and role security
  restrictions. In addition, expressions cannot access objects whose names
  begin with underscore.

write limits
  Python expressions cannot change attributes of Zope objects.

Despite these limits malicious Python expressions can cause problems.

Built-in Functions
~~~~~~~~~~~~~~~~~~

Python expressions have the same built-ins as Python-based Scripts with a few
additions.

These standard Python built-ins are available:

- None
- abs
- apply
- callable
- chr
- cmp
- complex
- delattr
- divmod
- filter
- float
- getattr
- hash
- hex
- int
- isinstance
- issubclass
- list
- len
- long
- map
- max
- min
- oct
- ord
- repr
- round
- setattr
- str
- tuple

The ``range`` and ``pow`` functions are available and work the same way they do in
standard Python; however, they are limited to keep them from generating very
large numbers and sequences. This limitation helps to avoid accidental long
execution times.

These functions are available in Python expressions, but not in Python-based
scripts:

path(string)
  Evaluate a TALES path expression.

string(string)
  Evaluate a TALES string expression.

exists(string)
  Evaluates a TALES exists expression.

nocall(string)
  Evaluates a TALES nocall expression.

Python Modules
~~~~~~~~~~~~~~

A number of Python modules are available by default. You can make more modules
available. You can access modules either via path expressions (for example
``modules/string/join``) or in Python with the ``modules`` mapping object (for
example ``modules["string"].join``). Here are the default modules:

string
  The standard `Python string module
  <http://www.python.org/doc/current/lib/module-string.html>`_ Note: most of
  the functions in the module are also available as methods on string objects.

random
  The standard
  `Python random module
  <http://www.python.org/doc/current/lib/module-random.html>`_

math
  The standard `Python math module
  <http://www.python.org/doc/current/lib/module-math.html>`_ .

sequence
  A module with a powerful sorting function. See sequence for more information.

Products.PythonScripts.standard
  Various HTML formatting functions available in DTML. See
  Products.PythonScripts.standard for more information.

ZTUtils
  Batch processing facilities similar to those offered by ``dtml-in``. See
  ZTUtils for more information.

AccessControl
  Security and access checking facilities. See AccessControl for more
  information.

Examples
--------

Using a module usage (pick a random choice from a list)::

  <span tal:replace="python:modules['random'].choice(
                         ['one', 'two', 'three', 'four', 'five'])">
    a random number between one and five
  </span>

String processing (capitalize the user name)::

  <p tal:content="python:user.getUserName().capitalize()">
    User Name
  </p>

Basic math (convert an image size to megabytes)::

  <p tal:content="python:image.getSize() / 1048576.0">
    12.2323
  </p>

String formatting (format a float to two decimal places)::

  <p tal:content="python:'%0.2f' % size">
    13.56
  </p>

TALES String expressions
========================

Syntax
------

String expression syntax:

.. code-block:: abnf

  string_expression ::= ( plain_string | [ varsub ] )*
  varsub            ::= ( '$' Path ) | ( '${' Path '}' )
  plain_string      ::= ( '$$' | non_dollar )*
  non_dollar        ::= any character except '$'

Description
-----------

String expressions interpret the expression string as text. If no expression
string is supplied the resulting string is *empty*. The string can contain
variable substitutions of the form `$name` or `${path}`, where `name` is a
variable name, and `path` is a path expression. The escaped string value of the
path expression is inserted into the string. To prevent a `$` from being
interpreted this way, it must be escaped as `$$`.

Examples
--------

Basic string formatting::

  <span tal:replace="string:$this and $that">
    Spam and Eggs
  </span>

Using paths::

  <p tal:content="string:total: ${request/form/total}">
    total: 12
  </p>

Including a dollar sign::

  <p tal:content="string:cost: $$$cost">
    cost: $42.00
  </p>
