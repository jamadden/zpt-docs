=====================
 Tutorial:  More TAL
=====================

.. highlight:: html

Mixing and Matching Statements
==============================

As you have seen in the example template, you can put more
than one TAL statement on the same tag.  There are three
limits you should be aware of, however.

1. Only one of each kind of statement can be used on a single
tag.  Since HTML does not allow multiple attributes with the
same name, you can't have two 'tal:define' on the same tag.

2. Both of 'tal:content' and 'tal:replace' cannot be used on
the same tag, since their functions conflict.

3. The order in which you write TAL attributes on a tag does
not affect the order in which they execute.  No matter how
you arrange them, the TAL statements on a tag always execute
in the following order: 'define', 'condition', 'repeat',
'content' / 'replace', 'attributes'.

To get around these limits, you can add another tag and
split up the statements between the tags.  If there is no
obvious tag type that would fit, you can use 'span' or 'div'
if it is appropriate for the output to contain an element,
or use a 'tal:block' element to have the start and end tags
omitted from the document.

For example, if you want to define a variable for each
repetition of a paragraph, you can't place the 'tal:define'
on the same tag as the 'tal:repeat', since the definition
would happen before all of the repetitions.  Instead, you
would write either of the following::

  <div tal:repeat="p phrases">
    <p tal:define="n repeat/p/number">
    Phrase number <span tal:replace="n">1</span> is
    "<span tal:replace="p">Phrase</span>".</p>
  </div>

  <p tal:repeat="p phrases">
    <span tal:define="n repeat/p/number">
    Phrase number <span tal:replace="n">1</span> is
    "<span tal:replace="p">Phrase</span>".</span>
  </p>

  Statements with Multiple Parts

If you need to set multiple attributes on a tag, you
can't do it by placing multiple 'tal:attributes' statements
on the tag, and splitting them across tags is useless.

Both the 'tal:attributes' and 'tal:define' statements can
have multiple parts in a single statement.  You separate the
parts with semicolons (';'), so any semicolon appearing in an
expression in one of these statements must be escaped by
doubling it (';;').  Here is an example of setting both the
'src' and 'alt' attributes of an image::

  <img src="default.jpg"
       tal:attributes="src item/icon; alt item/id">

Here is a mixture of variable definitions::

  <span tal:define="global logo here/logo.gif; ids here/objectIds">

String Expressions
==================

String expressions allow you to easily mix path expressions
with text.  All of the text after the leading 'string:' is
taken and searched for path expressions.  Each path
expression must be preceded by a dollar sign ('$').  If it
has more than one part, or needs to be separated from the
text that follows it, it must be surrounded by braces ('{}').
Since the text is inside of an attribute value, you can only
include a double quote by using the entity syntax
"&quot;".  Since dollar signs are used to signal path
expressions, a literal dollar sign must be written as two
dollar signs ('$$'). For example::

  "string:Just text."
  "string:&copy; $year, by Me."
  "string:Three ${vegetable}s, please."
  "string:Your name is ${user/getUserName}!"

Nocall Path Expressions
=======================

An ordinary path expression tries to render the object
that it fetches.  This means that if the object is a function,
Script, Method, or some other kind of executable thing, then
expression will evaluate to the result of calling the object.
This is usually what you want, but not always.  For example,
if you want to put a DTML Document into a variable so that
you can refer to its properties, you can't use a normal path
expression because it will render the Document into a string.

If you put the 'nocall:' expression type prefix in front of a
path, it prevents the rendering and simply gives you the
object.  For example::

  <span tal:define="doc nocall:here/aDoc"
        tal:content="string:${doc/id}: ${doc/title}">
  Id: Title</span>

This expression type is also valuable when you want to define
a variable to hold a function or class from a module, for use
in a Python expression.

Python Expressions
==================

A Python expression starts with 'python:', followed by an
expression written in the Python language.  See the section
on writing Python expressions for more information.

Other Builtin Variables
=======================

You have already seen some examples of the builtin variables
'template', 'user', 'repeat', and 'request'.
Here is a complete list of the other builtin variables and
their uses:

- *nothing*: a false value, similar to a blank string, that
  you can use in 'tal:replace' or 'tal:content' to erase a
  tag or its contents.  If you set an attribute to 'nothing',
  the attribute is removed from the tag (or not inserted),
  unlike a blank string.

- *default*: a special value that doesn't change anything
  when used in 'tal:replace', 'tal:content', or
  'tal:attributes'.  It leaves the template text in place.

- *options*: the *keyword* arguments, if any, that were
  passed to the template.

- *attrs*: a dictionary of attributes of the current tag in
  the template.  The keys are the attributes names, and the
  values are the original values of the attributes in the
  template.

- *root*: the root Zope object.  Use this to get
  Zope objects from fixed locations, no matter where your
  template is placed or called.

- *here*: the object on which the template is being called.
  This is often the same as the *container*, but can be
  different if you are using acquisition.  Use this to get
  Zope objects that you expect to find in different places
  depending on how the template is called.

