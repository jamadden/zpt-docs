============
 Extensions
============

ZPT-specific Behaviors
======================

The behavior of Zope Page Templates is almost completely described by the TAL,
TALES, and METAL specifications. ZPTs do, however, have a few additional
features that are not described in the standards.


HTML Support Features
---------------------

.. highlight:: html

When the content-type of a Page Template is set to ``text/html``, Zope processes
the template somewhat differently than with any other content-type. As
mentioned under TAL Namespace, HTML documents are not required to declare
namespaces, and are provided with ``tal`` and ``metal`` namespaces by default.

HTML documents are parsed using a non-XML parser that is somewhat more
forgiving of malformed markup. In particular, elements that are often written
without closing tags, such as paragraphs and list items, are not treated as
errors when written that way, unless they are statement elements. This laxity
can cause a confusing error in at least one case; a ``<div>`` element is
block-level, and therefore technically not allowed to be nested in a ``<p>``
element, so it will cause the paragraph to be implicitly closed. The closing
``</p>`` tag will then cause a NestingError, since it is not matched up with the
opening tag. The solution is to use ``<span>`` instead.

Unclosed statement elements are always treated as errors, so as not to cause
subtle errors by trying to infer where the element ends. Elements which
normally do not have closing tags in HTML, such as image and input elements,
are not required to have a closing tag, or to use the XHTML ``<tag />`` form.

Certain boolean attributes, such as ``checked`` and ``selected``, are treated
differently by ``tal:attributes``. The value is treated as true or false (as
defined by ``tal:condition``). The attribute is set to ``attr="attr"`` in the true
case and omitted otherwise. If the value is ``default``, then it is treated as
true if the attribute already exists, and false if it does not. For example,
each of the following lines::

  <input type="checkbox" checked tal:attributes="checked default">
  <input type="checkbox" tal:attributes="checked string:yes">
  <input type="checkbox" tal:attributes="checked python:42">

will render as::

  <input type="checkbox" checked="checked">

while each of these::

  <input type="checkbox" tal:attributes="checked default">
  <input type="checkbox" tal:attributes="checked string:">
  <input type="checkbox" tal:attributes="checked nothing">

will render as::

  <input type="checkbox">

This works correctly in all browsers in which it has been tested.
