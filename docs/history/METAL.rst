=====================================
 Macro Expansion for TAL  (or METAL)
=====================================

.. toctree::
   :maxdepth: 1

   METALSpecification10
   METALSpecification11

Macros provide a way to define a chunk of presentation in one
template, and share it in others, so that changes to the macro are
immediately reflected in all of the places that share it.

Macros are a special set of TAL statements that are processed
separately from other statements. There are statements to define
macros and macro slots, and to use macros and provide macro arguments.
When a template which uses METAL macros is rendered, it expands them
before processing other TAL statements.

PageTemplates implement METAL, and expand all of the macros used in a
template when generating its source, as well as when rendering it.
This allows macros to be viewed in-place.

See the METAL specifications for details of what METAL provides:

- :doc:`METALSpecification10` is implemented in Zope 2 and Zope 3.0.
- :doc:`METALSpecification11` is implemented in Zope 3.1.

Comments
========

emiliano (Mar 13, 2001 4:27 am; Comment #3)  --
 If the macros are unfolded in full it is essential that it be very
 clear to the user which parts are editable, especially if you're
 editing a macro that includes static parts (non-slots) or that calls
 other macros itself. Assuming that there's some kind of access
 control scheme in place, this can get quite tricky.
