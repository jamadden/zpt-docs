========================
 Module Access In TALES
========================

Problem
=======

DTML allows access to standard modules such as 'string' and 'math'.
Scripts can import and module that has security restrictions. TALES
expressions (especially Python expressions) should have similar
access.

Proposal
========

Add a builtin variable 'modules' that can be used to access Python
modules that have security restrictions. In Python, this would be used
with mapping notation::

    python:modules['string'].split(x)
    python:modules['Products.PythonScripts.standard'].html_quote(x)

The variable would be usable in path expressions also::

    tal:define="join nocall modules/string/join"

Comments
========

richard (Apr 9, 2001 7:11 pm; Comment #1)  --
 I can't help thinking that it'd be more user-friendly for the python:
 case to be able to use an attribute interface rather than a mapping
 interface...

evan (Apr 20, 2001 3:03 pm; Comment #2)  --
 Unfortunately, Python overloads dotted notation for module import and
 for attribute access. 'modules["a"].b' is *not* semantically
 identical to 'modules["a.b"]', since "import a; x=a.b" and "import
 a.b; x=a.b" mean two different things if 'a' is a package.