- *container*: the container (usually a Folder) in which the
  template is kept.  Use this to get Zope objects from
  locations relative to the template's permanent home.

- *modules*: the collection of Python modules available to
  templates.  See the section on writing Python expressions.

Alternate Paths
===============

The path 'template/title' is guaranteed to exist every time
the template is used, although it may be a blank string.
Some paths, such as 'request/form/x', may not exist during
some renderings of the template.  This normally causes an
error when the path is evaluated.

When a path doesn't exist, you often have a fallback path or
value that you would like to use instead.  For instance, if
'request/form/x' doesn't exist, you might want to use 'here/x'
instead.  You can do this by listing the paths in order of
preference, separated by vertical bar characters ('|')::

  <h4 tal:content="request/form/x | here/x">Header</h4>

Two variables that are very useful as the last path in a list
of alternates are 'nothing' and 'default'.  Use 'nothing' to
blank the target if none of the paths is found, or 'default'
to leave the example text in place.

You can also test the existence of a path directly with the
'exists:' expression type prefix.  A path expression with
'exists:' in front of it is true if the path exists, false
otherwise.  These examples both display an error message only
if it is passed in the request::

  <h4 tal:define="err request/form/errmsg | nothing"
      tal:condition="err" tal:content="err">Error!</h4>

  <h4 tal:condition="exists:request/form/errmsg"
      tal:content="request/form/errmsg">Error!</h4>

Dummy Elements
==============

You can include page elements that are visible in the
template but not in generated text by using the builtin
variable 'nothing', like this::

  <tr tal:replace="nothing">
    <td>10213</td><td>Example Item</td><td>$15.34</td>
  </tr>

This can be useful for filling out parts of the page that
will take up more of the generated page than of the template.
For instance, a table that usually has ten rows will only
have one row in the template.  By adding nine dummy rows, the
template's layout will look more like the final result.

Inserting Structure
===================

Normally, the 'tal:replace' and 'tal:content' statements
quote the text that they insert, converting '<' to '&lt;',
for instance.  If you actually want to insert the unquoted
text, you need to precede the expression with the 'structure'
keyword.  Given a variable 'copyright', the following two
lines::

  <span tal:replace="copyright">Copyright 2000</span>
  <span tal:replace="structure copyright">Copyright 2000</span>

might generate "&amp;copy; 2001 By &lt;b&gt;Me&lt;/b&gt;"
and "&copy; 2001 By <b>Me</b>" respectively.

This feature is especially useful when you are inserting a
fragment of HTML that is stored in a property or generated by
another Zope object.  For instance, you may have news items
that contain simple HTML markup such as bold and italic text
when they are rendered, and you want to preserve this when
inserting them into a "Top News" page.  In this case, you
might write::

  <p tal:repeat="article topnewsitems"
     tal:content="structure article">A News Article</p>


Comments
========

jwm (Aug 8, 2001 7:12 pm; Comment #2) *Editor Remark Requested* --
 Multiple Defines::

       &lt;span tal:define="global logo here/logo.gif; ids here/objectIds">

 'global' is a keyword used per variable definition, so in this case
 'logo' becomes global, will 'ids' stays local to the span. A bit like
 you'd expect, really :-)

 Strings:

 How about some examples of them in action straight after the
 construction examples. I find when I hit something I don't quite
 understand I tend to stall, and in this case I didn't see where I'd
 use a string construct until I read the fairly unrelated example for
 'nocall' below it.

 So we'd have something like this::

        "string:Just text."
        "string:� $year, by Me."
        "string:Three ${vegetable}s, please."
        "string:Your name is ${user/getUserName}!"

 Which are used like this::

     <span tal:replace="string:Interesing text">Boring text</span>
     <p tal:content="string:You are logged in as ${user/getUserName}">You are logged in as someone.</p>

 Actually on further reading, I think just mentioning that we're
 talking about 'expresion type prefixes' that are things that appear
 inside the attribute strings created when you use a 'statement' (ie,
 any of tal:define, etc) just before statements would clear this up.

benster (Jun 29, 2002 11:00 pm; Comment #4) *Editor Remark Requested* --
 Under Mixing and Matching Statements:

 ::

   > For example, if you want to define a variable for each
   > repetition of a paragraph ...

 Where is the paragraph defined? Is it a string variable? It's not a
 string expression, judging from the examples. It would be helpful to
 see this paragraph being defined.

 FredDrake --
   The paragraph is the 'p' element shown in the example.  There's
   nothing magical about this.


mux_07 Fri Feb 2 13:29:02 -0800 2007

  Perhaps a good example for tal:attributes would be the style
  attribute, which demonstrates a need to double the semi-colon e.g.
  ``<span tal:attributes="style
  string:font-size:${item/size}em;;font-size-adjust:${item/sizeAdjust};;">Sized
  Font</span>``
