===============================
 TAL Specification Version 1.2
===============================

This specification supercedes :doc:`TALSpecification10`.

The *Template Attribute Language* is an :doc:`../AttributeLanguage`
used to create dynamic templates. It allows elements of a document to
be replaced, repeated, or omitted.

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

See :doc:`EBNF` for rules and terminals. See :doc:`../AttributeLanguage` for a
description of attribute language statements.

The following are the TAL 1.2 statements:

- define
- attributes
- condition
- content
- replace
- repeat
- on-error

Each statement is described below, along with its argument syntax.  Also,
the order of operations is described.

Although :doc:`TAL` does not define the syntax of 'expression'
non-terminals, leaving that up to the implementation, a canonical
expression syntax for use in :doc:`TAL` arguments is described in
:doc:`TALES`.

Expressions used in statements may return values of any type, although
most statements will only accept strings, or will convert values into
a string representation. The expression language must define a value
named *nothing* (see :doc:`TALES`) that is not a string. In
particular, this value is useful for deleting elements or attributes.

Evaluation of an expression may cause the action (the effect of the
statement) to be cancelled. In this case, it is as though the
statement (or part of the statement, in some cases) did not exist.

define
------

Syntax::

       argument       ::= define_scope [';' define_scope]*
       define_scope   ::= (['local'] | 'global') define_var
       define_var     ::= variable_name expression
       variable_name  ::= Name

*Note: If you want to include a semi-colon (;) in an 'expression', it
must be escaped by doubling it (;;).*

You can define two different kinds of TAL variables: local and global.
When you define a local variable in a statement element, you can only
use that variable in that element and the elements it contains. If you
redefine a local variable in a contained element, the new definition
hides the outer element's definition within the inner element. When
you define a global variables, you can use it in any element processed
after the defining element. If you redefine a global variable, you
replace its definition for the rest of the Template.

If the expression associated with a variable evaluates to *nothing*,
then that variable has the value *nothing*, and may be used as such in
further expressions. If the expression cancels the action, then that
variable is not (re)defined. This means that if the variable exists in
an enclosing scope, its value is unchanged, but if it does not, it is
not created. Each variable definition is independent, so variables may
be defined in the same statement in which some variable definitions
are cancelled.

Examples::

          tal:define="mytitle template/title; tlen python:len(mytitle)"
          tal:define="global company_name string:Digital Creations, Inc."

attributes
----------

Syntax::

       argument             ::= attribute_statement [';' attribute_statement]*
       attribute_statement  ::= attribute_name expression
       attribute_name       ::= [namespace ':'] Name
       namespace            ::= Name

*Note: If you want to include a semi-colon (;) in an
'expression', it must be escaped by doubling it (;;).*

If you want to replace the value of an attribute (or create an
attribute) with a dynamic value, you need the 'attributes' statement.
You can qualify an attribute name with a namespace prefix, for example
'html:table', if you are generating an XML document with multiple
namespaces. The value of each expression is converted to a string, if
necessary.

If the expression associated with an attribute assignment evaluates to
*nothing*, then that attribute is deleted from the statement element.
If the expression cancels the action, then that attribute is left
unchanged. Each attribute assignment is independent, so attributes may
be assigned in the same statement in which some attributes are deleted
and others are left alone due to cancellation. Examples::

        <a href="/sample/link.html"
           tal:attributes="href here/sub/absolute_url">
        <textarea rows="80" cols="20"
           tal:attributes="rows request/rows;cols request/cols">

When this statement is used on an element with an active 'replace'
command, the implementation may ignore the 'attributes' statement. If
it does not, the replacement must use the 'structure' type, the
structure returned by the expression must yield at least one element,
and the attributes will be replaced on the first such element only.

When this is used on an element with a 'repeat' statement, the
replacement is made on each repetition of the element, and the
replacement expression is evaluated fresh for each repetition.

condition
---------

Syntax::

        argument ::= expression

To include a particular part of a Template only under certain
conditions, and omit it otherwise, use the 'condition' statement. If
its expression evaluates to a *true* value, then normal processing of
the element continues, otherwise the statement element is immediately
removed from the document. It is up to the interface between TAL and
the expression engine to determine the value of *true* and *false*.
For these purposes, the value *nothing* is false, and cancellation of
the action has the same effect as returning a true value.

Example::

          <p tal:condition="here/copyright"
             tal:content="here/copyright">(c) 2000</p>

replace
-------

Syntax::

        argument ::= (['text'] | 'structure') expression

To replace an element with dynamic content, use the 'replace'
statement. This replaces the statement element with either text or a
structure (unescaped markup). The body of the statement is an
expression with an optional type prefix. The value of the expression
is converted into an escaped string if you prefix the expression with
'text' or omit the prefix, and is inserted unchanged if you prefix it
with 'structure'. Escaping consists of converting "&amp;" to
"&amp;amp;", "&lt;" to "&amp;lt;", and "&gt;" to "&amp;gt;".

If the value is *nothing*, then the element is simply removed. If the
action is cancelled, then the element is left unchanged (see the TALES
*default* value).

*Note: The default replacement behavior is 'text'.*

Examples::

        <span tal:replace="template/title">Title</span>
        <span tal:replace="text template/title">Title</span>
        <span tal:replace="structure table" />
        <span tal:replace="nothing">This element is a comment.</span>

content
-------

Syntax::

        argument ::= (['text'] | 'structure') expression

Rather than replacing an entire element, you can insert text or
structure in place of its children with the 'content' statement. The
statement argument is exactly like that of 'replace', and is
interpreted in the same fashion. If the expression evaluates to
*nothing*, the statement element is left childless. If the action is
cancelled, then the element's contents are unchanged.

*Note: The default replacement behavior is 'text'.*

