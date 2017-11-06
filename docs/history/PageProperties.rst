=================
 Page Properties
=================

Problem
=======

Some of the data associated with a page, such as its Title, should be available
for inspection by other objects.

Proposal
========

Give Page Templates a property sheet just like Folders and DTML Documents.

Comments
========


Lynn Walton (Jun 12, 2001 11:14 pm; Comment #1)  --
 I definitely think properties for PageTemplates would be an
 incredibly useful feature. And also as part of it, an idea which Evan
 suggested in response to a mailing list post I made, having a global
 variable page too, where global variables would automatically be
 included in the page as though a <div tal:define="...."></div> was
 done.

 Here is a quote from Evan's post that gives the idea more: (Hope Evan doesn't mind me quoting this here.)

 ::

   --------- QUOTE -----------------------
   It does make me think, though, that rather than the
   standard property sheet such as Folders and Documents have (or in
   addition to such as sheet), it might be valuable to have a global
   variable definition sheet, which would take a form like this (please
   excuse ASCII art):

   Variable    Expression
   ______    _____________
   |x_____|  |here/foo_______|
   ______    _____________
   |name__|  |string:Fred_____|
   ______    _____________
   |four___|  |python:2+2____|

   ...and implicitly execute it at the start of page rendering, as though
   the first line of the template were:

   <div tal:define="global x here/foo;global name string:Fred;global four
   python:2+2"></div>
   -------------- END QUOTE ---------------------------------

 So, I hope this idea get's implemented too!

peterbe (Jul 16, 2001 4:04 am; Comment #2)  --
 +1

 Wohoo! I've wanted this. Since ZPT has the template namespace we can
 use ZPT as both a DTML Method and DTML Document at the same time.

jmr (Jul 19, 2001 5:25 pm; Comment #3)  --
 +1

 ZPT Properties would be great! (I didn't see the original on the
 "global properties" mail, and can't understand what is meant because
 the wiki has mangled the ascii art... so no comment on that
 comment.:)
