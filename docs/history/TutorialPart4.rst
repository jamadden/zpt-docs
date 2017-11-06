=============================
 Tutorial: Advanced Concepts
=============================

Basic Python Expressions
========================

The Python language is a as::

    "python:path('here/%s/thing' % foldername)"
    "python:path(string('here/$foldername/thing'))"
    "python:path('request/form/x') or default"

  The final example has a slightly different meaning than the
  path expression "request/form/x | default", since it will
  use the default text if "request/form/x" doesn't exists
  *or* if it is false.

Getting at Zope Objects
=======================

Much of the power of Zope involves tying together specialized
objects.  Your Page Templates can use Scripts, SQL
Methods, Catalogs, and custom content objects.
In order to use them, you have to know how to
get access to them.

Object properties are usually attributes, so you can get a
template's title with the expression "template.title". Most
Zope objects support acquisition, which allows you to
get attributes from "parent" objects.  This means that the
Python expression "here.Control_Panel" will acquire the
Control Panel object from the root Folder.  Object methods
are attributes, as in "here.objectIds" and "request.set".
Objects contained in a Folder can be accessed as attributes
of the Folder, but since they often have Ids that are not
valid Python identifiers, you can't use the normal notation.
For example, instead of writing "here.penguin.gif", you must
write "getattr(here, 'penguin.gif')".

Some objects, such as 'request', 'modules', and Zope Folders
support item access.  Some examples of this are::

  request['URL'], modules['math'], and here['thing']

When you use item access on a Folder, it doesn't try to
acquire the name, so it will only succeed if there is
actually an object with that Id contained in the Folder.

As shown in previous chapters, path expressions allow you to
ignore details of how you get from one object to the next.
Zope tries attribute access, then item access.
You can write "here/images/penguin.gif" instead of
"python:getattr(here.images, 'penguin.gif')", and
"request/form/x" instead of "python:request.form!['x']".

The tradeoff is that path expressions don't allow you to
specify those details.  For instance, if you have a form
variable named "get", you must write
"python:request.form!['get']", since "request/form/get" will
evaluate to the "get" *method* of the form dictionary.

Using Scripts
=============

Script objects are often used to encapsulate business logic
and complex data manipulation.  Any time that you find
yourself writing lots of TAL statements with complicated
expressions in them, you should consider whether you could do
the work better in a Script.

Each Script has a list of parameters that it expects to be
given when it is called.  If this list is empty, then you can
use the Script by writing a path expression.  Otherwise, you
will need to use a Python expression, like this::

  "python:here.myscript(1, 2)"
  "python:here.myscript('arg', foo=request.form['x'])"

If you want to return more than a single bit of data from a
Script to a Page Template, it is a good idea to return it in
a dictionary.  That way, you can define a variable to hold
all the data, and use path expressions to refer to each bit.
For example::

  getPerson returns this: {'name': 'Fred', 'age': 25}

  <span tal:define="person here/getPerson"
        tal:replace="string:${person/name} is ${person/age}">
  Name is 30</span> years old.


Calling DTML
============

Unlike Scripts, DTML Methods don't have an explicit parameter
list.  Instead, they expect to be passed a client, a mapping,
and keyword arguments.  They use these to construct a
namespace.

When the ZPublisher publishes a DTML object, it passes the
context of the object as the client, and the REQUEST as the
mapping.  When one DTML object calls another, it passes
its own namespace as the mapping, and no client.

If you use a path expression to render a DTML object, it will
pass a namespace with 'request', 'here', and the template's
variables already on it.  This means that the DTML object
will be able to use the same names as if it were being
published in the same context as the template, plus the
variable names defined in the template.

Python Modules
==============

The Python language comes with a large number of modules,
which provide a wide variety of capabilities to Python
programs.  Each module is a collection of Python functions,
data, and classes related to a single purpose, such as
mathematical calculations or regular expressions.

Several modules, including "math" and "string", are available
in Python Expressions by default.  For example, you can get
the value of &pi; from the math module by writing "python:math.pi".
To access it from a path expression, however, you need to use
the 'modules' variable.  In this case, you would use "modules/math/pi".
Please refer to the Zope Book or a DTML reference guide for more
information about these modules.

The 'string' module is hidden in Python expressions by the "string"
expression type function, so you need to access it through
the 'modules' variable.  You can do this directly in an expression
in which you use it, or define a global variable for it, like this::

  tal:define="global mstring modules/string"
  tal:replace="python:mstring.join(slist, ':')"

As long as you're using Python 2.0 or newer, you can avoid this in
many cases using string methods::

  tal:replace="python:':'.join(slist)"

Modules can be grouped into packages, which are simply
a way of organizing and naming related modules.  For instance,
Zope's Python-based Scripts are provided by a collection of
modules in the "PythonScripts" subpackage of the Zope "Products"
package.  In particular, the "standard" module in this package
provides a number of useful formatting functions that are
standard in the DTML "Var" tag.  The full name of this module
is "Products.PythonScripts.standard", so you could get access to it
using either of the following statements::

  tal:define="pps modules/Products.PythonScripts.standard"
  tal:define="pps python:modules['Products.PythonScripts.standard']"

Most Python modules cannot be accessed from Page Templates, DTML, or
Scripts unless you add Zope security assertions to them.  That's
outside the scope of this document, and is covered by the Zope
Security Guide.
