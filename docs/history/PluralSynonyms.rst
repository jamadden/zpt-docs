=================
 Plural Synonyms
=================

Problem
=======

It's easy to forget whether the correct spelling of some of the TAL
attributes is plural or not. In particular, a prominent member of the
ZPT community used 'tal:contents' instead of 'tal:content'.

Proposal
========

Make synonyms for all of the TAL statements for which it is reasonable to
have both the singular and plural forms.  In particular:

* content / contents
* attribute / attributes
* define / defines
* on-error / on-errors

We would never have given any of these new names a *different* meaning
for fear of confusing users, so it seems reasonable to me to give them
the same meaning. I omitted 'conditions', since that implies (to me)
that it allows multiple conditions. I don't have a strong reason for
leaving out 'replaces' and 'repeats', just a vague feeling that
they're unlikely to suffer from the same confusion, since they are
verbs and don't accept multiple parts.

Comments
========


peterbe (Jul 16, 2001 4:01 am; Comment #1)  --
 -1 NO!

 I :doc:`agree with myself <OptionalInAs>` that this will only cause
 confusion. The Python "only one" is better. It isn't that difficult
 to remember these things.

mindlace (Jul 18, 2001 2:10 pm; Comment #2)  --
 -1 TOOWTDI

poster (Jul 31, 2001 11:31 am; Comment #3)  --
 -1
