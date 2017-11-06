================================
 Standard Look And Feel Example
================================

.. highlight:: html

This macro provides the capability of the *out of the box*
'standard_header' and 'standard_footer'. The macro's name is 'master';
it defines just one slot to be filled ('main'). An example template
that uses this macro can be found in :doc:`SimpleWelcome`.

::

 <html xmlns:tal="http://xml.zope.org/namespaces/tal"
       xmlns:metal="http://xml.zope.org/namespaces/metal"
       metal:define-macro="master">
   <head>
     <title tal:content="here/title">The title</title>
   </head>
   <body bgcolor="#999999">
     <div metal:define-slot="main">
     If you supply a tag with a 'fill-slot="main"' attribute
     when using this macro, that tag will replace this text.
     </div>
     <p tal:content="structure here/ZopeAttributionButton"></p>
