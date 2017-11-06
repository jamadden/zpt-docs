============================================================
 Historical Change Log for TAL, Page Templates, and ZTUtils
============================================================

.. From https://github.com/zopefoundation/zpt-docs/blob/master/src/ChangeLog.stx

2001-08-13: PageTemplates and TAL 1.4.0, ZTUtils 1.1.0
======================================================

- ZPTs are now cache-enabled

- Added property sheet to ZPT

- Added TAL statement: ``omit_tag="[<boolean expr>]"`` replaces the
  statement tag with its contents if the boolean expression is
  true or omitted.

- The TAL and METAL namespaces can be applied to tag names, tags in
  these namespaces are removed from rendered output (leaving the
  contents in place, as with omit_tag) whenever attributes in these
  namespaces would be, and tag attributes without explicit namespaces
  default to the tag's namespace (per XML spec).

- TreeMakers have a setChildAccess() method that you can use to
  control tree construction. Child nodes can be accessed through
  either an attribute name or callback function. Children fetched by
  attribute name can be filtered through a callback function.

- A new LazyFilter class allows you to filter a sequence using Zope
  security and an optional filter callback function. The security and
  filter tests are lazy, meaning they are performed as late as
  possible.

  The optional 'skip' argument determines the reaction when access to
  a sequence element is refused by the Zope security policy. The
  default (None) is to raise the 'Unauthorized' exception. If a string
  is passed, such elements are skipped. If the string is non-empty, it
  is treated as a permission name, and the element is skipped if the
  user doesn't have that permission on the element.

- The Zope versions of TreeMaker, SimpleTreeMaker, and Batch now use
  LazyFilter. The TreeMakers have a setSkip() method that can be used
  to set the 'skip' value. Batch has an optional 'skip_unauthorized'
  argument that is passed to LazyFilter as 'skip'.

- Utility functions ``make_query()``, ``url_query()``, and
  ``make_hidden_input()`` have been added.

- FIXED: Expressions with embedded newlines were broken

- FIXED: History comparison tried to expand macros

- FIXED: Iterator exceptions weren't converted

- FIXED: 'Unauthorized' exception couldn't be handled by on-error


2001-07-18: PageTemplates and TAL 1.3.3
=======================================

* Allow any false value in tal:repeat to act as an empty sequence.

* manage_addPageTemplate accepts optional title and text
  arguments, and returns the new object if REQUEST is None.

* FIXED: The various Python modules failed to import CompilerError.

* FIXED: Security machinery changes in Zope 2.4 broke ZRPythonExpr

* FIXED: ``tal:atributes`` was creating stray attributes in METAL
  expansion, and there was no unit test for this behavior.

* FIXED: ``tal:attributes`` parsing was not catching badly malformed
  values, and used "print" instead of raising exceptions.

2001-05-25: PageTemplates 1.3.1
===============================

* Added error logging to PageTemplateFiles.

* Refactored PythonExpr, and added support for Zope 2.4

2001-05-21: TAL and PageTemplates 1.3.0
=======================================

* Guido fixed use of nested macros.

* New builtin variables ``default``, ``user``, and ``attrs``.

* Removed path modifiers.

* Added ``|`` operator for paths.

* Tweaked parameters passed when calling DTML.

* Expression types now have corresponding builtin functions in
  Python expressions.

2001-05-07: TAL and PageTemplates 1.2.1
=======================================

* Bugfixes for 'repeat' variable access

2001-04-27: TAL and PageTemplates 1.2.0, ZTUtils 1.0.0
======================================================

* Depends on the new ``ZTUtils`` package, which adds batching and
  tree widget capabilities.

* Path expressions now have optional path modifiers.  These
  appear in parenthesis before the path, and include ``if``,
  ``exists``, and ``nocall``.

* Changed ``nocall:`` and ``exists:`` expressions types into path modifiers.

* ``tal:attributes`` no longer inserts empty attributes into
  source.

* The ``if`` path modifier can cancel any TAL action.

2001-04-10: TAL and PageTemplates 1.1.0
=======================================

* Bug fixes

* Tell TAL not to parse replacement structural text

* Change tests to match TAL's omitted attributes

2001-03-30: TAL and PageTemplates 1.0.0
=======================================

* Minor bugs fixed

2001-03-27: TAL and PageTemplates 1.0.0b1
=========================================

* All functionality described in the Project Wiki is implemented
