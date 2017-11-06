==============================
 Tutorial:  Template Language
==============================

.. highlight:: html

Creating a Page Template
========================

If you design pages, you will probably use FTP or WebDAV
instead of the Zope Management Interface (ZMI) to create and
edit Page Templates.  See your Zope adminstrator for
instructions.  For the very small examples in this
introduction, it is much easier to use the ZMI.

If you are a Zope administrator or a programmer, you will
probably use the ZMI at least occasionally.  You may also use
emacs, cadaver, or some other client.  See the Zope
Administrator's Guide for instructions on setting up Zope to
to work with various clients.

Use your web browser to log into the Zope management
interface.  Choose a Folder (the root is fine) and pick "Page
Template" from the drop-down add list.  Type "simple_page" in
the add form's 'Id' field, then push the "Add and Edit"
button.

You should now see the main editing page for the new
Page Template.  The title is blank, the content-type is
'text/html', and the default template text is in the editing
area.

Now you will create a very simple dynamic page. Type the
words "a Simple Page" in the 'Title' field.  Edit the
template text to look like this::

  This is <b tal:replace="template/title">the Title</b>.

Now push the "Save Changes" button.  The edit page should
show a message confirming that your changes have been saved.
If some text starting with '<!-- Page Template Diagnostics'
is added to the template, then check to make sure you typed
the example correctly and save it again.  You don't need to
erase the error comment: once the error is corrected it will
go away.

Click on the 'Test' tab.  You should see a mostly blank page
with "This is a Simple Page." at the top.

Back up, then click on the "Browse HTML source" link under
the content-type field. You should see
"This is **the Title**."  Back up again, so that you are
ready to edit the example further.

Simple Expressions
==================

The text "template/title" in your simple Page Template is a
*path expression*.  This the most commonly used of the
expression types defined by the TAL Expression Syntax
(TALES).  It fetches the 'title' property of the template.
Here are some other common path expressions:

  o request/URL: The URL of the current web request.

  o user/getUserName: The authenticated user's login name.

  o container/objectIds: A list of Ids of the objects in the
  same Folder as the template.

Every path starts with a variable name.  If the variable
contains the value you want, you stop there.  Otherwise, you
add a slash ('/') and the name of a sub-object or property.
You may need to work your way through several sub-objects to
get to the value you're looking for.

There is a small built in set of variables, such as 'request'
and 'user', that will be listed and described later.  You
will also learn how to define your own variables.

Inserting Text
==============

In your "simple_page" template, you used the 'tal:replace'
statement on a bold tag.  When you tested it, it replaced
the entire tag with the title of the template.  When you
browsed the source, you saw the template text in bold.  We
used a bold tag in order to highlight the difference.

In order to place dynamic text inside of other text, you
typically use 'tal:replace' on a 'span' tag.  Add the
following lines to your example::

  <br>
  The URL is <span tal:replace="request/URL">URL</span>.

The 'span' tag is structural, not visual, so this looks
like "The URL is URL." when you view the source in an editor
or browser.  Remember to take care when editing not to
destroy the 'span' or place formatting tags such as 'b' or
'font' inside of it, since they would also be replaced.

If you want to insert text into a tag but leave the tag
itself alone, you use 'tal:content'.  To set the title of
your example page to the template's title property, add the
following lines above the other text::

  <head>
  <title tal:content="template/title">The Title</title>
  </head>

If you open the "Test" tab in a new window, the window's
title will be "a Simple Page".

Repeating Structures
====================

Now you will add some context to your page, in the form of a
list of the objects that are in the same Folder.  You will
make a table that has a numbered row for each object, and
columns for the id, meta-type, and title.  Add these lines to
the bottom of your example template::

  <table border="1" width="100%">
    <tr>
      <th>#</th><th>Id</th><th>Meta-Type</th><th>Title</th>
    </tr>
    <tr tal:repeat="item container/objectValues">
      <td tal:content="repeat/item/number">#</td>
      <td tal:content="item/id">Id</td>
      <td tal:content="item/meta_type">Meta-Type</td>
      <td tal:content="item/title">Title</td>
    </tr>
  </table>

The 'tal:repeat' statement on the table row means
"repeat this row for each item in my container's list of
object values".  The repeat statement puts the objects from
the list into the 'item' variable one at a time, and
makes a copy of the row using that variable.  The value
of "item/id" in each row is the Id of the object for that row.

The name 'item' is not special; You can use any name you like for
the variable, as long as it starts with a letter and contains only
letters, numbers, and underscores ('_').  It only exists in the tag
that the 'tal:repeat' statement is in, in this case the
'tr' tag; If you tried to use it above or below that tag
you would get an error.

Each row in the example is numbered, which requires knowing the
number of the current repetition.  You might also like to know whether
a particular row is the first or last row, or similar information.
In each copy of the row, the variable 'item' contains an object from
the list.  This object doesn't know anything about the repetition, so
you can't use 'item/somename' to get this sort of information.
This is what the builtin 'repeat' variable is for.  By placing the name
of the repeating variable after 'repeat' in a path, you can access
the repetition count from zero ('index'), from one ('number'),
from "A" ('Letter'), and in several other ways. So, the
expression 'repeat/item/number' is '1' in the first row, '2'
in the second row, and so on.

Since one 'tal:repeat' can be placed inside of another, more
than one can be active at the same time.  This is why you
must write 'repeat/item/number' instead of just
'repeat/number' to get information about the
'tal:repeat' that creates the 'item' variable.

