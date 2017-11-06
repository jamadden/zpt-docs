====================
 Expression Aliases
====================

Problem
=======

You can access other expression types from Python expressions by using the
type name as a function, for example::

      tal:replace="python:path(string('here/$x'))"

but 'string' conflicts with the string module, which would normally allow
you to write expressions such as::

      tal:replace="python:string.split(x, ':')[0]"

Proposal
========

Place the TALES expression functions into a builtin 'tales' namespace.
This is more verbose, but more explicit::

      tal:replace="python:tales.path(tales.string('here/$x'))"

Old Proposal
============

.. note:: Reaction to this was divided. The new proposal is more
          Pythonic.

Make capitalized versions ('Path', 'Nocall', 'String') of these
functions, don't replace 'string', and promote the capitalized
version in docs. Then, you could use both, as in::

      tal:replace="string.split(Path(String('here/$x')), ':')[0]"


Comments
========


pje (Aug 19, 2001 8:12 am; Comment #8)  --
 >     but 'string' conflicts with the string module, which would normally allow
 >     you to write expressions such as::
 >
 >       tal:replace="python:string.split(x, ':')[0]"


 Um, now that Zope is on Python 2.1, can't you do the above with::

  tal:replace="python:x.split(':')[0]"

 I know that's just one example, but AFAICT the string module is all
 but deprecated in 2.1. The "tales.foo" approach seems awfully verbose
 to me, if string is the only conflict being worried about here. If a
 prefix is preferable to capitalization, how about "as"? e.g.
 "python:asPath(asString('here/$x'))", or
 "python:as_path(as_string('here/$x'))". Just a thought.

tone (Aug 20, 2001 12:21 am; Comment #9)  --
 I agree with pje. If ZPT is going to be core in 2.5, it's important
 to keep things 'clean' and not too verbose. +1 for using 2.1
 string-type methods.
