====================
 Search and Replace
====================

.. highlight:: html

Problem
=======

There are a number of cases in which it is not possible to put a tag
at a place in a page where you want to insert text. These include the
inside of '<script>', '<style>', and '<![CDATA[' tags.
Most of these cases can probably be handled by dynamically including
the entire tag, since the contents are likely to either be irrelevant
to structured editing or very hard to mark for dynamic replacement
without ruining structured editing. For example, most editors will not
parse (much less execute) scripts, and while they may use a
stylesheet, it will be hard to make a valid stylesheet that is also
dynamic.

Proposal
========

* Case-by-case: Make a TAL statement that specifies placeholder/expression
  pairs, and replaces all contained instances of the placeholder text with
  the value of the expression.

  For example::

      <script tal:put="IMGDIR here/images/absolute_url">
      for (var i=0; i < elements.length; i++)
        if (elements[i].type == 'img')
          elements[i].src = 'IMGDIR' + elements[i].src;
      </script>

* Notation: Make a TAL statement that specifies placeholder delimiters, and
  replaces all contained path expressions with the specified delimiters::

    <script tal:paths="${ }">
    for (var i=0; i < elements.length; i++)
      if (elements[i].type == 'img')
        elements[i].src = '${here/images/absolute_url}' + elements[i].src;
    </script>

Comments
========

peterbe (Jul 16, 2001 3:49 am; Comment #1) *Editor Remark Requested* --
 -1
 Why not something like this::

  <script tal:replace="peter here/title">
   alert("You are in the peter page");
  </script>

 Which is rendered as::

  <script>
   alert("You are in the My Title page");
  </script>

 Disabling the script and style tag will make it impossible to test
 (as in to test it **with** the script) offline zpt files in
 Dreamweaver or GoLive.

 Keeps the number of TAL operators down. Will it confuse with
 'tal:replace="here/title"' ??

d.maurer (Jul 16, 2001 3:50 am; Comment #2)  --
 In the example
        for (var i=0; i < elements.length; i++)
          if (elements[i].type == 'img')
            elements[i].src = 'IMGDIR' + elements[i].src;

 I would prefer, if replacement were not done based on literal text
 but would use the variable syntax used elsewhere in TALES, e.g.
 $IMGDIR would be replaced by the value of "IMGDIR".

 Also note, that such replacements will often require context specific
 quoting. Probably, a set of quoting functions should be made
 available, including "url_quote", "html_quote", "string_quote".

d.maurer (Jul 16, 2001 3:51 am; Comment #3)  --
 About this:
     o Notation: Make a TAL statement that specifies placeholder delimiters, and
     replaces all contained path expressions with the specified delimiters::

      <script tal:paths="${ }">
      for (var i=0; i < elements.length; i++)
        if (elements[i].type == 'img')
          elements[i].src = '${here/images/absolute_url}' + elements[i].src;
      </script>

 I do not understand what is proposed here.

peterbe (Jul 16, 2001 3:52 am; Comment #4)  --

 oh fk, my structured text was mangled. I hope you see what it was
 meant to be.

mindlace (Jul 18, 2001 1:51 pm; Comment #5)  --
 +1 "dummy" text can be real for offline purposes.

poster (Jul 31, 2001 11:51 am; Comment #6)  --
 +1 for both proposals
