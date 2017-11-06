==============================
 Compile Source Collaboration
==============================

In this collaboration, we update template source and prepare create a
TAL object to object to render the template.

Assumptions:

 - Our initial assumption is that we start from an object that
   implements the DOMInterface. An alternative might be to input an
   XML source string.

 - We want to separate the implementation of TAL and TALES

 - The implementation of TAL also implements METAL.

Collaboration
=============

Objects:

PT
  The page template
TI
  An object that implements the TALImplementation interface.
EC
  An object that implements the ExpressionCompiler
  interface.
DOM
  an object that implements the DOM Interface.

Sequence of calls

- PT calls TI.compileFromDOM(DOM, EC)

  When TAL expressions are encountered:

  - TI calls EC.compile(src)

    where src is the expression source string.

    The compiled expression is returned and saved by the TAL
    implementation for later use. The value returned is opaque and
    could be the original source of the expression compiler doesn't do
    anything.

    The TAL compile method returns an opaque data structue that is
    used later to render output. Note that the compiled data contains
    any compiled expressions.
