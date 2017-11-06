========================
 Simple Welcome Example
========================

.. highlight:: html

This template provides a simple example for constructing a page that
uses the :doc:`StandardLookAndFeel` macro. The only retained portion
of this template is the content provided to ``fill-slot="main"``.

Original Source::

 <html xmlns:tal="http://xml.zope.org/namespaces/tal"
       xmlns:metal="http://xml.zope.org/namespaces/metal"
       metal:use-macro="here/StandardLookAndFeel/macros/master">
   <body>
     <div metal:fill-slot="main">
       <p>Welcome to the machine</p>
     </div>
   </body>
 </html>

Expanded Results::

 <html xmlns:tal="http://xml.zope.org/namespaces/tal"
       xmlns:metal="http://xml.zope.org/namespaces/metal"
       metal:use-macro="here/StandardLookAndFeel/macros/master">
   <head>
     <title tal:content="here/title">The title</title>
   </head>
   <body bgcolor="#999999">
     <div metal:fill-slot="main">
       <p>Welcome to the machine</p>
     </div>
     <p tal:content="structure here/ZopeAttributionButton"></p>
   </body>
 </html>

.. highlight:: text

Comments
========


peterbe (Apr 25, 2001 9:53 am; Comment #1) *Editor Remark Requested* --
 Can't get this to work!

 ::

        metal:use-macro="here/StandardLookAndFeel/macros/master">

 Initially:
  Win2k Zope 2.3.0 & PageTemplates-1.1.1.tar.gz & TAL-1.1.0.tar.gz
  No ParsedXML product installed (is this the reason?)

 'here' is a protected word right? 'here' is like 'self' in py modules
 and 'context' in python scripts. Right?

 So I create a PageTemplate in /test with id: "standard_template" and
 this 'metal:define-macro="master">' Another PageTemplate to use the
 'master' macro with id: index_html
 "metal:use-macro="here/standard_lookandfeel/master">"

 Here's the Traceback I get inside "index_html"::

  Macro expansion failed
  Products.PageTemplates.TALES.TALESError: exceptions.AttributeError on __getitem__ in "<PathExpr standard:here/standard_lookandfeel/master>"

peterbe (Apr 25, 2001 11:58 am; Comment #2)  --
 Ahh... Got the syntax right finally.

 ::

        metal:use-macro="here/standard_lookandfeel/macros/master">

 Where 'standard_lookandfeel' is a PageTemplate in the same folder or
 inherited from a folder "below".

 This was tricky. It might be documented and all but it bloody well
 keep me occupied for some long time.
