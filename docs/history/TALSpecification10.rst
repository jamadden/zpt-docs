===============================
 TAL Specification Version 1.0
===============================

The *Template Attribute Language* is an :doc:`../AttributeLanguage` used to
create dynamic templates. It allows elements of a document to be
replaced, repeated, or omitted.

The statements of TAL are XML attributes from the TAL namespace. These
attributes can be applied to an XML or HTML document in order to make
it act as a template.

A **TAL statement** has a name (the attribute name) and a body (the
attribute value). For example, an 'content' statement might look like
'tal:content="string:Hello"'. The element on which a statement is
defined is its **statement element**. Most TAL statements require
expressions, but the syntax and semantics of these expressions are not
part of TAL. :doc:`TALES` is recommended for this purpose.

The TAL namespace URI and recommended alias are currently defined as::

  xmlns:tal="http://xml.zope.org/namespaces/tal"

TAL Statements
==============

*See :doc:`EBNF` for rules and terminals.* *See :doc:`../AttributeLanguage` for a
description of attribute language statements.*

The following are the TAL 1.0 statements::

   define, attributes, condition, content, replace, repeat

Each statement is described below, along with its argument syntax.
Although :doc:`TAL` does not define the syntax of 'expression'
non-terminals, leaving that up to the implementation, a canonical
expression syntax for use in :doc:`TAL` arguments is described in [TALES
Specification 1.0].

Define
------

Syntax::

     argument       ::= define_scope [';' define_scope]*
     define_scope   ::= (['local'] | 'global') define_var
     define_var     ::= variable_name expression
     variable_name  ::= Name

*Note: If you want to include a semi-colon (;) in an
'expression', it must be escaped by doubling it (;;).*

You can define two different kinds of TAL variables: local and global.
When you define a local variable in a statement element, you can only
use that variable in that element and the elements it contains.
If you redefine a local variable in a contained element, the new
definition hides the outer element's definition within the inner element.
When you define a global variables, you can use it in any element
processed after the defining element.  If you redefine a global variable,
you replace its definition for the rest of the Template.

Examples::

    tal:define="mytitle template/title; tlen python:len(mytitle)"
    tal:define="global company_name string:Digital Creations, Inc."

Attributes
----------

Syntax::

 argument             ::= attribute_statement [';' attribute_statement]*
 attribute_statement  ::= attribute_name expression
 attribute_name       ::= [namespace ':'] Name
 namespace            ::= Name

*Note: If you want to include a semi-colon (;) in an 'expression', it
must be escaped by doubling it (;;).*

If you want to replace the value of an attribute (or create an
attribute) with a dynamic value, you need the 'attributes' command.
You can qualify an attribute name with a namespace prefix, for example
'html:table'. The value of each expression is converted to a string,
if necessary. If the value is *nothing* (see [TALES Specification
1.0]), the attribute is deleted. Examples::

  <a href="/sample/link.html"
     tal:attributes="href here/sub/absolute_url">
  <textarea rows="80" cols="20"
     tal:attributes="rows request/rows;cols request/cols">

When this is used on a node with a 'replace' command, the replacement
must use the 'structure' type, the structure returned by the
expression must yield at least one element node, and the attributes
are replaced on the first such element node only.

When this is used on a node with a 'repeat' command, the replacement
is made on each repetition of the element, and the replacement
expression is evaluated fresh for each repetition.

Condition
---------

Syntax::

  argument ::= expression

To include a particular part of a Template only under certain
conditions, and omit it otherwise, use the 'condition' command. Its
body is an expression. If the expression evaluates to a *true* value,
then normal processing of the element continues, otherwise the command
element is immediately removed from the document. It is up to the
interface between TAL and the expression engine to determine the value
of *true* and *false*.

Example::

    <p tal:condition="here/copyright"
       tal:content="here/copyright">(c) 2000</p>

Replace
-------

Syntax::

  argument ::= (['text'] | 'structure') expression

To replace an element with dynamic content, use the 'replace'
statement. This replaces the current element with either text or a
structure (unescaped *ML). The body of the statement is an expression
with an optional type prefix. The value of the expression is converted
into an escaped string if you prefix the expression with 'text' or
omit the prefix, and is inserted raw if you prefix it with
'structure'. If the value is *nothing* (see :doc:`TALES`), then the
element is simply removed.

*Note: The default replacement behavior is 'text'.*

Examples::

  <span tal:replace="template/title">Title</span>
  <span tal:replace="text template/title">Title</span>
  <span tal:replace="structure table" />

Content
-------

Syntax::

  argument ::= (['text'] | 'structure') expression

Rather than replacing an entire element, you can insert text or
structure in place of its children with the 'content' statement. The
statement argument is exactly like that of 'replace', and is
interpreted in the same fashion. If the expression evaluates to
*nothing*, the statement element is left childless.

*Note: The default replacement behavior is 'text'.*

Example::

    <p tal:content="user/name">Fred Farkas</p>

Repeat
------

Syntax::

 argument      ::= variable_name expression
 variable_name ::= Name

When you want to replicate a subtree of your document once for each
item in a sequence, you use 'repeat'. The expression should evaluate
to a sequence. The 'variable_name' is used to define a local variable
and a repeat variable. For each iteration, the local variable is set
to the current sequence element, and the repeat variable is set to an
iteration object. You use iteration objects to access information
about the current repeat iteration (such as the repeat index).
(Iteration objects are more properly the domain of :doc:`TALES`.) The
repeat variable has the same name as the local variable, but is only
accessible through the 'repeat' variable. Examples::

  <p tal:repeat="txt python:'one', 'two', 'three'">
     <span tal:replace="txt" />
  </p>
  <table>
    <tr tal:repeat="item here/cart">
        <td tal:content="repeat/item/index">1</td>
        <td tal:content="item/description">Widget</td>
        <td tal:content="item/price">$1.50</td>
    </tr>
  </table>

Order of Operations
===================

When there is only one TAL statement per element, the order in which
they are executed is simple. Starting with the root node, each node
executes its command, then tells each of its child elements, in order,
to do the same.

Only one command out of 'content', 'replace', and 'repeat' is allowed
on a single element. Any one of these may appear on the same element
as a 'define', 'attributes', and 'condition' command.

When an element has multiple commands, they are executed in this
order:

- 'define'
- 'condition'
- 'content', 'replace', or 'repeat'
- 'attributes'

The reasoning behind this ordering goes like this: You often want to
set up variables for use in other commands, so 'define' comes first.
The very next thing to do is decide whether this node will be included
at all, so 'condition' is next; since the condition may depend on
variables you just set, it comes after 'define'. Setting attributes
doesn't make sense if the next step throws the entire node away, and
we will probably want to be able to fill in different attribute values
on each iteration of a repeat, so 'attributes' is last. All of the
remaining commands clash, because they each replace or edit the
command element.

If you want to override this ordering, you must do so by enclosing the
element in another element, possibly 'div' or 'span', and placing some
of the commands on this new element. Examples::

    <p tal:define="x /a/long/path/from/the/root"
       tal:condition="x"
       tal:content="x/txt"
       tal:attributes="class x/class">Ex Text</p>
