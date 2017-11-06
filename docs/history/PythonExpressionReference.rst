.. from
   https://raw.githubusercontent.com/zopefoundation/zpt-docs/master/src/PythonExpressionReference.stx

=============================
 Python Expression Reference
=============================

This is a quick reference guide for using TALES Python
expressions in Zope Page Templates.

Variables and Object Access
===========================

All TALES variables, including builtin, global, and local variables,
can be used in Python expressions. See :doc:`../tales` for more
information. Builtin variable uses are described below:

* *root*: Get properties or sub-objects of the Zope root object.
* *here*: If your template is being applied to a content object, use
  this to access it. You can also use this to acquire objects or
  properties from the current acquisition context.
* *container*: Access objects in the same location as the template,
  without regard to where it is being used.
* *template*: Get the template's title, or use the template's own
  macro definitions elsewhere in itself.
* *request*: Read submitted form contents, environment information,
  and the RESPONSE object. Set request variables for use by other
  objects.
* *user*: Check the user's access rights, or get their login name.
* *modules*: Call Python functions stored in filesystem modules and
  packages. Use subitem notation, with the full dotted module access
  path as the key, like this: "modules!['package.module']".
* *attrs*: Get the default (original) values of the statement
  element's attributes. This is a dictionary, with the attribute name
  as the key.
* *options*: Get keyword arguments passed to the template, when it is
  called from a Method or Script. This is a dictionary.
* *repeat*: Access the iterator information for enclosing TAL repeat
  statements. Use subitem notation, with the repeat variable name as
  the key.
* *default* and *nothing*: Return these as the value of the expression
  to control TAL statements.

You can also use the same builtin functions as in Python Scripts and
DTML namespaces, such as 'str()', 'getattr()', and 'DateTime()'. See
"Appendix A of the Zope Book",
http://www.zope.org/Members/michel/ZB/AppendixA.dtml for more
information. A few of them deserve special mention here:

* *test*: This allows you to choose among two or more alternate value
  based on boolean expressions. This can be very useful for operations
  such as giving different styles to alternating lines of a table::

    tal:define="oddrow repeat/item/odd"
    tal:attributes="class python:test(oddrow, 'odd', 'even')"

* *DateTime*: Get the current date and time, or construct a DateTime
  object from a string or other data.

Path and String Expressions in Python
=====================================

Each of the TALES expression types can be invoked from Python by a
function with the same name. Here are some situations in which other
expression types are useful inside of Python expressions:

* *path*: Since the path function returns a special false value when
  the path cannot be traversed, boolean logic can be used to select
  the first of several paths which both exists and has a true value,
  like this::

    path('here/a') or path('here/b')

* *path*: Fetch an object with a dynamic path::

    path('container/%s/index_html' % folder_name)

* *string*: Fetch an object with a complex dynamic path::

    path(string('here/${options/foo}/${request/bar}'))

Formatting DateTimes
====================

DateTime objects have dozens of methods that you can use to extract
information such as the month, day of the week, etc. See the "DateTime
API Reference", http://www.zope.org/Members/michel/ZB/AppendixB.dtml
for more information. The most general of these is the 'strftime()'
method, which uses a format string with special placeholders, like
this::

  "%b %e, %Y" produces "Jul  4, 2001"
  "%m/%d/%y" produces "07/04/01"

Formatting Numbers and Strings
==============================

Python's formatting operator ('%') can be used for simple numeric
formatting and string concatenation; It is very efficient. See the
"Python Reference",
http://www.python.org/doc/current/lib/typesseq-strings.html for
details. A simple example::

  "Hello %s, you have %s tries and $%.2f left." % (
    name, attempts, dollars)

There is a library of commonly-used formatting functions in the
PythonScripts Product that duplicate format options available in DTML.
You can get the module with a variable definition like this::

  tal:define="pss modules/Products/PythonScripts/standard"

Then you can convert newlines to HTML break tags::

  tal:replace="structure python:pss.newline_to_br(lines)"

format monetary and numeric values::

  tal:replace="python:pss.whole_dollars(money)"
  tal:replace="python:pss.dollars_and_cents(money)"
  tal:replace="python:pss.thousands_commas(n)"

quote strings for safe use in !URLs, or in SQL::

  tal:replace="python:pss.url_quote(anURL)"
  tal:replace="python:pss.url_quote_plus(anURL)"
  tal:replace="python:pss.sql_quote(sql_text)"

or convert structured text into HTML::

  tal:replace="structure python:pss.structured_text(txt)"
