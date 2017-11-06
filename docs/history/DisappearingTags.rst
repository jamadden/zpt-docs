===================
 Disappearing Tags
===================

.. highlight:: html

Problem
=======

Several people don't like the fact that their pages end up littered
with ``<span>`` tags that they had to add in order to hold
``tal:repeat``. Not all (not many?) XML schemas provide a "neutral" tag
like ``<span>`` and ``<div>``, so it might be impossible to cleanly
generate documents in those schemas.

Proposal
========

These proposals aren't mutually exclusive, and #2 will not solve the
problem for XML, so both may be implemented.

1. Define a ``<tal:block>`` tag that is always replaced by
   its contents.

   Also, since the default namespace of its attributes
   is ``tal:``, we can omit it from the attributes.  We would
   get ``1 2 3`` from::

     <tal:block repeat="digit python:1,2,3">
       <tal:block replace="digit">1</tal:block>
     </tal:block>

   Testing has shown that Dreamweaver and GoLive, at least, have no problem
   with ``tal:block`` tags.

2. Add a statement called ``tal:remove`` or ``tal:no_tag`` that replaces the
   statement's tag with its children, after other TAL processing is done.
   The attribute value could be a boolean expression that controls whether
   the tag is removed, or omitted for the default behaviour.  For example::

     <span tal:repeat="digit python:1,2,3"
           tal:remove="">
           <span tal:replace="digit">1</span>
     </span>

     <span tal:repeat="digit python:1,2,3"
           tal:no_tag="options/no_tags">
		   <span tal:replace="digit">1</span>
     </span>

Comments
========

(Earlier comments consolidated)

mindlace (Jun 17, 2001 4:02 am; Comment #1)  --
 Note that the block syntax would also allow me to use ZPT for things like
 CSS.  I think the "big problem" with your own tags in DTML was things like
 if else /if  , where the "middle" tag was a singleton, and no editor would
 be able to know if it should keep else.

 From a pedantic perspective, having a block tag also lets TAL be a real schema
 as opposed to this floating attribute namespace.

byron (Jun 18, 2001 6:00 pm; Comment #9)  --
 Is there a missing double quote after *tal:replace="digit* in the examples, or is that the syntax?

 *Evan - whoops, no, it's a typo.  Fixed now*

richard (Jul 14, 2001 10:39 pm; Comment #10)  --
 +1

peterbe (Jul 16, 2001 3:28 am; Comment #11) *Editor Remark Requested* --
 +1
 This is cool and necessary for survival for those XML Schemas you're talking about.
 Please explain/discuss the possible values of the tal:remove attribute a bit more.

mindlace (Jul 18, 2001 1:40 pm; Comment #12)  --
 +6 :)

mindlace (Jul 18, 2001 1:42 pm; Comment #13)  --
 er... that goes for proposal 1. I'm indifferent about #2.

n.larosa (Jul 30, 2001 9:05 am; Comment #14)  --
 +1 for proposal #2. I would avoid the introduction of new tags, everything in attributes is cleaner.

 This would be clean up the generated pages a lot. Also, often there
 are <div></div> empty tags that cannot be avoided, so I
 would like to see three values for the tal:remove attribute:
 yes, no and empty. The empty
 value would instruct the parser to only erase the tag if it is empty.

poster (Jul 30, 2001 10:41 pm; Comment #15)  --
 Proposal 1: **+1**
   this seems necessary for a robust templating system (XML, as noted
   above), and acknowledges that it is structural logic and not a
   display tag; I would suggest this to be the last-resort method,
   preferring my alternate proposal below.

 Proposal 2: **-1**
   Pure structural logic masquerading as actual document markup is not
   only redundant but potentially confusing, and somewhat against the
   "What you see is very similar to what you get" ZPT principle. Of
   course, this argument could also be made against tal:replace as an
   attribute and not a tag... In any case, tal:remove would also
   significantly overlap tal:replace in functionality.

 Alternate proposal to proposal 2: tal:repeat_internal
   The situation in which the noted problem arises is in repeats. If a
   tag attribute is available that repeats only its internal contents,
   I believe many situations could be resolved more gracefully. In an
   example modified from one in Evan's "Getting Started" tutorial, the
   tbody tag would not be endlessly and pointlessly repeated in the
   produced code, for instance::

     <table><tr><th>Titles</th></tr>
       <tbody tal:repeat_internal="item container/objectValues">
       <tr bgcolor="#EEEEEE" tal:condition="repeat/item/even">
         <td tal:content="item/title">a title</td></tr>
         <tr tal:condition="repeat/item/odd">
         <td tal:content="item/title">a title</td></tr>
       </tbody>
     </table>

bernt (Aug 12, 2001 9:13 am; Comment #17)  --
 See :doc:`DefaultContentVariable` Problem and Proposal for a related problem and an alternate solution.
