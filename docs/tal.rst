===============
 TAL Reference
===============

.. highlight:: html

The *Template Attribute Language* (TAL) standard is an attribute language used
to create dynamic templates. It allows elements of a document to be replaced,
repeated, or omitted.

.. include:: impl-note.rst

Introduction
============

The statements of TAL are XML attributes from the TAL namespace. These
attributes can be applied to an XML or HTML document in order to make it act as
a template.

A **TAL statement** has a name (the attribute name) and a body (the attribute
value). For example, an ``content`` statement might look like::

  tal:content="string:Hello"

The element on which a statement is defined is its **statement element**. Most
TAL statements require expressions, but the syntax and semantics of these
expressions are not part of TAL. TALES is recommended for this purpose.

TAL Namespace
-------------

The TAL namespace URI and recommended alias are currently defined
as::

  xmlns:tal="http://xml.zope.org/namespaces/tal"

This is not a URL, but merely a unique identifier. Do not expect a browser to
resolve it successfully.

Implementations should not require an XML namespace declaration when
creating templates with a content-type of ``text/html``. However, they
should require an XML namespace declaration for all other content-types.

TAL Statements
--------------

These are the tal statements:

tal:attributes
  dynamically change element attributes.
tal:define
   define variables.
tal:condition
   test conditions.
tal:content
   replace the content of an element.
tal:omit-tag
   remove an element, leaving the content of the element.
tal:on-error
   handle errors.
tal:repeat
   repeat an element.
tal:replace
   replace the content of an element and remove the element
   leaving the content.

Expressions used in statements may return values of any type, although most
statements will only accept strings, or will convert values into a string
representation. The expression language must define a value named *nothing*
that is not a string. In particular, this value is useful for deleting elements
or attributes.

Order of Operations
-------------------

When there is only one TAL statement per element, the order in which they are
executed is simple. Starting with the root element, each element's statements
are executed, then each of its child elements is visited, in order, to do the
same.

Any combination of statements may appear on the same elements, except that the
``content`` and ``replace`` statements may not appear together.

Due to the fact that TAL sees statements as XML attributes, even in HTML
documents, it cannot use the order in which statements are written in the tag
to determine the order in which they are executed. TAL must also forbid
multiples of the same kind of statement on a single element, so it is
sufficient to arrange the kinds of statement in a precedence list.

When an element has multiple statements, they are executed in this order:

1. define

2. condition

3. repeat

4. content or replace

5. attributes

6. omit-tag

Since the ``on-error`` statement is only invoked when an error occurs, it does
not appear in the list.

It may not be apparent that there needs to be an ordering. The reason that
there must be one is that TAL is XML based. The XML specification specifically
states that XML processors are free to rewrite the terms. In particular, you
cannot assume that attributes of an XML statement will be processed in the
order written, particularly if there is another preprocessor involved. To avoid
needless proliferation of tags, and still permit unambiguous execution of
complex TAL, a precedence order was chosen according to the following
rationale.

The reasoning behind this ordering goes like this: You often want to set up
variables for use in other statements, so ``define`` comes first. The very next
thing to do is decide whether this element will be included at all, so
``condition`` is next; since the condition may depend on variables you just set,
it comes after ``define``. It is valuable be able to replace various parts of an
element with different values on each iteration of a repeat, so ``repeat`` is
next. It makes no sense to replace attributes and then throw them away, so
``attributes`` is last. The remaining statements clash, because they each replace
or edit the statement element.

attributes: Replace element attributes
======================================

Syntax
------

tal:attributes syntax:

.. code-block:: abnf

  argument             ::= attribute_statement [';' attribute_statement]*
  attribute_statement  ::= attribute_name expression
  attribute_name       ::= [namespace-prefix ':'] Name
  namespace-prefix     ::= Name

*Note: If you want to include a semi-colon (;) in an ``expression``, it must be
escaped by doubling it (;;).*

Description
-----------

The ``tal:attributes`` statement replaces the value of an attribute (or creates
an attribute) with a dynamic value. You can qualify an attribute name with a
namespace prefix, for example::

  html:table

