================
 Page Templates
================

A Page Template is a Zope object intended to hold presentation and
apply it to content. It holds presentation in the form of an XML or
HTML document. The application of this presentation to content is
controlled by attributes in the document called *Template Attributes*.
The possible attributes constitute the *Template Attribute Language*
(:doc:`../tal`). Most of these attributes' values contain expressions referring
to objects or perform computations. In Page Templates, these
expressions are written in the TAL Expression Syntax (:doc:`../tales`).

A separation of user roles is possible because TAL describes dynamic
presentation elements and the inclusion of content, while the
expressions encapsulate the (sometimes) complex logic of addressing
Zope objects and computing values.

Presentation reuse is addressed through :doc:`../metal`, which allow you to
define and use chunks of presentations as *parametric macros*.

A :doc:`PresentationDesigner` uses Page Templates, and may need to know TAL,
but doesn't need to know the expression syntax (TALES). TAL is
designed to be comprehensible to a non-programmer.