Example::

          <p tal:content="user/name">Fred Farkas</p>

repeat
------

Syntax::

       argument      ::= variable_name expression
       variable_name ::= Name

When you want to replicate a subtree of your document once for each
item in a sequence, you use 'repeat'. The expression should evaluate
to a sequence. If the sequence is empty, then the statement element is
deleted, otherwise it is repeated for each value in the sequence. If
the action is cancelled, then the element is left unchanged, and no
new variables are defined.

The 'variable_name' is used to define a local variable and a repeat
variable. For each repetition, the local variable is set to the
current sequence element, and the repeat variable is set to an
iteration object. You use iteration objects to access information
about the current repetition (such as the repeat index). (Iteration
objects are more properly the domain of :doc:`TALES`.) The repeat
variable has the same name as the local variable, but is only
accessible through the builtin variable named 'repeat' (see
RepeatVariable). Examples::

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

on-error

      Syntax::

        argument ::= (['text'] | 'structure') expression

You can provide error handling for your document using 'on-error'.
When a TAL statement produces an error, the TAL interpreter searches
for an 'on-error' statement on the same element, then on the enclosing
element, and so forth. The first 'on-error' found is invoked. It is
treated as a 'content' statement.

The simplest sort of 'on-error' statement has a literal error string
or *nothing* for an expression. A more complex handler may call a
script that examines the error and either emits error text or raises
an exception to propagate the error outwards. See
:doc:`RenderErrorHandlingStrategies` for further information. Example::

        <p tal:on-error="string: Error! This paragraph is buggy!">
        My name is <span tal:replace="here/SlimShady" />.<br />
        (My login name is
        <b tal:on-error="string: Username is not defined!"
           tal:content="user">Unknown</b>)
        </p>

In the above example, if 'here/SlimShady' results in an error, the
'on-error' statement catches it and replaces the paragraph with the
string '"Error! This paragraph is buggy!"'. If 'here/SlimShady'
evaluates correctly, but there is an error evaluating 'user', then
'"Username is not defined!"' replaces 'Unknown', but the rest of the
paragraph is processed normally.

Order of Operations
===================

When there is only one TAL statement per element, the order in which
they are executed is simple. Starting with the root element, each
element's statements are executed, then each of its child elements is
visited, in order, to do the same.

Any combination of statements may appear on the same elements, except
that the 'content' and 'replace' statements may not appear together.

When an element has multiple statements, they are executed in this
order:

- 'define'
- 'condition'
- 'repeat'
- 'content' or 'replace'
- 'attributes'

Since the 'on-error' statement is only invoked when an error occurs,
it does not appear in the list.

The reasoning behind this ordering goes like this: You often want to
set up variables for use in other statements, so 'define' comes first.
The very next thing to do is decide whether this element will be
included at all, so 'condition' is next; since the condition may
depend on variables you just set, it comes after 'define'. It is
valuable be able to replace various parts of an element with different
values on each iteration of a repeat, so 'repeat' is next. It makes no
sense to replace attributes and then throw them away, so 'attributes'
is last. The remaining statements clash, because they each replace or
edit the statement element.

If you want to override this ordering, you must do so by enclosing the
element in another element, possibly 'div' or 'span', and placing some
of the statements on this new element. Examples::

          <p tal:define="x /a/long/path/from/the/root"
             tal:condition="x"
             tal:content="x/txt"
             tal:attributes="class x/class">Ex Text</p>


Comments
========


hansa (Jul 10, 2001 11:59 am; Comment #2) *Editor Remark Requested* --
 - my browser is unable to locate the server xml.zope.org

 - typo ... *See AttributeLanguage for a*

 - variable redefinition: case 'global' redefs 'local' only implicit.

 - "When this statement is used on a node with an active 'replace'
   command," perhaps the example should contain a 'replace' (ditto
   'repeat'). X-effects when 'attributes' and 'replace'/'repeat' on
   the same statement element.

 - ..."the implementation may ignore"... hmm. This is a spec, used only for ZPT.
   But even if not, where is it documented for ZPT? or do i have to experiment
   and the language is "what the machine accepts"?

 - ..."interface between TAL and the expression engine"... This interface
   has a)namespaces passed to METAL, b)exceptions returned to TAL, c)agreements
   abt common variable names 'nothing', d) and objects nothing, false etc.
   i would like to have it explicitly described and also, if there is leeway in
   implementing the interface, where. TALES does more than TAL describes and in
   the tutorials plenty more is mentioned.

 - Also, if examples are given, they should be more like before/after TAL

 - change/the element is left/the statement element is left/

Evan --
  The namespace URN "http://xml.zope.org/namespaces/tal" is not a URL, but
  merely a unique identifier.  The "http://" is misleading, and probably
  shouldn't be there.

  I don't see what the typo is, in item 2.  I don't understand item 3.

  This spec is meant to be independent of Zope and ZPT.  Some of the optional
  behavior in the spec isn't properly documented for ZPT, as you noted.

  I'll address the other items as time allows.


hansa (Jul 11, 2001 6:45 am; Comment #3)  --
 - the typo: in my browser the asterisks appear, when by looking at
   the source the intention was italics
 - "When this statement..." appears in section abt 'attributes' right
   after the example and explains cross-effects between 'attributes'
   and 'replace'. It took some time to figure out an example with both
   statements, like::

          <a href="dummy" tal:replace="<a>gaga</a><a>dodo</a>"
             tal:attributes="href here/sub/absolute_url">

   the interpretation of which is implementation dependent and should give
   either::

     <a href="result of here/sub/absolute/url">gaga</a><a>dodo</a>

   or::

     <a>gaga</a><a>dodo</a>.

 - a description of the full ZPT would be very welcome. Thanks for the
   patience anyway
