============================
 Frequently Asked Questions
============================

.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/FAQ.stx

.. highlight:: html

Q1: What are Page Templates (ZPT)?
   See :doc:`VisionStatement` for an overview.

Q2: Will ZPT replace DTML?
  ZPT is an *alternative* to DTML and may replace most if not all of its
  usage for a particular audience. If you are a PresentationDesigner, you
  may find ZPT more useful than DTML.

Q3: Is DTML being deprecated?
   No, we encourage new projects (and developers) to take a look at ZPT and
   see if it fits. At DC, we will probably use ZPT as much as possible on
   new projects. However, if DTML is a better fit...

Q4: Can I use ZPT without knowing XML?
    Yes, you can use it without knowing anything about XML.  ZPT's
    AttributeLanguage was designed for compatibility with XML markup syntax,
    but this mostly means that it is simple and very well-defined.

Q4a: So, Why XML?
    As the Web moves to XHTML_, XML will
    be the common underlying markup format. There is enough flexibility
    through the usage of namespaces, that we can work within the structure of
    XML to do the DTMLish things we wish to do. Also, we expect more HTML
    editors, in the future, to support XML than DTML ;-)

Q4b: Okay, but why be restricted to working within attributes?
    Defining our own elements was certainly a possibility. Unfortunately,
    we expect that most HTML editors will continue to want to deal with
    HTML, not arbitrary XML. Defining your own element set (through a DTD or
    Schema) requires a lot of processing overhead on the editor side in order
    to do anything useful with that markup. Part of the core idea of ZPT is
    to allow [PresentationDesigner]s to concentrate on the *presentation* (the
    HTML) rather than some newly defined elements that don't visually render
    themselves in the editor.

Q5: Does ZPT require XHTML?
    ZPT will work within plain old HTML.  The ZPT markup exists in
    attributes of your HTML tags, so if your HTML editor leaves
    unfamiliar attributes alone, it should work with ZPT templates.

Q6: What is XHTML (briefly, please)?
    Loosely speaking, XHTML_ is HTML with stricter
    syntax and semantics.  As
    a rule, all tags must have *end tags*. And, all attributes must follow
    a syntax of 'attributename = "something always in quotes"'. Also,
    certain elements cannot contain other elements as children. Thats pretty
    much it (with apologies to XML language lawyers).

Q7: My favorite editor produces plain old HTML. Can I use ZPT?
    Yes, you have a couple of choices. The best is to use an editor that
    supports XML (meaning: it doesn't muck with your elements or use of
    *namespaced* attributes). If your editor doesn't mess with
    stuff like this::

        <span tal:define="x str:1" />

    Then you can probably use ZPT.

    However, you can also use a tool like `HTML Tidy <http://tidy.sourceforge.net/>`_
    to convert your HTML to XHTML_ (which ZPT naturally
    speaks). The side effect of this is that
    your output doesn't match your original source! This is not an optimal
    solution.

Q8: What HTML editors work well with ZPT?
    We are still compiling a list, but on the commercial (industrial
    strength) front, GoLive and Dreamweaver 4 work well with ZPT.
    Recent versions of `Amaya <http://www.w3.org/Amaya/>`_ work well in HTML mode.

Q9: Ideally, how does one use an HTML editor with ZPT?
    ZPT wants to be fairly tightly integrated with your editing process.
    Right now, this means using the ZPT HTML edit form (yuck) or using
    a HTML editor that works with FTP or `WebDAV <http://www.webdav.org/>`_.
    Since ZPT expects you
    to retrieve your editable sources from Zope, this is the most natural
    way to work: Zope has the canonical sources for the site.

Q10: Why does ZPT talk about TAL and METAL being non-Zope specific?
    We thought that these ideas were so cool, that we shouldn't
    hog them all to ourselves. We, of course, expect to have the
    most butt kicking implementation of the specs, but we wanted to make
    sure we had a general enough specification so that people who *steal*
    the idea (and all good ideas should be stolen!), will not produce
    widely different implementations. We need convergence, not divergence!

Q11: Does this mean that they will be proposed as standards to a body such as the W3C?
    Currently, we have no plans to propose TAL and METAL as a standard.
    As a practical specification, TAL and METAL need to be able to evolve
    as needed for applications using template systems based on TAL and METAL,
    and there's no need to wait for outside standards bodies to approve
    new versions of the specifications.

Q12: How do I run the tests?  Should they all pass?
    The TAL README.txt file describes how to run the test suite for TAL.  If
    you do not have Expat installed, the XML tests will not pass.  (All recent
    versions of Python include Expat as a matter of course.)  The
    ZPT tests can be run by executing run.py from the tests directory.

Q13: Does acquisition work with these path expressions in TALES?
    Path expressions such as ``here/master_page/macros/copyright`` must always
    start with a builtin or defined variable ("here", in this case).  Each
    step in the path will take advantage of acquisition if the current object
    supports it, so "master_page" may be acquired from "here".  This is
    different from DTML, where every name is searched for in a large, diverse
    set of locations many of which support acquisition.
    locations

Q14: How do I write comments that won't be included in the rendered page?

    Use 'tal:replace="nothing"' or 'tal:condition="nothing"', like this::

      <span tal:replace="nothing">A Comment</span>
      <span tal:replace="nothing"><-- A Commented Comment --></span>

Q15: When I use tal:attributes to set the 'src' or 'href' attribute of a tag, it turns my ampersands into &amp;amp;s!  How can I stop it?

    You can't, and shouldn't.  According to the standard, escaping is the
    proper thing to do here.  It works properly with every browser we've tried.

.. _XHTML: http://www.w3.org/MarkUp

Comments
========

flight (May 15, 2001 7:52 am; Comment #7) *Editor Remark Requested* --
 Regarding Q8/Q9 (HTML editors with ZPT): Somebody should fill in some tips how to use (X)Emacs with PageTemplates.

 How do I set up a PageTemplate to startup the correct Emacs mode ? Which Emacs modes are preferable for editing PageTemplates (elaborate on psgml, html-mode, hm--html-mode, xml-mode etc.pp.) ? Is it possible to augment the mode's definitions with TAL, METAL and perhaps TALES descriptions ?

Anonymous User (Jun 11, 2001 4:53 am; Comment #8) *Editor Remark Requested* --
 In my view, a good FAQ should provide links to the current
 specifications.

Anonymous User (Jan 7, 2002 9:40 pm; Comment #10)  --
 I've been using HTML_Kit from http://www.chami.org, its shareware, Windows only. Has FTP built-in and Tidy.

 Would be handy if ZPT's would return a long FTP response code with the error message if there's a problem in the template during a STOR.

Anonymous User (Mar 20, 2002 8:58 pm; Comment #11) *Editor Remark Requested* --
 A comment on Q15:  while I agree that ampersands etc. should be quoted, there is a slight problem when one tries to place quoted characters themselves in a 'src' or 'href' attribute.  For example, while &amp; is handled correctly, the equally valid &#38; is not, and gets turned into &amp;#38; and won't be properly recognized by any browser.  The code should probably ignore ampersands that are already part of escape sequences.
