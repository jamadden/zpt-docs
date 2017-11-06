.. from
   https://github.com/zopefoundation/zpt-docs/blob/master/src/IntroductionForDTMLers.stx

==============================================
 A DTML user's Introduction to Page Templates
==============================================

.. rubric:: DTML is good

DTML solves a lot of problems, and solves them well. You can use it to
generate XML, HTML, RDF, SQL, or any other sort of text your heart
desires. You have access to the full power of Zope, through the
namespace, and to Python, through expressions.

.. rubric:: DTML has problems

For all that you may be familiar with DTML now, and love it to death,
you can probably remember a time when it made you tear your hair out.
Its syntax is quirky and unpredictable. Some tag attributes use names,
some use literal strings, and some can use Python expressions, but
there's not much rhyme or reason to it. Access to names in the
namespace that contain punctuation, or names which must be reached via
a path, are painful to use.

If you do all of your page design in vi, emacs, or some other
text-based editor, you're probably content to replace large, arbitrary
chunks of your page with '<dtml-var foo>'. On the other hand, if you
use a visual editor or work with someone who does, you've exerienced
the pain of having to reconcile DTML's dynamicism with the visual
representation of a page (or page fragment).

.. rubric:: DTML will go on

Nobody who uses DTML wants to see it disappear. We will continue to
mai`ntain and improve it, and may well incorporate the more successful
ideas from Page Templates. It will continue to be the language of
choice for generating unstructured text (like SQL or CSV).

Page Templates are better (where they apply)
============================================

:doc:`TAL`, :doc:`TALES` and :doc:`METAL` are our simplest,
most straighforward attempt to design templates for HTML that still
have enough expressiveness to get the job done. Each of them covers a
single layer of template functionality in a cleanly extensible
fashion. Forget the cutesy acronyms for a moment, and follow the
reasoning:

Expressions
===========

Just as in DTML, most of the grunt work of Page Templates involves
expressions; You need to perform calculations and comparisons, and
refer to objects. When you refer to a Zope object, you want to be able
to use its Id, or a path of Ids, including Ids with odd characters in
them. You want to be able to use simple Python expressions. You want
to be able to write literal strings, and interpolate variables into a
string. Perhaps at some point you'll discover that there's a whole new
notation that you would like to be able to use (eg. Perl). These are
the requirements that produced :doc:`TALES`

Referring to Zope objects by name is one of the clunkier bits of DTML,
and it is made worse by the "everything in one pile" namespace DTML
uses. In TALES, the namespace is replaced by paths. There are a
small number of "contexts" that you use as starting points for
referring to objects. They are similar to Script bindings, and include
"root", "here", "container", and "template", among others. You pick a
context, then write a slash-separated path from the context to the
object. For example, you refer to object "big-wig" in the same Folder
as the Template by the path "container/big-wig". You can acquire "dead
parrot" from "shop", and "shop" from the object on which the Template
was called with "here/shop/dead parrot". Any character that is valid
in a Zope Id is allowed in a path. Contexts are just builtin
variables, and a path can start with any variable, so if you have
defined variable "ick" as the path "here/shop/dead parrot", you can
use "ick/name" to get the parrot's "name" property.

In DTML, sometimes you can use a Python expression in an attribute,
sometimes you must, and sometimes you can't. Every place that you can
use a TALES expression, you can use Python. You simply prefix the
Python expression with "python:", as in "python:2 + 2" or
"python:string.split(x, '','')".

Similarly, you can always provide a literal string by using the
expression type prefix "string:". Thus, "string:This isn't bad" is a
valid TALES expression. You can insert variables values into string
expressions using "$varname" (or "${varname}") syntax. If variable
"name" refers to "Evan" and "thing" refers to "cat", then
"string:$name likes ${thing}s" produces "Evan likes cats".

TALES is designed to be extended by simply defining new expression
types. If you wanted to use XPath notation, you could write an XPath
expression engine, register it with TALES, and start writing
expressions like "xpath:...".

Statements
==========

Here is a simple example of how you might use statements from DTML
and from Page Templates to insert the value of an expression::

        <p>Ten times X is <b><dtml-var expr="x * 10"></b></p>

        <p>Ten times X is <b z:insert="python: x * 10">(x * 10)</b></p>

In the DTML version, you use the made-up tag "dtml-var" with an "expr"
attribute to hold the expression. In the PT version, you add TAL
attribute "z:replace" to the HTML "b" tag. If you loaded a page
containing each of these into a WYSIWYG editor, the DTML tag would
probably not show up, or would be replaced with some kind of icon,
while the PT version would look like: <b>(x * 10)</b>.

DTML Tags are the statements of the DTML language. They are completely
independent of the text in which they are embedded, and they don't pay
any attention to any structure or grammar that the text may possess.
If you mark up HTML with DTML carefully, you can produce a composite
document that an HTML editing tool can tolerate, or can be made to
tolerate. On the other hand, it's easy to write DTML that an editing
tool considers complete gibberish, but that renders into perfectly
good HTML. For this reason (among others) it is highly unlikely that
many HTML tools will be adapted to deal well with DTML.

Page Templates' TAL statements are attributes of ordinary HTML
(or XML) tags. This means that you can only use TAL with HTML or XML
text, but Zope generates a lot of HTML. Most of the popular HTML tools
ignore and preserve tag attributes that they don't recognize. Since
TAL attributes have their own XML namespace (a prefix like 'z:' or
'tal:' that you choose), they don't interfere with the validity of XML
documents. Thus, HTML or XML that you have marked up with TAL is
likely to be perfectly compatible with tools that know nothing about
Zope.

Write an HTML document as XHTML, or at least very tidily, and it has a
nice easy-to-parse tree structure. This allows us to frame template
operations in terms of repeating, replacing, removing, or changing
attributes of tags. You can handle almost all cases where this model
doesn't fit the dynamicism of your document by adding the odd '<span>'
or '<div>', just as you might when applying CSS classes.

Variables and Scoping
=====================

Much of the difficulty of DTML lies in using the DTML namespace. This
is a powerful, difficult to control tool, especially when combined
with acquisition. It is all too easy to accidentally obscure a name,
and often hard to access names deep in the stack. There are also
security issues posed by the fact that DTML code cannot be sure that
any name in the namespace is bound to what it expected to find.

Page Templates solve this, by providing your TALES expressions with
a set of builtin variables. If there is an object named 'helper' in
the same container as your template, you can reliably access it as
'container/helper'.

You can define your own global or local variables with the
'define="var expr"' statement. Local variables are like the names
bound by a '<dtml-let>'; Their scope is the statement tag and its
content. Global variables are more like 'REQUEST.set' names, since
they are in scope from the start of the statement tag to the end of
the template.

Template Operations
===================

Just as '<dtml-var foo>' is the most fundamental statement of DTML,
'replace="expr"' and 'content="expr"' are the basic workhorses of
TAL. The first replaces the tag on which you place it with the value
of the expression "expr", while the other inserts the value in place
of the tag's contents. As you saw above, the TALES expression "expr"
can evaluate to any Zope object, string, or Python-computed value.

You can write comments that don't show up in a rendered page, or
comment out part of a page, by putting 'replace="nothing"' on a tag.
TAL inside of the tag will not be executed.

Rather than replacing an entire tag, you can set tag attributes with
'attributes="attrname expr"'. This allows you to have reasonable dummy
values for the attributes that a WYSIWYG editor can deal with. For
example::

        <textarea name="fred" rows="10" cols="60"
          z:attributes="rows request/my_rows; cols request/my_cols">

Instead of '<dtml-in>', TAL uses 'repeat="var expr"'. This repeats the
tag on which you place it once for each element of the sequence
"expr". In each repetition, the local variable "var" is bound to the
current element of the sequence, and repeat variable "var" gives you
access to the TAL equivalent of 'sequence-number'. For example::

        <p z:repeat="thing here/collection">
          Paragraph #<span z:replace="repeat/thing/number">1</span>:
          <span z:replace="thing">something</span>.
        </p>

The 'condition="expr"' statement is similar to '<dtml-if>', but more
basic. If the expression is true, the statement does nothing. If the
expression is false, the tag and its contents is skipped. There is no
equivalent to '<dtml-else>' or '<dtml-elif>'.

The Macros Saga
===============

There are times when the power of '<dtml-var>' can be a problem. You
can't tell, just by inspecting DTML source, whether '<dtml-var x>'
inserts a string, performs a catalog search, sends mail, or even
restarts Zope! Templates often include other templates, or parts of
them, and it can be useful to know when that is only thing happening.

If you have some way to tell that '<dtml-var sidebar>' inserts a
sidebar template, then you can solve the problem faced by designers
who use WYSIWYG tools. When they load a template page into their
editor, insert the sidebar template so that it appears normally in the
page, instead of as a little placeholder symbol. When they save it
back to Zope, convert the sidebar back into a reference.

:doc:`METAL` is the language that allows this sort of behavior. It
is similar to TAL, in that it consists of tag attributes in an XML
namespace. You can use it to define *macros*, by placing a
'define-macro="name"' attribute on a tag that you want to re-use.
METAL affects template rendering, of course, inserting any macros
that the template uses just before TAL executes, it *also* inserts
macros whenever you get the source code for the template. You can
create libraries of macros, with all of the advantages of code reuse,
yet still see *everything* in a page when you edit it.

Macros can only be changed by editing the definition. If you load a
template into an editor, and change a tag that has a
'use-macro="expr"' attribute, these changes will have no effect.

Only whole tags can be macros, so you can't make a
'standard_html_header' and 'standard_html_footer' macro. Instead, you
would create a 'standard_page' template, make the entire 'html' tag a
macro, and place a 'define-slot="main"' METAL attribute on the
'body' tag. Slots are tags within a macro that can be replaced when
the macro is inserted. They are replaced if the 'use-macro' statement
tag contains a 'fill-slot="name"' statement tag, where 'name' matches
the name of a slot in the macro. To use your 'standard_page' template,
you place 'use-macro="here/standard_page/macros/page"' on your page's
'html' tag, and 'fill-slot="main"' on its 'body' tag. Now the 'html'
tag and all of its contents *other than* the body tag will be replaced
with the macro body. To see what this looks like, check out the
[Examples].



Comments
--------


peterbe (May 10, 2001 5:09 am; Comment #2)  --
 This doesn't work::

  <p z:repeat="thing here/collection">
     Paragraph #<span z:replace="loop:thing/number">1</span>:
     <span z:replace="thing">something</span>.
  </p>


 Products.PageTemplates.TALES.CompilerError: Unrecognized expression type "loop".

 And where do I find more info about doing TAL what DTML-IN does?

evan (May 10, 2001 9:37 am; Comment #3)  --
 I have fixed the example. There isn't any good documentation written
 yet on the equivalent of dtml-in in TAL, although one of the examples
 in PageTemplates 1.2 should be informative.

peterbe (May 15, 2001 5:21 am; Comment #4)  --
 As announced before (but then was broken), I have started to compile
 a little list of examples of how to go from DTML to ZPT.
 http://www.zope.org/Members/peterbe/DTML2ZPT_Conversion_examples
