.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/HTMLTALOptionsAllow.stx

=========================
 Empty Attribute Options
=========================

If we had attributes (e.g. tal:dummy) whos values must always be
empty, it would be nice, in HTML mode, to allow simple option syntax::

  <tr tal:dummy> ... </tr>

Comments
========


peterbe (Jul 16, 2001 3:55 am; Comment #1) *Editor Remark Requested* --
 Please elaborate more here.
 The word "dummy" annoys me. It feel unserious and a bit too funky.

n.larosa (Jul 30, 2001 9:20 am; Comment #2)  --
 Well, if you do not like "dummy", think "comment" or something else,
 that's not the point here.

 My vote is ``0`` on this, saving three chars does not seem like much.
 On the other hand, it would not validate as XHTML 1.0.
