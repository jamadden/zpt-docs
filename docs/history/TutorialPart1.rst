===========================
 Tutorial: Getting Started
===========================


Introduction
============

Page Templates are a web page generation tool. They help programmers
and designers collaborate in producing dynamic web pages for Zope web
applications. Designers can use them to maintain pages without having
to abandon their tools, while preserving the work required to embed
those pages in an application.

The goal is natural workflow. A designer will use a WYSIWYG HTML
editor to create a template, then a programmer will edit it to make it
part of an application. As required, the designer can load the
template back into his editor and make further changes to its
structure and appearance. By taking reasonable steps to preserve the
changes made by the programmer, he will not disrupt her application.

Page Templates aim at this goal by adopting three principles:

1. Play nicely with editing tools.

2. What you see is very similar to what you get.

3. Keep code out of templates, except for structural logic.

A Page Template is a model of the pages that it will generate. In
particular, it is a valid HTML page. Since HTML is highly structured,
and WYSIWYG editors carefully preserve this structure, there are
strict limits on the ways in which the programmer can change a page
and still respect the first principle.

Who Are They For?
=================

Page Templates are for programmers and designers who need to work
together to create dynamic web pages. If you create and edit all of
your web pages with a text editor, you might not care for Page
Templates. Then again, they can be simpler to use and understand than
the alternatve, DTML.

Why Yet Another Template Language?
==================================

There are plenty of template systems out there, some of them
quite popular, such as ASP, JSP, and PHP.  Why invent
another?

First, none of these template systems are aimed at HTML designers.
Once a page has been converted into a template, it is invalid HTML,
making it difficult to work with outside of the application. Each of
them violates the first or second principle of Zope Page Templates to
one degree or another. Programmers should not "hijack" the work of the
designers and turn HTML into software. XMLC, part of the Enhydra
project, shares our goal, but requires the programmer to write
substantial amounts of Java support code for each template.

Second, all of these systems suffer from failure to separate
presentation, logic, and content (data). Their violations of the third
principle decrease the scalability of content management and website
development efforts that use these systems.

Applying The Principles
=======================

Page Templates have a template language (TAL) that consists
of special tag attributes.  For example, a dynamic page title
might look like this::

      <title tal:content="here/title">Page Title</title>

The 'tal:content' attribute is a TAL statement. Since it has an XML
namespace (the 'tal:' part) most editing tools will not complain that
they don't understand it, and will not remove it. It will not change
the structure or appearance of the template when loaded into a WYSIWYG
editor or a web browser. The name 'content' indicates that it will set
the content of the 'title' tag, and the value "here/title" is an
expression providing the text to insert into the tag.

This example also demonstrates the second principle; When you view the
template in an editor, the title text will act as a placeholder for
the dynamic title text. The template acts like an example of how
generated documents should look.

There are template commands for replacing entire tags, their contents,
or just some of their attributes. You can repeat a tag several times
or omit it entirely. You can join parts of several templates together,
and specify simple error handling. All of these capabilities are used
to generate document structures. You **can't** create subroutines or
classes, write loops or multi-way tests, or easily express complex
algorithms.

The template language is deliberately not as powerful and
general-purpose as it could be. It is meant to be used inside of a
framework (such as Zope) in which other objects handle business logic
and tasks unrelated to page layout.

For instance, template language would be useful for rendering an
invoice page, generating one row for each line item, and inserting the
description, quantity, price, and so on into the text for each row. It
would not be used to create the invoice record in a database or to
interact with a credit card processing facility.
