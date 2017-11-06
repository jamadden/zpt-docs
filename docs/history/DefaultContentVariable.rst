.. from
   https://raw.githubusercontent.com/zopefoundation/zpt-docs/master/src/DefaultContentVariable.stx
.. highlight:: html

==========================
 Default Content Variable
==========================

The problem is resolved by the new :doc:`TALSpecification14`

  Forget it...

Problem
=======

When using Page Templates for templating just some inner part of a html
page, you need to replace some elements by their content::

    <html>
      <body>
        The template should render as this text,
        without any tags before and behind.
      </body>
    </html>

This is not possible with the ``default`` variable, because it adapts
to ``replace`` and ``content`` dynamicaly.

Proposal
========

Add a variable ``defaultcontent`` to Tales, that represents the tag's content.

With Tales having a variable like ``defaultcontent``, one could replace::

    <sometag tal:replace="defaultcontent">Original content</sometag>

by simply ::

    Original content

Remark: There is surely a better (shorter) name for such a variable.

Did I miss an easy way to do so with the current language definition?

* Yes, I saw :doc:`../metal`. The proposal is what I expected to exist in the Tales language. I find it more intuitive.
* The second proposal from the :doc:`DisappearingTags` page would resolve my problem too.
