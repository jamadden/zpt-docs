===============================
 Page Template Implementations
===============================

This document will mention some implementations of page templates.

* `zope.pagetemplate <https://zopepagetemplate.readthedocs.io>`_ is
  the reference implementation for page templates in Python. It
  implements TAL and METAL using the packages `zope.tal
  <https:://zopetal.readthedocs.io>`_ and TALES using `zope.tales
  <https://zopetales.readthedocs.io>`_.
* `Chameleon <https://chameleon.readthedocs.io>`_ is a Python implementation
  with a focus on speed (it compiles templates into Python bytecode). It
  differs from the reference and specifications in a few ways, most
  notably by using Python expressions as the default and not providing
  an implementation of path expressions. If you add the `z3c.pt
  <https://z3cpt.readthedocs.io/>`_ package, you can combine path
  expressions and Chameleon templates.
* `SimpleTAL <http://owlfish.com/software/simpleTAL/>`_ is an independent
  Python implementation.
* `PHPTAL <https://phptal.org>`_ is an independent implementation in PHP 5.
* `ZPT-JS <https://www.npmjs.com/package/zpt>`_ is an independent
  implementation in Javascript.
