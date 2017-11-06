================
 Project Vision
================

.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/VisionStatement.stx

.. toctree::
   :maxdepth: 1

   UseCases
   RiskFactors

Problem
=======

Presentation Designers like to work with sophisticated *visually
oriented* web page tools where they can sculpt presentation in the
context of the prototypical content. DTML uses its own non-HTML markup
which is counter to the way most web page editors work. Through the
extensive use of DTML, content is dynamically generated and
prototypical proxies (for said content) is not available to the
designer. In addtion, content, presentation and logic are all blended
into one place and each respective party is expected to keep their
hands off of the other's domain.

Proposed Solution
=================

Adopt a consistent, XML compliant, markup language as a target, embed
all logic in namespaced attributes (where they are ignored by the web
page tools) and provide a means to supply prototypical content to give
the presentation designer a sense of *context*.

The solution shall have the following characteristics:

- Everything is explicit. There are no implicit contexts or magic side
  effects.
- Separates Presentation from Content and Business Logic.
- Work with *plain old* HTML as well as XML.
- XML compliant.
- Allows *example* content in PageTemplates to allow designers to
  see/edit their work in context (idea courtesy of HiperDOM).
- Macro based template facility. You can create paramaterized chunks of
  Presentation that can be applied as macros in pages.
- Many of these ideas are universal. Things that are not Zope specific
  shouldn't be made Zope specific.

Risk Factors
============

See :doc:`RiskFactors`

Scope
=====

This markup language will replace DTML for most general functionality
and will be accompanied by tools that will help the Presentation
Designer's tools import and export pages to and from Zope.

This markup language will not be Zope specific in design, but may be so in
implementation.

See the :doc:`FAQ` for additional information.

Deliverables
============

1. The Template Attribute Language (:doc:`../tal`)

2. The Template Attribute Language Expression Syntax (:doc:`../tales`)

3. The Template Attribute Language Macros (:doc:`../metal`)

4. PageTemplates


Comments
========


jeske (Apr 3, 2002 6:05 pm; Comment #2) --
 I recommend you take a look
 at `Clearsilver.net <http://clearsilver.net>`_. Clearsilver is an
 extremely fast template system which separates presentation from
 application logic. It is based on the template system we used
 internally for eGroups.com and which is still used for Yahoo Groups.
