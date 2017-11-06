===============
 Loop Examples
===============

Here is a template which present a list of URLs to the user. Each URL
is constructed by 'str' and its base name is displayed in an unordered
list.

.. highlight:: html

.. note:: This examples uses the StandardLookAndFeel macro.

::

 <?xml version="1.0" ?>
 <html xmlns:tal="http://xml.zope.org/namespaces/tal"
       xmlns:metal="http://xml.zope.org/namespaces/metal"
       metal:use-macro="here/StandardLookAndFeel/macros/master">
 <head>
  <title>Untitled Document</title>
  <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
 </head>
   <body>
     <div metal:fill-slot="main">
       <p>Welcome to the machine</p>
       <p>Choose your type:</p>
        <ul>
          <li tal:repeat="type python:'digital', 'analog', 'organic'">
           <a href="dummy"
              tal:attributes="href string:/mach/$type"
              tal:content="type">selection</a>
          </li>
        </ul>
     </div>
   </body>
 </html>





Produces (something like)::

      <p>Welcome to the machine</p>
      <p>Choose your type:
        <ul>
          <li tal:repeat="type python:'digital', 'analog', 'organic'">
           <a href="/mach/digital"
              tal:attributes="href string:/mach/$type"
              tal:content="type">digital</a>
          </li>
          <li tal:repeat="type python:'digital', 'analog', 'organic'">
           <a href="/mach/analog"
              tal:attributes="href string:/mach/$type"
              tal:content="type">analog</a></li>
          <li tal:repeat="type python:'digital', 'analog', 'organic'">
           <a href="/mach/organic"
              tal:attributes="href string:/mach/$type"
              tal:content="type">organic</a>
          </li>

        </ul>
      </p>



Comments
========


richard (Apr 6, 2001 1:27 am; Comment #6)  --
 A much simpler example: displaying information from a ZSQL method::

  <table>
   <tr tal:repeat="row here/my_zsql_method">
      <td tal:content="row/column1">Column 1 dummy data</td>
      <td tal:content="row/column2">Column 2 dummy data</td>
   </tr>
  </table>
