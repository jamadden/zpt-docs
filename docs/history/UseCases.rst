.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/UseCases.stx

===========
 Use Cases
===========

*Use cases* describe the functionality of a system from the
point of view of users of the system. *Actors* describe the
unique kinds of roles that users play when interacting with
the system.

Actors

.. toctree::

   PresentationDesigner
   ZopeProgrammer
   SiteArchitect
   GoLive

Use cases

.. toctree::

   WebSiteCreationStory
   DesignerMocksUpSiteLookAndFeel
   ArchitectAssignsFunctionality
   ProgrammerAddsCode


Comments
========


peterbe (Jul 20, 2001 4:29 am; Comment #1)  --
 Here's how I described it to a friend.
 My point was that one can reverse the development cycle around the design

  - the designer designs something stupid as usual and gives it to
    the developer and says "make this HTML dynamic" and the developer changes
    stuff and inserts <dtml- ...> or <% response.write ...%>
    or <cf ...>

    The developer cuts tables into 10 pieces so that they depend on loops and
    stuff. Also, to be smart he cuts his code into many files/objects hence
    header.inc bodytag.inc index.php footer.inc

    Doing this the beginning of a table <table> ends up in the header.inc
    and the end </table> ends up in footer.inc

    After a while when there is empirical evidence that the design was too
    artistic, the HTML is messed up by geeky DTML or ASP or PHP code that
    nobody dares to touch. Project goes to hell!

  - the web application developer prepares a bunch of Python Scripts
    and uses TAL to print it through a template with very simple
    design. he then asks the designer "make this simple HTML use some
    nice colours and CSS and feel free to fuck up the simplicity of it
    if you want to".

  - The designer changes things with the template given from the
    application developer in Dreamweaver and can during his development
    hit F12 to view it in an external browser.

  - After a while when there is empirical evidence that the design was
    too artistic, the boss can ask the designer to redesign the
    template. He can work with the template as if it was plain HTML
    and does not even have to look at the source (if he's careful).

cchoi (Dec 13, 2001 4:52 am; Comment #3) *Editor Remark Requested* --
 Here's my two bits on this:

 Actor

  - IntranetEditor - An authorized user allowed to edit content on an
    intranet who does not necessarily know HTML, but can use wysiwyg
    HTML editors that are WebDAV compliant.


 Use Cases

  - IntranetEditorWritesContentUsingOffice2000

    The idea here is that rank and file users should be able to create
    and edit ZPT files using Office2000 tools via WebDAV. Basic text,
    table entry, and linking is what is desired here, not full-blown
    page layout editing.

    I've tried using MS Word 2000 to read in a ZPT file, edit it, and
    write it back out via WebDAV. And as expected, MS Word writes tags
    in the file that ZPT processing yaks on, in this case tags with
    the characters '[' and ']' in them. Removing such tags got the
    page to render though, and the happy part is, there weren't that
    many tags. Getting ZPT to work seamlessly with Office2000 (in
    particular Word and Excel) would be a huge win.

    Issues

      * How to get IntranetEditors to understand slot and use tags when they
        don't even want to understand HTML

      * Is the many-naive-editors model for an intranet a good idea in the first place?

cchoi (Dec 13, 2001 5:27 am; Comment #4)  --
 More thoughts

 * Zope's WebDAV implementation adopts Zope's security model. WebDAV
   as I currently understand it does not incorporate a security model,
   as Greg Stein `relates <http://www.webdav.org/mod_dav/install.html>`_:

   "The DAV spec (RFC 2518) does not incorporate a security model. It
   relies on any web server and file system security that the
   administrator configures.":


 Observation

   ZPT, Office2000, WebDAV, and Zope's security model almost suggests
   Zope as an Internet accessible file system where many users can do
   work. Is ZODB up for this?

cchoi (Dec 13, 2001 5:43 am; Comment #5)  --
  yet another thought

  How well can ZPT files withstand importing and exporting through
  multiple HTML editors? We know that in theory yes, but in practice
  with the existing HTML editors out there the answer is less clear.
  Has anybody tested this?

  For example,

   * Write a ZPT file in Web Browser

   * Read and write back out ZPT file using the following apps in different sequences:

     - MS Word 97 and 2000
     - Netscape Navigator
     - Mozilla
     - Dreamweaver
     - GoLive
     - [
