=================================
 METAL Specification Version 1.0
=================================

The *Macro Expansion Template Attribute Language* is an
:doc:`../AttributeLanguage` for structured macro preprocessing. It can be
used in conjunction with or independently of :doc:`TAL`, :doc:`TALES`
and `ZPT <http://www.zope.org/Wikis/DevSite/Projects/ZPT/FrontPage>`_.

Macros provide a way to define a chunk of presentation in one
template, and share it in others, so that changes to the macro are
immediately reflected in all of the places that share it.
Additionally, macros are always fully expanded, even in a template's
source text, so that the template appears very similar to its final
rendering.

The METAL namespace URI and recommended alias are currently defined as::

    xmlns:metal="http://xml.zope.org/namespaces/metal"

METAL Statements
================

See :doc:`EBNF` for rules and terminals. See :doc:`../AttributeLanguage` for a
description of attribute language statements.

The following are the names of the required TAL 1.0 statements:

- define-macro
- use-macro
- define-slot
- use-slot

Each statement is described below, along with its argument syntax.

Although METAL does not define the syntax of 'expression' non-terminals,
leaving that up to the implementation, a canonical expression syntax for
use in METAL arguments is described in :doc:`TALESSpecification10`.

define-macro
------------

Syntax::

       argument ::= Name

To define a macro, choose a name for the macro and add a
'define-macro' attribute to a document element with the name as the
argument. The element's subtree is the macro body. Example::

        <p metal:define-macro="copyright">
         Copyright 2001, <em>Foobar</em> Inc.
        </p>

use-macro
---------

Syntax::

       argument ::= expression

To use a macro, first choose the document element that you want to
replace. Add a 'use-macro' attribute to it, and set the value of the
attribute to an expression that will return a macro definition. This
will usually be some kind of path or template id and a macro name. It
is important to remember that this expression will be evaluated
separately from any :doc:`TAL` commands in the template, so it cannot
depend on any such commands.

.. note::
   PageTemplates use :doc:`TALES` for the expression, and you can use any
   of the standard context names in path expressions.   Since they expose
   their collection of macro definitions through a 'macros' attribute, you
   can access individual macros easily.*  For example::

       <hr />
        <p metal:use-macro="here/master_page/macros/copyright">
       <hr />

Macro Expansion
~~~~~~~~~~~~~~~

The effect of expanding a macro is to graft a subtree from another
document (or from elsewhere in the current document) in place of the
statement element, replacing the existing subtree. Parts of the
original subtree may remain, grafted onto the new subtree, if the
macro has **slots**. If the macro body uses any macros, they are
expanded first.

When a macro is expanded, its 'define-macro' attribute is replaced
with the 'use-macro' attribute from the statement element. This makes
the root of the expanded macro a valid 'use-macro' statement element.

define-slot and fill-slot
-------------------------

Syntax::

       argument ::= Name

Macros are much more useful if you can override parts of them when you
use them. For example, you might want to reuse a complex table, but
provide different contents for one of the table cells in every place
that you use it. It would be possible to place the contents somewhere
on each Tempate, define a variable for it, and 'insert' that variable
into the cell in the macro body. This, however, would violate the
presentation structure, preventing you from seeing the table with the
cell contents in place.

To make elements of the macro body overridable, add 'define-slot'
attributes with the value set to a slot name. Wherever the macro is
used, choose corresponding sub-elements of the statement element and
add 'fill-slot' attributes with the value set to the slot name. When
the macro is expanded, 'fill-slot' elements will replace the
'define-slot' elements in the macro body that use the same slot name.

Slot names must be unique within a single macro, and within a single
macro use. It is legal, however, to define a slot in a macro and not
fill it. This will simply cause the default contents of the slot
definition to be copied into the expanded macro. If a 'fill-slot'
element names a slot that is not found in the macro body, it causes an
error.

Examples::

       In doc1:
        <table metal:define-macro="sidebar">
          <tr><th>Links</th></tr>
          <tr><td metal:define-slot="links">
            <a href="/">A Link</a>
          </td></tr>
        </table>

       In doc2:
        <table metal:use-macro="here/doc1/macros/sidebar">
          <tr><th>Links</th></tr>
          <tr><td metal:fill-slot="links">
            <a href="http://www.goodplace.com">Good Place</a><br>
            <a href="http://www.badplace.com">Bad Place</a><br>
            <a href="http://www.otherplace.com">Other Place</a>
          </td></tr>
        </table>

Notice that 'doc2', which uses the macro defined in 'doc1', contains
the entire text of the 'sidebar' macro except for the 'links' slot.
This is because the macro is inserted every time the source of 'doc2'
is edited.


Comments
========


gotcha (Mar 16, 2001 5:15 am; Comment #1) *Editor Remark Requested* --
 Two questions:

 I have found no :doc:`UseCases` for METAL so I would like to state one of
 them :

 UseCase

   I create a new template (NT) and I want to reuse some presentation
   code from a previous template (PT) I have written.

   1 I must edit PT and add 'metal:define-macro' attribute in the
   element I want to reuse

   2 I can then add in NT a 'metal:use-macro' attribute to an element
   I want to be replaced by the corresponding excerpt of PT

   3 when i later access NT, NT element with 'metal:use-macro' is
   replaced by the (recursive) expansion of PT element

   4 later, I can change PT 'metal:define-macro' element content

   5 following access to NT reflects changes in PT

 PS please excuse poor english

 Are there any need to keep both this page and METAL page ?
