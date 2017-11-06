===============================
 Render Template Collaboration
===============================

In this collaboration, we use a compiled TAL to render text.


Objects

  PT -- The PageTemplate

  PT2 -- Another PageTemplate

  TI -- An object that implements the TALImplementation interface.

  CTAL -- A compiled TAL data structure returned from a TAL compile
     call.

  CTAL2 -- ditto.

  EEF -- An object that implements the ExpressionEngineFactory interface.

  EE -- An object that implements the ExpressionEngine interface.

  cexpr -- A compiled expression. This will be context-dependent. For
     example, when we are executing a define. this will be a different
     expression for each definition.

Sequence of calls

  - PT calls EEF.create(data)

    to create an expression engine. The expression is passed names
    to be included in the standard namespace. The call returns an expression
    engine, EE.

  - PT calls TI.renderText(CTAL, EE)

    As the TALImplementation renders the TAL, it will make calls to EE
    as different TAL statements are executed:

    When a node containing a define or a repeat is encountered (and a
    condition, if present, if true):

    - TI calls EE.beginScope()

    After processing node containing a define or a loop:

    - TI calls EE.endScope()

    when a z:define is executed:

    For each define (in order), either:

    - TI calls EE.setLocal(name, cexpr)

    or

    - TI calls EE.setGlobal(name, cexpr)

    For each condition:

    - TI calls EE.isTrue(cexpr)

    For each repeat:

    - TI calls EE.startRepeat(name, cexpr)

    while 1:

      - TI calls EE.repeat()

        The return value is a boolean. If the value is false, we break
        out of the loop. Otherwise, we render the output node and continue.

    For each insert or replace:

    - TI calls EE.text(cexpr)

        To compute the text to be used. The return value is either
        a string, None, or an object that satisfies the
        DOM text-node interface.

    or

    - TI calls EE.structure(cexpr)

        To compute the structure to be inserted.  The result should
        be a DOM Fragment or None.

    For each attribute to be set:

    - TI calls EE.text(cexpr)

        to compute the attribute text.  The result should be a string or None.

    When a use-macro is encountered,

    - TI calls EE.macro(cexpr)

       The expression engine evaluates the expression. This is likely
       to involve traversal into a PageTemplate, which will
       ask the TAL implementation for the macro.

       PT2 calls TI.findMacro(CTAL2, name)
