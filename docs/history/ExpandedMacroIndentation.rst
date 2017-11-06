============================
 Expanded Macro Indentation
============================

I think it would be nice if an expanded macro was automatically
indented as far as the ``use-macro`` element that it was replacing.
Currently, this is only true for the starting element of the expanded
macro, other elements are only indented as far as they are in the
macro definition.

This would make the page template with the expanded macros easier to
understand and edit.


Comments
========

alzhimer (Mar 22, 2002 5:50 am; Comment #1)  --
 even nicer would be to have an option to automagically format the
 whole PT according to the structure of the HTML. or at least, at a
 start, keep the indenting as is (now, with "expand macros" enabled it
 would justify every line to the left which makes the PT very hard to
 read).

 The auto-formatted HTML might look something like::

   <html>
   <head>
     <title>The title</title>
   </head>
   <body>
     <h1>Cool Page</h1>
     <p>
       Some Text here...
     </p>
   </body>
   </html>
