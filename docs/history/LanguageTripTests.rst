==========================================
 Can you *trip* up TAL, TALES, and METAL?
==========================================

.. epigraph::

   My test programs are intended to break the system, to push it to
   its extreme limits, to pile complication on complication, in ways
   that the system programmer never consciously anticipated. To
   prepare such test data, I get into the meanest, nastiest frame of
   mind that I can manage, and I write the cruelest code I can think
   of; then I turn around and embed that in even nastier constructions
   that are almost obscene.

    -- Donald Knuth, The Errors of TeX (1989)


Things that should work
=======================

Here are a few things that should work. If they don't, then there's
either a bug in the implementation or the specifications have changed
out from under this document.

.. highlight:: html

This one may confuse you, but it shouldn't confuse TAL/TALES::

    <span z:define="global global str:0; local global str:1;;one">
      <span z:define="local local local:global; global global str:2">
       <ul>
        <li z:insert="var:local">1;one</li>
        <li z:insert="var:global">1;one</li>
        <li z:insert="local:global">1;one</li>
        <li z:insert="global:global">2</li>
       </ul>
      </span>
    </span>

This snippet defines a simple macro::

    <table z:define-macro="useless" z:define="local one python:1"
           z:attributes="border var:defaultborder">
      <tr><th>One line table</th></tr>
      <tr z:define-slot="firstrow"><td>firstrow</td></tr>
    </table>

This snippet uses the above macro::

    <table z:use-macro="template/useless" z:define="local one str:one">
      <tr z:fill-slot="firstrow"><td z:insert="var:one">1 or one?</td></tr>
    </table>


Things that should generate errors
==================================

  There are a few things that should generate errors.  We should write them
  down.
