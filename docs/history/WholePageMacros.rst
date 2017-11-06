.. from https://github.com/zopefoundation/zpt-docs/blob/master/src/WholePageMacros.stx

===================
 Whole Page Macros
===================

Problem
=======

If you want to re-use an entire page as a macro, you can't if the page
has elements outside of the outermost tag. In particular, '<!DOCTYPE>'
must precede the main tag and '<?xml?>' must precede everything else
if they are used.

It is trivial to expose the entire text of a Page Templates as a macro
through a 'macro' attribute. Unfortunately, there is no place to put a
'metal:use-macro' statement that uses a full-page macro. If you wrote
'<html metal:use-macro="here/other.zpt/macro">', for instance, the
'<html>' tag would be replaced by the contents of 'other.zpt'. The
resulting macro expansion would not have 'metal:use-macro' in it, so
further changes would break the connection.

Proposals
=========

* Add a 'master_macro' property to Page Templates. Since it would not
  be part of the text of the page, it would not be broken by the macro
  expansion process. It wouldn't be visible in (or editable through)
  the source, though, which could be a problem.

* Add a processing instruction '<?metal use-macro=""?>' that would act
  like the 'master_macro' property, except that it would be
  re-inserted into the expanded macro at the top, or just below the
  '<?xml?>', if there is one. The line below might expand into the
  text that follows it::

     <?metal use-macro="here/other.zpt/macro"?>

     <?xml version="1.0"?>
     <?metal use-macro="here/other.zpt/macro"?>
     <records tal:repeat="record options/records">
       <record tal:content="structure record"></record>
     </records>

Comments
========

mindlace (Jul 18, 2001 2:12 pm; Comment #1)  --
 +1 for the second option.

ElviX (Aug 6, 2001 5:17 am; Comment #2)  --
 +1 for both

 Never use wysiwyg editors anyway, so the first one would never pose a
 problem..

dracvl (Oct 25, 2001 4:25 am; Comment #3)  --
 I think both would be useful, the master macro to quickly apply the
 same doctype to the whole site, and the other one to have selected
 parts of the site have a specific doctype. Number two should then be
 able to override the master macro? (I'm tired and don't think
 clearly, just think that a rule is needed if we want to keep both)
