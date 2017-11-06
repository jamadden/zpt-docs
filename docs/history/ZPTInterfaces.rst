================
 ZPT Interfaces
================

ExpressionCompiler
  The expression compiler interface provides the
  ability to compile expressions to speed later rendering. A trivial
  expression compiler could simply return uncompiled expression source.

ExpressionContextFactory
  An interface for creating expression contexts.

ExpressionContext
  An interfaces for objects that evaluate previously
  compiled expressions, while maintaining the evaluation context.

TALImplementation
  This is the interface provided by TAL
  and METAL implementations.
