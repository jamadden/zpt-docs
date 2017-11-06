=========================
 Web Site Creation Story
=========================

Zozoban Incorporated designs corporate business cards. Well, that's a
bit of an understatement--they are the premier providers of *very*
trendy business cards for Fortune 1000 companies. In particular, they
are experts in quick turnaround orders for companies that like to
*spin* their image every few months.

Recently, Zozoban has noticed that demand for new card designs surge
just before major industry conferences. This is especially noticable
in the technology industry, where each new conference demands a new
*identity* (new buzzwords are unveiled and must be embraced).

In the past year, the conference demand has made it difficult for
Zozoban to keep up. So, they finally caught a clue and decided to
bring their business more fully to the web. Unfortunately, Internet
World was only 2 months away. Part of the Internet World hype was to
have the flashiest, most time relevant business cards available to
catch the eye of potential clients and partners. New businesses and
old, they all greased up their marketing machinery for the show. Cards
were a big part of this.

Zozoban had no problem generating the cards and delivering them in a
matter of a few days, but working with corporations that wanted minor
tweaks (add a new employee, change the catchphrase or color) took a
toll on their rather small staff. Sue, their innovative new Web
SiteArchitect, suggested that most of this could easily be done
through the Web. Little did Sue realize that they would simply say "Do
it.". Unfortunately, they added "..and have it ready 1 month before
Internet World".

ZPT To The Rescue
=================

Sue enlisted her resident :doc:`PresentationDesigner`, Jeff, to do the mock
ups. She didn't want to wait weeks before getting feedback from her
bosses. Jeff used GoLive to generate an overall look and feel page
that would be the model for all pages on the site. Jeff had no idea
what ZPT was, but he was willing to learn as he went. Sue told him
that she would do the initial structuring, all she wanted from Jeff
was a single page representing the general layout.

Sue downloaded ZPT and installed it into her departmental Zope
instance. She told Jeff to save his page (using WebDAV) into
http::/internal/mastertemps/masterlookandfeel.html. She had previously
created it as an empty page and checked off the 'notify me on changes'
option.

A couple of hours later, Sue was notified by email (by Zope) that Jeff
had modified masterlookandfeel.html. Sue used her own favorite editor,
emacs, to modify the file. She turned the page into a METAL macro by
adding the attribute ``metal:define-macro`` to the ``html`` tag. Jeff had
inserted ``divs`` and ``spans`` into the areas where they expected the
business card application to show through. Sue *paramatized* these
areas as ``slots``. This is where the content would plug in. This also
reminded Sue that she need to get Ulrich, her ZopeProgrammer, started
on developing Zope methods to access the corporate database which held
the card design information as well as client contacts.

Sue's overall design was to have corporate clients log into the site,
look at their current battery of cards (previously designed for them
by Zozoban), and annotate them with the changes they needed. Perhaps
she would also throw in the capability to show them new stock designs
for their casual perusal. Maybe she would even allow *potential*
clients browse such designs without having a corporate login. But, no
matter any new features she could dream of, they would amount to
nothing if she couldn't get a prototype of the site up and running.
Ulrich would work on the back end logic, she could work out the
requirements with him later. Right now, she needed to focus on the
prototype.

Before calling Jeff, Sue created a general page to contain the
application. She called this page, simply enough, "main.html". This
page was wrapped in a ``metal:use-macro`` attribute that would bring
in the master look and feel template. She wrote ``metal:fill-slot``
attributes on tables and text that she wanted to supply to the
template (so that they would show through). This would eventually be
populated with the results of Ulrich's code. But, that wasn't anywhere
near ready, so Sue provided some sample content.

When Jeff got Sue's email, he used GoLive to download "main.html". He
was a bit confused. It looked like Sue had copied his master layout to
this file. Didn't she realize what a waste of duplication that would
be? Glancing back at her email, he noticed she had written that he
should edit the layout of her sample content here, making adjustments
as needed. Her tables were badly formed, and the fonts were all wrong.
Jeff made changes and saved them back to Zope. Jeff also needed to
change the master layout a bit to adjust to the oddly sized table that
would be needed to show the card samples. He brought up
"masterlookandfeel.html" and tweaked the layout. He closed "main.html"
and opened it up again. The master look and feel changes appeared.

to be continued...

Comments
========


smw (Sep 21, 2001 2:43 pm; Comment #1) *Editor Remark Requested* --
 Please consider finishing this, it's great!
