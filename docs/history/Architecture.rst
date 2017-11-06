==============
 Architecture
==============

.. toctree::
   :maxdepth: 1
   :caption: Contents

   ErrorHandlingStrategies
   EditErrorHandlingStrategies
   RenderErrorHandlingStrategies
   ZopeTemplateLanguageCollaborations
   CompileSourceCollaboration
   RenderTemplateCollaboration
   GetTemplateSourceCollaboration
   ZPTInterfaces

This document (and the documents it links to) *will* describe the
architecture for the Zope PageTemplates. It is a work in progress.

By an architecture, we mean a subset of the design that documents the
design decisions made to address the Major :doc:`UseCases` and
:doc:`RiskFactors`. This will typically include a high-level design
overview. The design will typically be expressed as prose, often
supplemented with UML diagrams.

ZPT consists of 4 major components:

1. The Template Attribute Language (:doc:`../tal`) implementation

2. The Template Attribute Language Expression Syntax (:doc:`../tales`) implementation

3. The Macro Expansion Template Attribute Language (:doc:`../metal`) implementation

4. PageTemplates


Before we can harden the design and implementation of these
components, we need to agree on the ways in which these components
interact. We'll begin by defining relevant :doc:`ZPTInterfaces` that
the various components will need to provide. We'll decide what these
interfaces need to look like through
:doc:`ZopeTemplateLanguageCollaborations`.

ZPT has complex interactions to deal with. The HTML editing tool
(whether it be emacs, vi, GoLive or whatever) will have to communicate
with Zope (usually through WebDAV or FTP) in order to save and load
PageTemplates. Since the templates may have TAL syntax errors in them,
we need to develop :doc:`ErrorHandlingStrategies`.
