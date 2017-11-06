=========================
 Editing Expanded Macros
=========================

I've been playing with macros a bit, and it occurs to me that while it
is a great safety feature to ignore edits to expanded macros in the
page templates that use them, this really ought to be a switchable
behaviour.

In other words, I'd like to be able to add a
``metal:edit-expanded="allow"`` statement to the macro definition in order
to facilitate rapid development (edits to the expanded macro are
applied to the macro definition), and then remove the statement when I
want to lock the macro down.

Alternatively, this could be a checkbox in the ZMI, but I think I like
the attribute better.

When I'm designing an overall page layout, I find that I need to
evaluate it in different contexts and tweak it in different ways
depending on the specifics of the page. All page layouts end up being
compromises between the needs of the different contexts in which the
layout is used. Developing consistent navigation systems for sites
seems to be the hardest balancing act.

It's annoying to have to constantly switch back and forth between the
context that I am currently evaluating and the 'master' macro template
in order to edit it. There is little productivity improvement over
using DTML in this case.

Being able to edit the expanded macro and have those changes applied
to the macro definition would save a *lot* of time and annoyance, and
make it easier and quicker to find the balance between the different
contexts' needs.

I want to emphasize again that I would *not* advocate that this
statement be left on the macro beyond the prototyping stage, or that
it be used in situations where one person is doing the design, and
another does the programming.

However, Zope is used by many people who are a 'one-man-show' so to
speak, and making their life easier only makes sense, especially as
this will not compromise any of ZPT's goals for other audiences.
