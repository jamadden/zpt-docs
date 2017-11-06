======
 EBNF
======
Here is the initial cut at an EBNF syntax (work still in progress)!

EBNF Syntax::

  [ ... ] apply zero or one times;
  [ ... ]* apply zero or more times;
  [ ... ]+ apply one or more times;
  ... | ... choose one of the alternatives;
  '...' use the literal characters enclosed;
  ( ... ) used for grouping.


EBNF Terminals::

  String      ::=
  Name        ::=
  URL_Segment ::=

  Letter      ::=
  Number     ::=
  NonAlphaId ::=



Comments
========

hansa (Jul 6, 2001 12:15 pm; Comment #1) *Editor Remark Requested* --
 it would be nice, to have EBNF for String..NonAlphaId specified and
 please consistent w a)the specs b) the tutorial
