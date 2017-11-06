==============================================
 Strategies for Error-handling at Render time
==============================================

As of version 1.2, TAL has the ability to handle render-time errors
using the ``on-error`` statement. Multiple ``on-error`` statements can be
nested, and information about the error is provided to the handler's
expression. You can create a single error handler on the top element,
or provide individual handlers for several parts of a template, or
both.

Note that compile-time errors, such as TAL or TALES syntax errors, are
*not* handled by ``on-error`` statements.

Each ``on-error`` statement is akin to a Python ``try:except:`` block
with a single ``except:`` handler. If you want to handle exception
selectively, you will need to write a script that examines the error
information and decides whether to return text, re-raise the
exception, or raise a new exception.

The simplest strategy for handling errors, apart from not handling
them, is to use a literal string. For example,
``tal:on-error="string:Server Error"`` indicates that an error has
occured, while allowing the rest of the page to render normally.

You can also use an expression that cancels the action. For example,
``tal:on-error="(if) nothing"`` will leave the template text
unchanged, allowing it to serve as error text.

A more complex handler could insert an error icon, with a Javascript
link to pop up error diagnostics for sophisticated users.
Alternatively, it could insert an HTML comment or element attributes
that give error details.

Guido recommends that ``on-error`` be used **very sparingly**, only to
catch errors that are caused by external things such as failing
database retrievals; it should also be used **very selectively**,
meaning to put the ``on-error`` around *just* the element that may
fail.

Very common "errors", such as missing variables in a form submission,
can be handled by a pre-processing script, or by TALES expressions
that test for variable existence.