if you are generating an XML document with multiple namespaces. The value of
each expression is converted to a string, if necessary.

If the expression associated with an attribute assignment evaluates to
*nothing*, then that attribute is deleted from the statement element. If the
expression evaluates to *default*, then that attribute is left unchanged. Each
attribute assignment is independent, so attributes may be assigned in the same
statement in which some attributes are deleted and others are left alone.

If you use ``tal:attributes`` on an element with an active ``tal:replace`` command,
the ``tal:attributes`` statement is ignored.


If you use ``tal:attributes`` on an element with a ``tal:repeat`` statement, the
replacement is made on each repetition of the element, and the replacement
expression is evaluated fresh for each repetition.

Examples
--------

Replacing a link::

  <a href="/sample/link.html"
     tal:attributes="href context/sub/absolute_url">

Replacing two attributes::

  <textarea
    rows="80" cols="20"
    tal:attributes="rows request/rows;cols request/cols">

condition: Conditionally insert or remove an element
====================================================

Syntax
------

tal:condition syntax:

.. code-block:: abnf

  argument ::= expression

Description
-----------

The ``tal:condition`` statement includes the statement element in the template
only if the condition is met, and omits it otherwise. If its expression
evaluates to a *true* value, then normal processing of the element continues,
otherwise the statement element is immediately removed from the template. For
these purposes, the value *nothing* is false, and *default* has the same effect
as returning a true value.

See :ref:`tales-not-expression` for information on what the default
expression language considers to be *true* or *false*.

Examples
--------

Test a variable before inserting it (the first example tests for existence and
truth, while the second only tests for existence)::

  <p tal:condition="request/message | nothing"
     tal:content="request/message">message goes here</p>

  <p tal:condition="exists:request/message"
     tal:content="request/message">message goes here</p>

Test for alternate conditions::

  <div tal:repeat="item python:range(10)">
    <p tal:condition="repeat/item/even">Even</p>
    <p tal:condition="repeat/item/odd">Odd</p>
  </div>


content: Replace the content of an element
==========================================

Syntax
------

tal:content syntax:

.. code-block:: abnf

  argument ::= (['text'] | 'structure') expression

Description
-----------

Rather than replacing an entire element, you can insert text or structure in
place of its children with the ``tal:content`` statement. The statement argument
is exactly like that of ``tal:replace``, and is interpreted in the same fashion.
If the expression evaluates to *nothing*, the statement element is left
childless. If the expression evaluates to *default*, then the element's
contents are unchanged.

The default replacement behavior is ``text``, which replaces angle-brackets and
ampersands with their HTML entity equivalents. The ``structure`` keyword passes
the replacement text through unchanged, allowing HTML/XML markup to be
inserted. This can break your page if the text contains unanticipated markup
(e.g.. text submitted via a web form), which is the reason that it is not the
default.

Examples
--------

Inserting the user name::

  <p tal:content="user/getUserName">Fred Farkas</p>

Inserting HTML/XML::

  <p tal:content="structure context/getStory">
    marked <b>up</b> content goes here.
  </p>


define: Define variables
========================

Syntax
------

tal:define syntax::

  argument       ::= define_scope [';' define_scope]*
  define_scope   ::= (['local'] | 'global') define_var
  define_var     ::= variable_name expression
  variable_name  ::= Name

*Note: If you want to include a semi-colon (;) in an ``expression``, it must be
escaped by doubling it (;;).*

Description
-----------

The ``tal:define`` statement defines variables. You can define two different
kinds of TAL variables: local and global. When you define a local variable in a
statement element, you can only use that variable in that element and the
elements it contains. If you redefine a local variable in a contained element,
the new definition hides the outer element's definition within the inner
element. When you define a global variables, you can use it in any element
processed after the defining element. If you redefine a global variable, you
replace its definition for the rest of the template.

*Note: local variables are the default*

If the expression associated with a variable evaluates to *nothing*, then that
variable has the value *nothing*, and may be used as such in further
expressions. Likewise, if the expression evaluates to *default*, then the
variable has the value *default*, and may be used as such in further
expressions.