Conditional Elements
====================

View the template, and you'll notice that the table is very
dull looking.  Let's improve it by shading alternate rows.
Copy the second row of the table, then
edit it so that it looks like this::

  <table border="1" width="100%">
    <tr>
      <th>#</th><th>Id</th><th>Meta-Type</th><th>Title</th>
    </tr>
    <tbody tal:repeat="item container/objectValues">
      <tr bgcolor="#EEEEEE" tal:condition="repeat/item/even">
        <td tal:content="repeat/item/number">#</td>
        <td tal:content="item/id">Id</td>
        <td tal:content="item/meta_type">Meta-Type</td>
        <td tal:content="item/title">Title</td>
      </tr>
      <tr tal:condition="repeat/item/odd">
        <td tal:content="repeat/item/number">#</td>
        <td tal:content="item/id">Id</td>
        <td tal:content="item/meta_type">Meta-Type</td>
        <td tal:content="item/title">Title</td>
      </tr>
    </tbody>
  </table>

The 'tal:repeat' has not changed, you have just moved it onto
the new 'tbody' tag.  This is a standard HTML tag meant to
group together the body rows of a table, which is how you are
using it.  There are two rows in the body, with identical
columns, and one has a grey background.

View the template's source, and you see both rows.  If you
had not added the 'tal:condition' statements to the rows,
then the template would generate both rows for every item,
which is not what you want.  The 'tal:condition' statement
on the first row ensures that it is only included on even-
indexed repetitions, while the second row's condition only
lets it appear in odd-indexed repetitions.

A 'tal:condition' statement does nothing if its expression
has a true value, but removes the entire statement tag,
including its contents, if the value is false. The 'odd' and
'even' properties of 'repeat/item' are either zero or one.
The number zero, a blank string, an empty list, and the
builtin variable 'nothing' are all false values.  Nearly
every other value is true, including non-zero numbers, and
strings with anything in them (even spaces!).

Defining Variables
==================

Your template will always show at least one row, since
the template itself is one of the objects listed.  In other
circumstances, you might want to account for the possibility
that the table will be empty.  Suppose you want to simply
omit the entire table in this case.  You can do this by
adding a 'tal:condition' to the table::

  <table border="1" width="100%"
         tal:condition="container/objectValues"

Now, when there are no objects, no part of the table will be
included in the output.  When there are objects, though, the
expression "container/objectValues" will be evaluated twice,
which is mildly inefficient.  Also, if you wanted to change
the expression, you would have to change it in both places.

To avoid these problems, you can define a variable to hold
the list, and then use it in both the 'tal:condition' and the
'tal:repeat'.  Change the first few lines of the table to
look like this::

  <table border="1" width="100%"
         tal:define="items container/objectValues"
         tal:condition="items">
    <tr>
      <th>#</th><th>Id</th><th>Meta-Type</th><th>Title</th>
    </tr>
    <tbody tal:repeat="item items">

The 'tal:define' statement creates the variable 'items', and
you can use it anywhere in the table tag.

Now, suppose that instead of simply leaving the table out
when there are no items, you want to show a message.  To do
this, you place the following above the table::

  <h4 tal:condition="not:container/objectValues">There
  Are No Items</h4>

You can't use your 'items' variable here, because it isn't
defined yet.  If you move the definition to the 'h4' tag,
you can't use it in the 'table' tag any more, because it
becomes a *local* variable of the 'h4' tag.  You could place
the definition on some tag that enclosed both the 'h4' and
the 'table', but there is a simpler solution.  By placing the
keyword 'global' in front of the variable name, you can make
the definition last from the 'h4' tag to the bottom of the
template::

  <h4 tal:define="global items container/objectValues"
      tal:condition="not:items">There Are No Items</h4>
  <table border="1" width="100%" tal:condition="items">

The 'not:' in the first 'tal:condition' is an expression type
prefix that can be placed in front of any expression.  If the
expression is true, 'not:' is false, and vice versa.

Changing Attributes
===================

Most, if not all, of the objects listed by your template have
an 'icon' property, that contains the path to the icon for
that kind of object.  In order to show this icon in the meta-
type column, you will need to insert this path into the 'src'
attribute of an 'img' tag, by editing the meta-type column in
both rows to look like this::

  <td><img src="/misc_/OFSP/Folder_icon.gif"
           tal:attributes="src item/icon">
      <span tal:replace="item/meta_type">Meta-Type</span>
  </td>

The 'tal:attributes' statement replaces the 'src' attribute
of the image with the value of 'item/icon'.  The value of
'src' in the template acts as a placeholder, so that the
image is not broken, and is the correct size.

Since the 'tal:content' attribute on the table cell would
have replaced the entire contents of the cell, including the
image, with the meta-type text, it had to be removed.
Instead, you insert the meta-type inline in the same fashion
as the URL at the top of the page.


Comments
========


peterbe (Jun 25, 2001 11:21 am; Comment #3) *Editor Remark Requested* --
 Perhaps also mention how to use ('parameters')

 ::

   tal:define="items container/objectValues"

 becomes::

   tal:define="items python:container.objectValues('Photo')"

 or if the objectpath containes . dots::

   tal:define="items container/folder.peter/objectValues"


 becomes::

   tal:define="itemsobj container/folder.peter/objectValues/; items python:itemsobj('Photo')"


 Stop and correct me before I make a fool out of myself.

evan (Jun 25, 2001 1:29 pm; Comment #4)  --
 These are good, but belong in part 4, where Python expressions are discussed.
