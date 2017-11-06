==========================
 Edit-time Error Handling
==========================

Here is a proposal for dealing with TAL errors in saved templates.

Context
=======

A :doc:`PresentationDesigner` is creating or editing a template, not through
the Zope Management Inferface, but through client software like GoLive
or Dreamweaver.

Problem
=======

The HTML file saved to Zope may have METAL/TAL syntax errors. Such a
file is no longer a valid TAL template. However, the file is most
likely okay HTML (as verified by the client software). What do we do
when the saved file is broken?

Forces
======

- Refusing to save the file (WebDAV or FTP PUT error) is very
  misleading. There is no consistent way to send back the intent of
  the rejection, so the user thinks that Zope is broken.

- Silently ignoring the error means bad pages. Bad pages mean bad
  sites. Bad sites mean bad press.

- The :doc:`PresentationDesigner` has a workflow. TAL is already
  disruptive enough to the workflow. In addition, using ZPT is also a
  change in the way things are done. The potential for more errors
  will cause the designer to question how useful doing templates and
  Zope is to begin with.

- The ZPT management interface can do fancy logging and presentation
  of the TAL syntax errors but a :doc:`PresentationDesigner` may not want to
  leave his/her neat little GoLive world to wrangle with HTML forms
  (and the synchronization problem that editing in two places can
  introduce).

- The workflow of the PresentationDesigner must be minimally impacted
   by the adoption of ZPT. In particular, TAL syntax errors will
   constantly remind the designer that they are working in a new
   paradigm. A paradigm not seamlessly supported by their HTML editing
   tool.

Solution
========

Mark the saved page as broken. When the page is rendered through a
browser (from Zope), it should display detailed information (an *error
log*) about what is wrong.

In addition, embed the *error log* in the original page. This way, the
next time the page is loaded into the editor, the error will be
evident. The designer will need to adopt a new process in which he
reloads a page from Zope (through WebDAV or FTP) in order to make sure
that a saved page has no errors.

A page with an error should contain a block of comments (after the
DOCTYPE, XML declaration or the first place where a comment is legal)
with text containing representing the *error log*, presented in such a
manner that it is very clear that something is wrong. The log should
be as verbose as necessary to point out exactly where the error
occurs, the nature of the error and, perhaps, hints about how to fix
it.

.. note:: 21march2001 - This has been reworked to address some of the concerns raised
          by Comments #1, #2 and #3*

Comments
========


Duncan (Mar 13, 2001 9:49 am; Comment #1)  --
 One option to consider would be to allow the client software to
 always succeed when it attempts to save some text, whether or not it
 is valid XML.

 If the saved page is valid, then it is compiled and everything works
 as usual. If the saved text is invalid, then the actual text is saved
 and returned to the user on request, but any attempt to use the
 template will throw an exception with details of the error. Ideally
 the error message should also be displayed to the user when the
 invalid text is saved.

 My reason for suggesting this is quite simple. If a user can get in a
 situation where they cannot save their work, and potentially cannot
 spot their mistake, this is a bad thing. But if the user is allowed
 to save at any point, then they or someone else can come back and fix
 the problem later.

evan (Mar 14, 2001 5:22 pm; Comment #2)  --
 I agree with Duncan. Saves should almost always succeed, except where
 the document is clearly nothing like HTML (eg. user accidentally
 tries to save a .jpeg to a template). If the page contains errors
 that prevent compilation, the list of errors is saved, and an
 indication is returned along with the success code (if possible). Now
 the template is in a broken state, and attempts to render it produce
 an error report instead, with complete descriptions of what needs to
 be fixed. Also, whenever the source is loaded, an HTML comment block
 is prepended. This would require scanning for and removing this block
 on each save.

 Now, we have two strategies. After each save you reload the page into
 either your editor, or into a browser window open to the page on
 which you're working. In the editor, a comment will appear right at
 the start, and in the browser a complete error report will appear.

gvanrossum (Mar 20, 2001 12:28 pm; Comment #3)  --
 The main text is inconsistent with Evan's last comment. Is the error
 inserted in the source as an HTML comment or as an HTML 'div'
 section?

 If we use a 'div', I question where to insert it. Right after 'html'
 would mean just before the 'head' element which might make the 'head'
 section placed invalid according to the DTD (?). Maybe a comment is
 safer? That could go almost right at the top (but after <?xml...?>
 and after <!DOCTYPE ...>).

 [Gosh I hate structured text.]
tcoram (Mar 21, 2001 9:46 am; Comment #4)  --
 Oops. Not after 'html'. That was a mistake, I meant after 'body'...
 But, anyway, it probably makes more since to include the errors in
 comments. For visual editors, like Dreamweaver, my concern was that
 comments are hidden in visual mode and the designer wouldn't notice
 the erorr log. However, Dreamweaver (at least DW4) shows a
 yellow-highlighted question marks where comments should be (which can
 be expanded on demand).

 Commments make a lot of sense. That way we aren't mucking with the
 *visual* structure of the document: If they want to live with an
 error (to be caught later?) they can.