Examples
--------

Defining a global variable::

  tal:define="global company_name string:Zope Corp, Inc."

Defining two variables, where the second depends on the first::

  tal:define="mytitle template/title; tlen python:len(mytitle)"



omit-tag: Remove an element, leaving its contents
=================================================

Syntax
------

tal:omit-tag syntax:

.. code-block:: abnf

  argument ::= [ expression ]

Description
-----------

The ``tal:omit-tag`` statement leaves the contents of an element in place while
omitting the surrounding start and end tags.

If the expression evaluates to a *false* value, then normal processing of the
element continues and the tags are not omitted. If the expression evaluates to
a *true* value, or no expression is provided, the statement element is replaced
with its contents. The *default* value is considered to be true.

See :ref:`tales-not-expression` for information on what the default
expression language considers to be *true* or *false*.

Examples
--------

Unconditionally omitting a tag::

  <div tal:omit-tag="" comment="This tag will be removed">
    <i>...but this text will remain.</i>
  </div>

Conditionally omitting a tag::

  <b tal:omit-tag="not:bold">
    I may be bold.
  </b>

The above example will omit the ``b`` tag if the variable ``bold`` is false.

Creating ten paragraph tags, with no enclosing tag::

  <span tal:repeat="n python:range(10)"
        tal:omit-tag="">
    <p tal:content="n">1</p>
  </span>


on-error: Handle errors
=======================

Syntax
------

tal:on-error syntax:

.. code-block:: abnf

  argument ::= (['text'] | 'structure') expression

Description
-----------

The ``tal:on-error`` statement provides error handling for your template. When a
TAL statement produces an error, the TAL interpreter searches for a
``tal:on-error`` statement on the same element, then on the enclosing element,
and so forth. The first ``tal:on-error`` found is invoked. It is treated as a
``tal:content`` statement.

A local variable ``error`` is set. This variable has these attributes:

:type: the exception type
:value: the exception instance
:traceback: the traceback object

The simplest sort of ``tal:on-error`` statement has a literal error string or
*nothing* for an expression. A more complex handler may call a script that
examines the error and either emits error text or raises an exception to
propagate the error outwards.

Examples
--------

Simple error message::

  <b tal:on-error="string: Username is not defined!"
     tal:content="context/getUsername">Ishmael</b>

Removing elements with errors::

  <b tal:on-error="nothing"
     tal:content="context/getUsername">Ishmael</b>

Calling an error-handling script::

  <div tal:on-error="structure context/errorScript">
  ...
  </div>

Here's what the error-handling script might look like::

  ## Script (Python) "errHandler"
  ##bind namespace=_
  ##
  error=_['error']
  if error.type==ZeroDivisionError:
      return "<p>Can't divide by zero.</p>"
  else
      return """<p>An error ocurred.</p>
      <p>Error type: %s</p>
      <p>Error value: %s</p>""" % (error.type, error.value)

.. _tal-repeat:

repeat: Repeat an element
=========================

Syntax
------

tal:repeat syntax:

.. code-block:: abnf

  argument      ::= variable_name expression
  variable_name ::= Name

Description
-----------

The ``tal:repeat`` statement replicates a sub-tree of your document once for each
item in a sequence. The expression should evaluate to a sequence. If the
sequence is empty, then the statement element is deleted, otherwise it is
repeated for each value in the sequence. If the expression is *default*, then
the element is left unchanged, and no new variables are defined.

The ``variable_name`` is used to define a local variable and a repeat variable.
For each repetition, the local variable is set to the current sequence element,
and the repeat variable is set to an iteration object.

Repeat Variables
----------------

You use repeat variables to access information about the current repetition
(such as the repeat index). The repeat variable has the same name as the local
variable, but is only accessible through the built-in variable named ``repeat``.


Repeat Variable Attributes
~~~~~~~~~~~~~~~~~~~~~~~~~~

