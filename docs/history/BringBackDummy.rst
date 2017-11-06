==================
 Bring Back Dummy
==================
.. highlight:: html

I think that::

  <tr tal:dummy="">.....</tr>

is more obvious than::

  <tr tal:replace="nothing">.....</tr>

That is, I think a dedicated tag would make dummy or example (I'd be
happy with tal:example.) elements easier to spot.


Comments
========


peterbe (Jun 12, 2001 6:24 am; Comment #1)  --
 I'm just a user, but my instinctive feel is that
 'dummy' or 'example' should not be implemented::

    <tr tal:replace="nothing">.....</tr>

  *does* feel very natural.

n.larosa (Jun 13, 2001 4:29 am; Comment #2)  --
 Well, this overlaps with the :doc:`CommentDirective` proposal. Is
 tal:comment more pleasant than tal:dummy ? I hope so.

peterbe (Jul 16, 2001 3:53 am; Comment #3)  --
 -1

 go tal:comment !

mindlace (Jul 18, 2001 2:04 pm; Comment #4)  --
 -1 but how about tal:replace="structure nothing"?

n.larosa (Jul 30, 2001 8:43 am; Comment #6)  --
 I guess I now understand the difference between the :doc:`CommentDirective`
 and :doc:`BringBackDummy`.

 A ``tal:comment`` attribute would be erased by itself, leaving
 the tag in place, while a ``tal:dummy`` attribute would make the
 whole tag disappear.

 Is this correct? If so, I see the need for something dummy-like.
 ``tal:replace="nothing"`` is too verbose, and does not clearly
 express the fact that this is a comment. I would use the tal:comment
 in place of the ``tal:dummy`` one, though.

 On the other hand, the standard HTML comment syntax leaks to the
 generated HTML pages, something to be avoided. And there's no such
 option in XML, generally speaking.

 A ``<span tal:comment="SQL data here">`` tag would not impact the
 designer's layout, while ``<`span tal:comment="">Jim, please
 no italics. Thanks.</span>`` would be visible.
