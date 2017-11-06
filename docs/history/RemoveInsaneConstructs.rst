
.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/RemoveInsaneConstructs.stx

==========================
 Remove Insane Constructs
==========================

Proposal
========

The TAL 1.4 specification provides a number of loopholes for
confusion. These appear to be issues where specific combinations of
TAL statements weren't considered when the specification was written.
As TAL has been more broadly used, and especially as it's seen
adoption in Zope 3, some less carefully
crafted corners of the specification have been identified.

This proposal suggests some specific improvements that should be made
to the specification, which can be folded in to create a new revision.
It is expected that at least the Zope 3 TAL interpreter can be
improved as a result.

Specific Changes
----------------

- 'tal:replace' and 'tal:content' can insert new markup structures
  into the output using the 'structure' keyword. The TAL 1.4
  specification does not take a stance on whether this structure
  should be parsed and interpreted in any way; implementations are
  *allowed* to ignore embedded TAL constructs, but are permitted to
  interpret them.

  In the case when interpretation is permitted, attribute replacements
  specified using 'tal:attributes' are made to the first element found
  in the replacement. It is not clear that this behavior is useful,
  but it is very fragile.

  The Zope 3 implementation attempts to support the insertion of
  attributes, and does so by honoring all TAL within the replacement,
  but in the absence of 'tal:attributes' does not respect TAL embedded
  in the replacement structure.

  `PETAL <http://search.cpan.org/author/JHIVER/>`_ does not support
  interpretation of TAL in the replacement structure.

  The specification should be modified to disallow the interpretation of replacement structure.

- If TAL in a replacement structure can never be interpreted, there is
  no value to ever combine 'tal:replace' and 'tal:attributes'. This
  should be explicitly identified as an error in the template.
  'tal:content' and 'tal:attributes' still makes sense.

References
==========

`structure and TAL interpretation
<http://mail.zope.org/pipermail/zpt/2004-July/thread.html#5441>`_ was
a thread on the ZPT mailing list where it was generally agreed that
parsing a replacement structure was undesirable.

Comments
========

tseaver Thu Oct 21 15:04:00 US/Eastern 2004
  +1 on disallowing 'tal:replace' with 'tal:attributes' as a programming error.

lalo Thu Oct 21 16:16:00 US/Eastern 2004
  +1

  IIRC OpenTAL ignores all other directives when a ``replace`` is
  executed, and doesn't parse the generated structure either (which
  would be a rather tough proposition given OpenTAL's model).

slinkp Thu Oct 21 18:21:00 US/Eastern 2004
  In however many years I've been reading and writing zpt, it has
  never even occurred to me to want to do that. It could even be a
  security issue to interpret the structure. Maybe you are inserting
  some HTML written by some content jockey who is not allowed to write
  code. Let's get rid of this.

efge Mon Oct 25 11:01:00 US/Eastern 2004
  +1 on both
