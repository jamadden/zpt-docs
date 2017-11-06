===============================
 TAL Specification Version 1.4
===============================

This specification supercedes :doc:`TALSpecification12`.

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

This is not a URL, but merely a unique identifier. Do not expect a
browser to resolve it successfully.

TAL Statements
==============

See :doc:`EBNF` for rules and terminals. See :doc:`../AttributeLanguage` for a
description of attribute language statements.

The following are the TAL 1.4 statements:

- define
- attributes
- condition
- content
- replace
- repeat
- on-error
- omit-tag

Each statement is described below, along with its argument syntax.  Also,
the order of operations< is described.

Although :doc:`TAL` does not define the syntax of 'expression'
non-terminals, leaving that up to the implementation, a canonical
expression syntax for use in :doc:`TAL` arguments is described in :doc:`TALES`.

Expressions used in statements may return values of any type, although
most statements will only accept strings, or will convert values into
a string representation. The expression language must define a value
named *nothing* (see :doc:`TALES`) that is not a string. In particular,
this value is useful for deleting elements or attributes.

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

*Note: If you want to include a semi-colon (;) in an
'expression', it must be escaped by doubling it (;;).*

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

*Note: If you want to include a semi-colon (;) in an 'expression', it
must be escaped by doubling it (;;).*

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
For example, for the first line below, either of the two outcomes that
follow it is acceptable::

      <span tal:replace="structure an_image" tal:attributes="border string:1">
      <img src="foo.png">
      <img src="foo.png" border="1">

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

If the value is *nothing*, then the element is simply
removed.  If the action is cancelled, then the element is left
unchanged (see the TALES *default* value).

**Note:** The default replacement behavior is 'text'.

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

**Note:** The default replacement behavior is 'text'.

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
:doc:`RepeatVariable`). Examples::

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
--------

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

omit-tag
--------

Syntax::

      argument ::= ![expression]

To leave the contents of a tag in place while omitting the surrounding
start and end tag, use the 'omit-tag' statement. If its expression
evaluates to a *false* value, then normal processing of the element
continues. If the expression evaluates to a *true* value, or there is
no expression, the statement tag is replaced with its contents. It is
up to the interface between TAL and the expression engine to determine
the value of *true* and *false*. For these purposes, the value
*nothing* is false, and cancellation of the action has the same effect
as returning a false value.

Examples::

        <div tal:omit-tag="" comment="This tag will be removed">
        <i>...but this text will remain.</i>
        </div>

        <b tal:omit-tag="not:bold">I may not be bold.</b>

Order of Operations
===================

When there is only one TAL statement per element, the order in which
they are executed is simple. Starting with the root element, each
element's statements are executed, then each of its child elements is
visited, in order, to do the same.

Any combination of statements may appear on the same elements, except
that the 'content' and 'replace' statements may not appear together.

When an element has multiple statements, they are executed
in this order:

- 'define'
- 'condition'
- 'repeat'
- 'content' or 'replace'
- 'attributes'
- 'omit-tag'

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

"the implementation may ignore." hmm. This is a spec, used only for
ZPT. But even if not, where is it documented for ZPT? or do i have to
experiment and the language is "what the machine accepts"?

..."interface between TAL and the expression engine"... This interface
has a)namespaces passed to METAL, b)exceptions returned to TAL,
c)agreements abt common variable names 'nothing', d) and objects
nothing, false etc. i would like to have it explicitly described and
also, if there is leeway in implementing the interface, where. TALES
does more than TAL describes and in the tutorials plenty more is
mentioned.

change/the element is left/the statement element is left/

MHudson (Feb 17, 2002 12:21 pm; Comment #1) *Editor Remark Requested* --
 TAL 1.4 includes several references to:
 If the expression _cancels_ the action...
 _cancellation_ of the action has the same effect as...

 The only reference I can find to the mechanism for cancellation is
 from TALES 1.2 where the path modifier (if) could _cancel_ an action.
 Since TALES 1.2 has been superceded by TALES 1.4 are there other
 mechanisms for cancellation or are these clauses now superceded?

MHudson (Feb 17, 2002 12:23 pm; Comment #2)  --
 TALES 1.4 mentioned above should be TALES 1.3 which removed the path
 modifers.

gotcha (Mar 22, 2002 11:10 am; Comment #3) *Editor Remark Requested* --
 It says:
        If you want to override this ordering, you must do so by enclosing
        the element in another element, possibly 'div' or 'span', and placing
        some of the statements on this new element.


 This sentence should be removed and replaced by something like:

 'tal:' prefixed elements are allowed and interpreted. Further, any 'tal:' prefixed element has a default 'tal:omit-tag' like behaviour.

 If you want to override this ordering, you can use 'tal:' prefixed
 elements.

 Example::

   <tal:formfields repeat="field python:form.get_fields()">
   <tal:fieldid define="fieldId python:field.getId()">
     <tal:m_formaterrorbyfield
         metal:use-macro="here/PT_macros/macros/formatErrorByField"
         condition="python:errorMap.has_key(fieldId)">
         <p class="errorMessage"
            tal:content="python:errorMap[fieldId].error_text"></p>
     </tal:m_formaterrorbyfield>
   </tal:fieldid>
   </tal:formfields>


Tobias Sun Feb 17 15:57:03 +0000 2008

  The text says, "It makes no sense to replace attributes and then
  throw them away, so attributes is last"; but in the list, "omit-tag"
  appears after "attributes". Wouldn't it make sense to note
  "omit-tag" before "attributes" (after define and repeat, possibly
  conflicting with replace)? If the omit condition is true, there
  wouldn't be any tag which could bear the attributes, right? (it
  might perfectly make sense to have attributes when the tag is *not*
  omitted).


FredDrake Fri May 7 08:59:51 -0400 2010

  This is a bug in the specification above (it's inconsistent):

  https://bugs.launchpad.net/zope.tal/+bug/430662

  A new version of the spec needs to be created and the
  implementations can be adjusted to suite should they determine that
  the new spec is acceptable. Some implementations may already support
  the recommended change.
