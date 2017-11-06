=====================
 Attribute Languages
=====================

.. from https://github.com/zopefoundation/zpt-docs/blob/master/src/AttributeLanguage.stx

An attribute language is a programming language designed to mesh in a
particular way with documents written using tag-structured markup, such
as HTML and XML.  The statements of the language are document tags with
special attributes, and look like this::

   <p ns:command="argument">Some Text</p>

The attribute ``ns:command="argument"`` is the *statement attribute*,
and the entire paragraph tag is the *statement element*, the
portion of the document on which this statement operates.  Each statement
attribute has three parts: the namespace prefix, the name, and the
argument.  The prefix identifies the language, and must be introduced
by an XML namespace declaration in XML and XHTML documents, like
this::

   xmlns:ns="http://language.creator.com/namespaces/MyAttributeLanguage"

HTML should include these declarations, but an implementation may allow
you to omit them.  The statement name determines which language statement
to apply to the statement element, and the argument determines how it is
applied.  The name is always a simple identifier, but the argument can
contain just about anything, depending on the statement.

How to Specify an Attribute Language
====================================

If you create an attribute language, you need to define the following:

* The URI of the XML namespace, and (optionally) a recommended alias.
* The names, argument syntax, and meaning of the possible statements.
* The order in which statements on the same element are processed, and
    restrictions on which statements can share the same element.

Why use Attribute Languages
===========================

A central concept of Page Template design is the proper embedding
of programming code into HTML and XML.  This is not a completely
new idea; template systems such as ASP, PHP, and DTML have defined various
new tags to hold code.  Tools must be modified to work properly with the
resulting mixed documents, since they are often invalid.

Presentation Templates take advantage of XML namespaces and the tendency
of HTML tools to preserve and ignore attributes that they don't recognize.
They do this by using attribute languages.   Attribute language statements
conform to the XML 1.0 syntax and may appear in XML, XHTML or HTML
documents.

.. important:: Statements in HTML documents must be within well formed
			   elements (even if the rest of the document is not well formed).

Drawbacks
=========

There are several restrictions imposed on attribute languages by the
use of attributes as statements.  The most obvious is that statements
are tied to tags.  This makes it slightly awkward to operate on other
attributes or on the contents of elements. A tag cannot contain more
than one attribute with the same name, the value must be quoted, and
the arrangement of attributes on a tag is not dependable.  Also, parsing
or writing a DTD for attribute languages is hard if the name of any
statement attribute contains "data".  Examples::

  Bad: <div ns:set="foo=1" ns:set="bar=2">
  Bad: <div ns:set-foo="1" ns:set-bar="2">
  Good: <div ns:set="foo=1, bar=2">


The attribute languages :doc:`tal` and :doc:`metal` used in
Page Templates both take these restrictions into account. They
do not depend on the ordering of attributes within a tag, and some
commands allow arguments with multiple parts.
