===================================
 Get Template Source Collaboration
===================================

When we get a templates source, we want to expand macros
references.

Objects

  PT -- The page template

  PT2 -- Another page template

  TI -- An object that implements the TALImplementation interface.

  CTAL -- A compiled TAL data structure returned from a TAL compile
     call.

  CTAL2 -- ditto.

  EEF -- An object that implements the ExpressionEngineFactory interface.

  EE -- An object that implements the ExpressionEngine interface.

  cexpr -- A compiled expression. This will be context-dependent. For
     example, when we are executing a define. this will be a different
     expression for each definition.

Sequence

  - PT calls EEF.create(data)

    to create an expression engine. The expression is passed names
    to be included in the standard namespace. The call returns an expression
    engine, EE.

  - PT calls TI.getSource(CTAL, EE)

    When a use-macro is encountered,

    - TI calls EE.macro(cexpr)

       The expression engine evaluates the expression. This is likely
       to involve traversal into a page template, which will
       ask the TAL implementation for the macro.

       PT2 calls TI.findMacro(CTAL2, name)
