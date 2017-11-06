===================
 Comment Directive
===================

.. highlight:: html

It would be nice, and obviously trivial to provide a do-nothing
comment directive. I could see providing one in both the tal and metal
namespaces::

  <span tal:content="here/splat"
        tal:comment="Jim was here"> ...

Comments
========


peterbe (Jun 12, 2001 6:30 am; Comment #1)  --
 I'm in on that!

 A quick check (did not visit www.w3c.org) proved that Golive have no
 problem with::

  <div tal:comment="

  On many
  Lines
  ">Commented DIV</div>

 It would be be very nice to be able to write comments on many lines.
 Can this be done?

n.larosa (Jun 13, 2001 4:27 am; Comment #2)  --
 Yeah! Go for it! I proposed this on the mailing list a while ago, but
 GvR was not impressed. Let's help him see the light! ;^)

fdrake (Jul 13, 2001 3:06 pm; Comment #3)  --
 Why would this need to be a TAL attribute? What about::

     <div my:comment="
        This is my long comment.
        I could go on for pages and pages...
       ">Commented DIV</div>

 Is it a matter of filtering out the attribute in the rendering? I can
 understand that you might not want the comments public.


mindlace (Jul 18, 2001 2:05 pm; Comment #5)  --
 -1 what's the difference between this and existing comment syntax or
 the ability to just use a bogus tag?


chaironome Tue Aug 31 21:50:00 US/Eastern 2004
  I think this is a fantastic idea. One could use tal:comment="this is
  my comment". It would be the perfect way to explain what you are
  trying to accomplish just by glancing at the page template. Best of
  all, if Zope recognized this tag, the server could assure that these
  comments are always filtered out.
