==================
 Extending Macros
==================

.. highlight:: html

It's often useful to override or elaborate one or more slots from a
macro, or to provide additional slots, possibly as replacements for a
single slot, without having to redefine all the slots of the "base"
macro. This is especially useful with complex macros that may change
frequently. This can almost be thought of as subclassing a macro.

Without the ability to extend macros directly, it becomes necessary
for the macro that wants to extend another to duplicate each of the
slots from the base macro. This is usually done with a stanza of the
form::

    <metal:block fill-slot="slotname">
      <metal:block define-slot="slotname">
        slot filler goes here
      </metal:block>
    </metal:block>

While this isn't the prettiest construct, it works to allow
a template that uses this macro to fill a slot provided by a
macro used by the macro providing this.

If there are many such slots in the "base" macro, however, this
creates a great deal of bloat in the derived macro. Likewise, if the
set of slots defined in the base macro, this is fragile and imposes an
additional burden on the maintainer of the derived macro.

These problems can be avoided by providing an explicit way for
macros to express that they extend other macros.

Desired Semantics
=================

A macro has an interface for clients (either other macros or pages)
defined by the set of slots that can be filled. When a macro is
defined without the extension mechanism, the set of slots is defined
entirely by elements with 'define-slot' attributes in the body of the
macro.

A derived macro's interface is determined a bit differently; the set
of slots includes those provided directly by the macro *and* the slots
of the base macro that are not filled by the derived macro. Clients
can then fill any of the slots, and, if the client is a macro, define
their own slots as well for their clients.

Note that each slot can only be filled once; slots cannot be filled by
both the derived macro and the client; the client can only provide a
filler if the derived macro provides a new slot to replace the one
that was filled in; it may have the same name, but it has a unique
identity.

Let's look at some examples.  Consider the following templates:

    T1

      ::

        <div metal:define-macro="base">
          <div metal:define-slot="A">
            default A filler
          </div>
          <div metal:define-slot="B">
            default B filler
          </div>
          <div metal:define-slot="C">
            default C filler
          </div>
        </div>

    T2

      ::

        <div metal:define-macro="extension"
             metal:extend-macro="T1/macros/base">
          <div metal:fill-slot="A">
            slot A decoration from T2
            <div metal:define-slot="A">
              overridden A filler
              <div metal:define-slot="D">
                default D filler
              </block>
            </div>
          </div>
        </block>

    T3

      ::

        <div metal:use-macro="T2/macros/extension">
          <div metal:fill-slot="A">
            final slot A filler
          </div>
          <div metal:fill-slot="C">
            final slot C filler
          </div>
          <div metal:fill-slot="D">
            final slot D filler
          </div>
        </div>

If T3 is the template to be rendered, we expect to see T3's filler for
T2's slots A and D, T2's filler for T1's A (with T3's filler for T2's
A embedded within it), and T3's filler for T1's slot C::

        <div>
          <div>
            slot A decoration from T2
            <div>
              final slot A filler
            </div>
            <div>
              final slot D filler
            </div>
          </div>
          <div>
            default B filler
          </block>
          <div>
            final slot C filler
          </block>
        </block>

(The indentation here is illustrative, not necessarily
what a processor will provide.)


Syntax
======

A new attribute is used to indicate that one macro extends another.
This attribute, 'extend-macro', takes an expression that identifies
the base macro as the value. The macro identified by that expression
is treated as a source for slots not provided by the derived macro.

The derived macro must provide a name for itself using the
'define-macro' attribute.

The 'use-macro' attribute may not be used in conjunction with
either 'define-macro' or 'extend-macro'.

Implementation
==============

This will be implemented in the 'zope.tal' package and
included in Zope 3.1.0.
