=================
 Repeat Variable
=================

The repeat variable is a :doc:`TALES` builtin variable that contains a
dictionary. This dictionary has an entry for each enclosing :doc:`TAL`
'repeat' statement, with a key equal to the variable name defined in
the statement. The value of the entry is a ZTUtils Iterator, which
keeps track of the state of the 'repeat' statement.

You can access the contents of the repeat variable using path
expressions or Python expressions. In path expressions, you write a
three-part path consisting of the name 'repeat', the statement
variable's name, and the name of the information you want. In Python
expressions, you use normal dictionary notation to get the Iterator,
then attribute access to get the information. For example::

    <p tal:repeat="thing things">
       <span tal:replace="repeat/thing/index">1</span>.
       <span tal:replace="thing">The Thing</span>
    </p>

    <p tal:repeat="thing things">
      <span tal:replace="python:repeat['thing'].index">1</span>.
      <span tal:replace="thing">The Thing</span>
    </p>

    <table border="1">
      <tr tal:repeat="row python:range(10)">
        <td tal:repeat="column python:range(10)">
    <span tal:define="x repeat/row/number; y repeat/column/number; z python:x*y"
          tal:replace="string:$x * $y = $z">1 * 1 = 1</span>
      </td></tr>
    </table>

If the same variable name is reused in a nested 'repeat', the inner
one's entry hides the entry for the outer one's for all expressions
inside of the inner 'repeat' statement.

The following information is available from an Iterator:

* *index* - repetition number, starting from zero.
* *number* - repetition number, starting from one.
* *even* - true for even-indexed repetitions (0, 2, 4, ...).
* *odd* - true for odd-indexed repetitions (1, 3, 5, ...).
* *start* - true for the starting repetition (index 0).
* *end* - true for the ending, or final, repetition.
* *length* - length of the sequence, which will be the total number
  of repetitions.
* *letter* - count reps with lower-case letters: "a" - "z", "aa" - "az",
  "ba" - "bz", ..., "za" - "zz", "aaa" - "aaz", and so forth.
* *Letter* - upper-case version of *letter*.
