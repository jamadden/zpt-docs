=================
 METAL Reference
=================

.. highlight:: html

The *Macro Expansion Template Attribute Language* (METAL) standard is a
facility for HTML/XML macro preprocessing. It can be used in conjunction with
or independently of TAL and TALES.

Introduction
============

Macros provide a way to define a chunk of presentation in one template, and
share it in others, so that changes to the macro are immediately reflected in
all of the places that share it. Additionally, macros are always fully
expanded, even in a template's source text, so that the template appears very
similar to its final rendering

METAL Namespace
---------------

The METAL namespace URI and recommended alias are currently defined as::

  xmlns:metal="http://xml.zope.org/namespaces/metal"

Just like the TAL namespace URI, this URI is not attached to a web page; it's
just a unique identifier.

Zope does not require an XML namespace declaration when creating templates with
a content-type of ``text/html``. However, it does require an XML namespace
declaration for all other content-types.

METAL Statements
----------------

METAL defines a number of statements:

metal:define-macro
  Define a macro.
metal:use-macro
  Use a macro.
metal:define-slot
  Define a macro customization point.
metal:fill-slot
  Customize a macro.

Although METAL does not define the syntax of expression non-terminals, leaving
that up to the implementation, a canonical expression syntax for use in METAL
arguments is described in TALES Specification.

define-macro: Define a macro
============================

Syntax
------

metal:define-macro syntax:

.. code-block:: abnf

  argument ::= Name

Description
-----------

The ``metal:define-macro`` statement defines a macro. The macro is named by the
statement expression, and is defined as the element and its sub-tree.

In Zope, a macro definition is available as a sub-object of a template's
``macros`` object. For example, to access a macro named ``header`` in a template
named ``master.html``, you could use the path expression::

  master.html/macros/header

Examples
--------

Simple macro definition::

  <p metal:define-macro="copyright">
    Copyright 2009, <em>Foobar</em> Inc.
  </p>



define-slot: Define a macro customization point
===============================================

Syntax
------

metal:define-slot syntax:

.. code-block:: abnf

  argument ::= Name

Description
-----------

The ``metal:define-slot`` statement defines a macro customization point or
*slot*. When a macro is used, its slots can be replaced, in order to customize
the macro. Slot definitions provide default content for the slot. You will get
the default slot contents if you decide not to customize the macro when using
it.

The ``metal:define-slot`` statement must be used inside a ``metal:define-macro``
statement.

Slot names must be unique within a macro.

Examples
--------

Simple macro with slot::

  <p metal:define-macro="hello">
    Hello <b metal:define-slot="name">World</b>
  </p>

This example defines a macro with one slot named ``name``. When you use this
macro you can customize the ``b`` element by filling the ``name`` slot.


fill-slot: Customize a macro
============================

Syntax
------

metal:fill-slot syntax:

.. code-block:: abnf

  argument ::= Name

Description
-----------

The ``metal:fill-slot`` statement customizes a macro by replacing a *slot* in the
macro with the statement element (and its content).

The ``metal:fill-slot`` statement must be used inside a ``metal:use-macro``
statement. Slot names must be unique within a macro.

If the named slot does not exist within the macro, the slot contents will be
silently dropped.

Examples
--------

Given this macro::

  <p metal:define-macro="hello">
    Hello <b metal:define-slot="name">World</b>
  </p>

You can fill the ``name`` slot like so::

  <p metal:use-macro="container/master.html/macros/hello">
    Hello <b metal:fill-slot="name">Kevin Bacon</b>
  </p>


use-macro: Use a macro
======================

Syntax
------

metal:use-macro syntax:

.. code-block:: abnf

  argument ::= expression

Description
-----------

The ``metal:use-macro`` statement replaces the statement element with a macro.
The statement expression describes a macro definition.

In Zope the expression will generally be a path expression referring to a macro
defined in another template. See "metal:define-macro" for more information.

The effect of expanding a macro is to graft a subtree from another document (or
from elsewhere in the current document) in place of the statement element,
replacing the existing sub-tree. Parts of the original subtree may remain,
grafted onto the new subtree, if the macro has *slots*. See metal:define-slot
for more information. If the macro body uses any macros, they are expanded
first.

When a macro is expanded, its ``metal:define-macro`` attribute is replaced with
the ``metal:use-macro`` attribute from the statement element. This makes the root
of the expanded macro a valid ``use-macro`` statement element.

Examples
--------

Basic macro usage::

  <p metal:use-macro="container/other.html/macros/header">
    header macro from defined in other.html template
  </p>

This example refers to the ``header`` macro defined in the ``other.html`` template
which is in the same folder as the current template. When the macro is
expanded, the ``p`` element and its contents will be replaced by the macro. Note:
there will still be a ``metal:use-macro`` attribute on the replacement element.
