.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/ReplaceInnerText.stx

====================
 Replace Inner Text
====================

.. highlight:: html

Problem
=======

When you use 'tal:content' or 'tal:replace' on a tag, then change the
style of tag's content with an editor, it is easy to accidentally
insert markup tags into the content. This markup will then be lost
when the page is rendered. For example::

     <i><span tal:replace="x"><b><font size="+1">Text</font></b></span></i>

The '<i>' tag is correctly placed, but the others will be lost.

Proposal
========

Add a TAL statement that replaces the content of its innermost simply
nested tag. Starting with TAL statement tag, if the current element
has only one child element (not counting whitespace), examine that
element. When you reach an element with zero or more than one child,
replace the content of that element.

In each of the following examples, '<b>' is the innermost simply
nested tag::

     <b>Text</b>
     <b>Text and <i>Markup</i></b>
     <i><b>Text</b></i>
     <span> <i> <b></b> </i> </span>

These are just examples of what it means for a tag to be "innermost
simply nested". I haven't got a proposed spelling for the new
statement, except that the value would presumably work the same as
those of 'tal:replace' and 'tal:content'.

Comments
========

peterbe (Jul 16, 2001 3:32 am; Comment #1) *Editor Remark Requested* --
 +1
 But I don't get it!
 With the propopsal, what would happen to this::

  <i>&lt;span tal:replace="x"><b><font size="+1">Text</font></b></span></i>

 This feature proposal is very important for the GoLive/Dreamweaver argument part of ZPT.

mindlace (Jul 18, 2001 1:47 pm; Comment #2)  --
 -1 DWIMpy! It's my understanding that at least goliath can be set to
 work in "css mode" where it doesn't do things like this to achieve
 presentation effects.

magnus (Jul 31, 2001 1:41 am; Comment #3)  --
 -1 If <i> <b> etc are the only reason, use CSS!

interra (Jul 31, 2001 3:06 am; Comment #4)  --
 -1 This kind of behavior is confusing. What if I want to mark all
  fields to be replaced it template with Bold Italic Yellow text? I
  want them to be stripped out of result!

poster (Jul 31, 2001 11:43 am; Comment #5)  --
 </i></b>+1 (as a new separate attribute only): CSS is the right thing
 to do but production-line assembly with many designers I've worked
 with is under very little programmatic control, and CSS is something
 that neither the designers nor the marketers who foot their bills
 care much about. Having "replace inner text" as an optional tag
 (*not* changing tal:replace current functionality) will make
 programmers in these kind of designer-driven shops more sane, I
 think. Those of you not in this kind of designer-driven shop (be
 thankful) can still use tal:replace and justifiably insist on CSS.