:index:  repetition number, starting from zero.
:number: repetition number, starting from one.
:even: true for even-indexed repetitions (0, 2, 4, ...).
:odd: true for odd-indexed repetitions (1, 3, 5, ...).
:start: true for the starting repetition (index 0).
:end: true for the ending, or final, repetition.
:first: true for the first item in a group - see note below
:last: true for the last item in a group - see note below
:length: length of the sequence, which will be the total number of
         repetitions.
:letter: repetition number as a lower-case letter: "a" - "z", "aa" - "az",
         "ba" - "bz", ..., "za" - "zz", "aaa" - "aaz", and so forth.
:Letter: upper-case version of *letter*.
:roman: repetition number as a lower-case roman numeral: "i", "ii", "iii",
        "iv", "v", etc.
:Roman: upper-case version of *roman*.

You can access the contents of the repeat variable using path expressions or
Python expressions. In path expressions, you write a three-part path consisting
of the name ``repeat``, the statement variable's name, and the name of the
information you want, for example, ``repeat/item/start``. In Python expressions,
you use normal dictionary notation to get the repeat variable, then attribute
access to get the information, for example, "python:repeat['item'].start".

With the exception of ``start``, ``end``, and ``index``, all of the attributes of a
repeat variable are methods. Thus, when you use a Python expression to access
them, you must call them, as in "python:repeat['item'].length()".

.. note:: ``first`` and ``last`` are intended for use with sorted
   sequences. They try to divide the sequence into group of items with
   the same value. If you provide a path, then the value obtained by
   following that path from a sequence item is used for grouping,
   otherwise the value of the item is used. You can provide the path
   by passing it as a parameter, as in::

      python:repeat['item'].first(color)

   or by appending it to the path from the repeat variable, as in
   "repeat/item/first/color".

.. admonition:: Zope Implementation Note

   ``first`` and ``last`` are Zope 2 extensions and are not
   available in other implementations.

Examples
--------

Iterating over a sequence of strings::

  <p tal:repeat="txt python: ('one', 'two', 'three')">
    <span tal:replace="txt" />
  </p>

Inserting a sequence of table rows, and using the repeat variable to number the
rows::

  <table>
    <tr tal:repeat="item context/cart">
      <td tal:content="repeat/item/number">1</td>
      <td tal:content="item/description">Widget</td>
      <td tal:content="item/price">$1.50</td>
    </tr>
  </table>

Nested repeats::

  <table border="1">
    <tr tal:repeat="row python:range(10)">
      <td tal:repeat="column python:range(10)">
        <span tal:define="x repeat/row/number;
                          y repeat/column/number;
                          z python:x*y"
              tal:replace="string:$x * $y = $z">
            1 * 1 = 1
        </span>
      </td>
    </tr>
  </table>


Insert objects. Separate groups of objects by meta-type by drawing a rule
between them::

  <div tal:repeat="object objects">
    <h2 tal:condition="repeat/object/first/meta_type"
        tal:content="object/meta_type">Meta Type</h2>
    <p tal:content="object/getId">Object ID</p>
    <hr tal:condition="repeat/object/last/meta_type" />
  </div>

Note, the objects in the above example should already be sorted by meta-type.


replace: Replace an element
===========================

Syntax
------

tal:replace syntax:

.. code-block:: abnf

  argument ::= (['text'] | 'structure') expression

Description
-----------

The ``tal:replace`` statement replaces an element with dynamic content. It
replaces the statement element with either text or a structure (unescaped
markup). The body of the statement is an expression with an optional type
prefix. The value of the expression is converted into an escaped string if you
prefix the expression with ``text`` or omit the prefix, and is inserted unchanged
if you prefix it with ``structure``. Escaping consists of converting "&amp;" to
"&amp;amp;", "&lt;" to "&amp;lt;", and "&gt;" to "&amp;gt;".

If the value is *nothing*, then the element is simply removed. If the value is
*default*, then the element is left unchanged.

Examples
--------

The two ways to insert the title of a template::

  <span tal:replace="template/title">Title</span>
  <span tal:replace="text template/title">Title</span>

Inserting HTML/XML::

  <div tal:replace="structure table" />

Inserting nothing::

  <div tal:replace="nothing">
    This element is a comment.
  </div>
