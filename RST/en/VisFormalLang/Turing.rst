.. This file is part of the OpenDSA eTextbook project. See
.. http://algoviz.org/OpenDSA for more details.
.. Copyright (c) 2012-2013 by the OpenDSA Project Contributors, and
.. distributed under an MIT open source license.

.. avmetadata::
   :author: Cliff Shaffer
   :topic: Turing Machines

Turing Machines
===============

Turing Machines
---------------

A General Model of Computation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We would like to define a general model of computation that is as
simple as possible.
The reason is that we want to be able to understand the limits of what
is possible in computing, but that is rather hard to do with a
complicated definition for a "computer" is.
But then, we need to be confident that whatever model we do pick, that
it actually represents all of the fundamental capabilities of a
"computer".

"State machines" are simple to understand.
There are a number of different state machines, with a range of
capabilities.
We will discuss a particular one, called a :term:`Turing machine`.
As we define "capability", the key is *ability*, not *efficiency*.

The necessary capabilites for any such "machine" are these:

* Read
* Write
* Compute

A Turing machine is defined as follows.
It has a tape, divided into squares, with an extending infinitely left and
right ends.
Each square can store one character.
The machine has a single I/O head that at any instant in time is "on"
one of the squares.
The control unit of the machine is defined by a set of abstract
:term:`states <state>`.
At any given instant, the machine is said to be
"in" one of the states, and has a set of actions that can be performed
when in that state.
From the current state, the machine will read the symbol on the
current state, and can then do the following:

* reading a single symbol from a tape,
* overwriting that symbol with a new symbol,
* and moving the tape head either left (:math:`L`), right (:math:`R`), or
  stay (:math:`S`)

By convention, the machine ceases if there is not valid transition
for the given input or if the control unit sends the machine into a
:term:`final state`. (By comparison, some other definitions of a TM
require an explicit reject state qreject, but OpenFLAP does not.)

The input to the machine is the intial contents of the tape, which is
described by listing all of the tape squares from the leftmost to the
rightmost non-blank tape cells.
Naturally, there must be a finite number of non-blank symbols on the
tape.
The :term:`alphabet` of the machine consists of some letters,
including the special symbol :math:`\#` which means a blank symbol on the
given square.

A Turing machine is formally defined as
(:math:`Q`, :math:`\Sigma`, :math:`\Gamma`, $s$, :math:`q_0`,
:math:`F`, :math:`\delta`) where

* :math:`Q` is a finite set of states (not including :math:`h`, the
  halt state).
* :math:`\Sigma` is an alphabet.
* :math:`\Gamma` is an tape symbols (containing :math:`\Sigma` and
  :math:`\#` which means an empty cell).
* :math:`s \in Q` is the :term:`initial state`.
* :math:`q_0` is the initial state.
* :math:`F` is the set of final states.
* :math:`\delta` is a function from :math:`Q \times \Gamma` to
  :math:`Q \times \Gamma \times \{L, R, S\})`.

Note that including :math:`\#` in the language is for convenience
only.
We want to be able to read our specifications without being confused.

If :math:`q \in Q`, :math:`a \in \Sigma` and
:math:`\delta(q, a) = (p, b, m)`,
then when in state :math:`q` and scanning :math:`a`,
enter state :math:`p`, replace :math:`a` with :math:`b`, and move the head
(:math:`m` is :math:`L`, :math:`R`, or :math:`S`).

.. topic:: Example

   :math:`M = (Q, \Sigma, \Gamma, s, q_0, F, \delta)` where

   * :math:`Q = \{q_0, q_1\}`,
   * :math:`\Sigma = \{a\}`,
   * :math:`\Gamma = \Sigma \cup \{\#\}`,
   * :math:`s = q_0`,
   * :math:`F = {q_1}`,
   * :math:`\delta =`

     .. math::

        \begin{array}{lll}
        \hline
        q&\gamma&\delta(q, \gamma)\\
        \hline
        q_0&a&(q_0, \#, R)\\
        q_0&\#&(q_1, \#, S)\\
        \end{array}

   This machine will scan right, changing any :math:`a` that it sees
   to a :math:`\#`.
   When it first hits a :math:`\#`, it will halt.

The following diagram shows the machine states and transitions.

.. inlineav:: RClearCON dgm
   :links: DataStructures/FLA/FLA.css AV/VisFormalLang/TM/RClearCON.css
   :scripts: lib/underscore.js DataStructures/FLA/FA.js AV/Development/formal_language/TuringMachine.js AV/VisFormalLang/TM/RClearCON.js
   :align: center


Turing Machines can accept Regular Languages and Context-Free Languages.
In the following example, we have a TM that accepts :math:`L(a^*b^*c^*)`.

.. topic:: Example

   :math:`M = (Q, \Sigma, \Gamma, s, q_0, F, \delta)` where

   * :math:`Q = \{q_0, q_1, q_2, q_3\}`,
   * :math:`\Sigma = \{a, b, c\}`,
   * :math:`\Gamma = \Sigma \cup \{\#\}`,
   * :math:`s = q_0`,
   * :math:`F = {q_2}`,
   * :math:`\delta =`

     .. math::

        \begin{array}{lll}
        \hline
        q&\sigma&\delta(q, \sigma)\\
        \hline
        q_0&a&(q_0, a, R)\\
        q_0&b&(q_1, b, R)\\
        q_0&a&(q_3, c, R)\\
        q_0&a&(q_2, \#, S)\\
        q_1&a&(q_1, b, R)\\
        q_1&c&(q_3, c, R)\\
        q_1&a&(q_2, \#, S)\\
        q_3&a&(q_3, c, R)\\
        q_3&a&(q_2, \#, S)\\
        \end{array}

   Turing machine that accepts :math:`L(a^*b^*c^*)` then halt.

.. inlineav:: TuringMachinesExample2CON dgm
   :links: DataStructures/FLA/FLA.css AV/VisFormalLang/TM/TuringMachinesExample2CON.css
   :scripts: lib/underscore.js DataStructures/FLA/FA.js AV/Development/formal_language/TuringMachine.js AV/VisFormalLang/TM/TuringMachinesExample2CON.js
   :align: center

Interpreting Turing Machines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A :term:`configuration` for a Turing machine looks like this:

.. math::
   (q, aaba\#\underline{\#}a)

This means that the TM is on state :math:`q`,
the tape contains :math:`aaba\#\underline{\#}a` and read / write
head position is on the underlined :math:`a`. In this book, any TM
starts running with the read/write head position is on the first Tape letter
from the left.


A :term:`halted configuration` occurs when the machine do not find
a move from the given state using the tape letter (the current configuration).
In other words, TM halts if there is no :math:`\Delta` defined. That is why
in this book we always assume that there are no transitions defined
out of the final state. Therefore, any TM will halt once it entered a
final state.


A :term:`computation` is a sequence of configurations for some
length :math:`n \geq 0`.

.. topic:: Example

   Recall the TM example that earases all a's from the tape.
   Here are the cofigurations for the input aaaa

   .. math::

      \begin{eqnarray*}
      (q_0, \underline{a}aaa) &\vdash_M&(q_0, \underline{\#}aaa)\\
      &\vdash_M&(q_0, \#\underline{\#}aa)\\
      &\vdash_M&(q_0, \#\#\underline{\#}a)\\
      &\vdash_M&(q_0, \#\#\#\underline{\#})\\
      &\vdash_M&(q_1, \#\#\#\#\underline{\#})\\
      \end{eqnarray*}
      

:math:`M` is said to **halt on input :math:`w`** iff
:math:`(s, w\underline{\#})` yields some halted configuration.

:math:`M` is said to **hang on input :math:`w`** if
:math:`(s, w\underline{\#})` yields some hanging configuration.
That means go into an infinite loop.


Turing Acceptors and Trurng Transducers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Turing machines compute functions from strings to strings.
Formally: Let :math:`f` be a function from :math:`\Sigma^*_0` to
:math:`\Sigma^*_1`.
Turing machine :math:`M` is said to **compute** :math:`f` when,
for any :math:`w \in \Sigma^*_0`, if :math:`f(w) = u` then

.. math::

   (s, \#w\underline{\#}) \vdash^*_M (h, \#u\underline{\#}).

Such a function :math:`f` is said to be a :term:`Turing-computable function`.

Here is how we express multiple parameters:
For :math:`f(w_1, ..., w_k) = u`,

.. math::

   (s, \#w_1\#w_2\#...\#w_k\underline{\#}) \vdash^*_M (h, \#u\underline{\#}).

One way to express functions on natural numbers is to represent a
number using :term:`unary notation`.
(Remember, we are not concerned about is efficient, we are concerned
about what is possible.)
In this case, we represent the value 0 as an empty string.
We say that :math:`f: \mathbb{N} \rightarrow \mathbb{N}`
is computed by :math:`M` if :math:`M`
computes :math:`f': \{I\}^* \rightarrow \{I\}^*` where
:math:`f'(I^n) = I^{f(n)}` for each :math:`n \in \mathbb{N}`.

.. topic:: Example

   Compute :math:`f(n) = n + 1` for each :math:`n \in \mathbb{N}`.

   .. math::

      \begin{array}{lll}
      \hline
      q&\sigma&\delta(q, \sigma)\\
      \hline
      q_0&I&(h, R)\\
      q_0&\#&(q_0, I)\\
      \end{array}

   An example computation:

   .. math::

      (q_0, \#II\underline{\#}) \vdash_M (q_0, \#II\underline{I}) \vdash_M
      (h, \#III\underline{\#}).

   In general,
   :math:`(q_0, \#I^n\underline{\#}) \vdash^*_M (h, \#I^{n+1}\underline{\#})`.
   What about :math:`n = 0`?
   The input is no marks in unary, and it works OK.


Turing-Decideable vs. Turing-Acceptable Languages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A language :math:`L \subset \Sigma_0^*` is :term:`Turing-decidable`
iff function :math:`\chi_L: \Sigma^*_0 \rightarrow \{\fbox{Y}, \fbox{N}\}`
is Turing-computable, where for each :math:`w \in \Sigma^*_0`,

.. math::

   \chi_L(w) = \left\{
   \begin{array}{ll}
   \fbox{Y} & \mbox{if $w \in L$}\\
   \fbox{N}  & \mbox{otherwise}
   \end{array}
   \right.

Example: Let :math:`\Sigma_0 = \{a\}`, and let
:math:`L = \{w \in \Sigma^*_0: |w|\ \mbox{is even}\}`.

:math:`M` erases the marks from right to left, with current parity
encode by state.
Once blank at left is reached, mark :math:`\fbox{Y}` or
:math:`\fbox{N}` as appropriate.

There are many views of computation.
One is functions mapping input to output
(:math:`N \rightarrow N`, or
strings to strings, for examples).
Another is deciding if a string is in a language.

:math:`M` :term:`accepts <accept>` a string :math:`w` if :math:`M`
halts on input :math:`w`.

* :math:`M` accepts a language iff :math:M` halts on :math:`w` iff
  :math:`w \in L`.
* A language is :math:`Turing-acceptable` if there is some Turing
  machine that accepts it.

Example: :math:`\Sigma_0 = \{a, b\}`,
:math:`L = \{w \in \Sigma^*_0: w\ \mbox{contains at least one}\ a\}`.

.. math::

   \begin{array}{lll}
   \hline
   q&\sigma&\delta(q, \sigma)\\
   \hline
   q_0&a&(h, a)\\
   q_0&b&(q_0, L)\\
   q_0&\#&(q_0, L)\\
   \hline
   \end{array}

Is this language Turing decidable?
Of course. Instead of just running left, invoke another state that
means "seen an :math:`a`", and print :math:`\fbox{Y}` if we reach
:math:`\#` in that state, :math:`\fbox{N}` otherwise.

Every Turing-decidable language is Turing-acceptable,
because if the machine would have printed :math:`\fbox{Y}`,
then the machine can halt instead,
or if the machine would have printed :math:`\fbox{N}`,
then it can hang left.

Is every Turing-acceptible language Turing decidable?
This is the Halting Problem.

Of course, if the Turing-acceptible language would halt,
we write :math:`\fbox{Y}`.
But if the Turing-acceptible language would hang,
can we *always* replace it with logic to write :math:`\fbox{N}`
instead?
Example: Collatz function.


Making More Complicated Machines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Lemma**: If

.. math::

   (q_1, w_1\underline{a_1}u_1) \vdash_M^* (q_2, ww_2\underline{a_2}u_2)

for string :math:`w` and

.. math::

   (q_2, w_2\underline{a_2}u_2) \vdash^*_M (q_3, w_3\underline{a_3}u_3),

then

.. math::

   (q_1, w_1\underline{a_1}u_1) \vdash^*_M (q_3, ww_3\underline{a_3}u_3).

Insight: Since
:math:`(q_2, w_2\underline{a_2}u_2) \vdash^*_M (q_3, w_3\underline{a_3}u_3)`,
this computation must take place without moving the head left of :math:`w_2`
The machine cannot "sense" the left end of the tape.
(And if it had moved left, it would have hung.)
Thus, the head won't move left of :math:`w_2` even if it is not at the
left end of the tape.

This means that Turing machine computations can be combined into
larger machines:

* :math:`M_2` prepares string as input to :math:`M_1`.
* :math:`M_2` passes control to :math:`M_1` with I/O head at end of input.
* :math:`M_2` retrieves control when :math:`M_1` has completed.

Here are some basic machines and notation

* :math:`|\Sigma|` symbol-writing machines (one for each symbol):
  Any give letter :math:`\sigma` has a symbol-writing machine named
  :math:`\sigma`.
* Head-moving machines, named :math:`R` and :math:`L`, move the head
  appropriately.
* Start state indicated with :math:`>`.
* Transitions on anything other than (for example) :math:`\#` are
  labeled :math:`\overline{\#}`
* Multiple copies of a machine get a superscript: :math:`R^2` means
  move right twice.


.. inlineav:: Turing1CON dgm
   :links: AV/SeniorAlgAnal/Turing1CON.css
   :scripts: AV/SeniorAlgAnal/Turing1CON.js
   :align: center

   First do :math:`M_1`, then do :math:`M_2` or :math:`M_3` depending
   on current symbol.

|

.. inlineav:: Turing2CON dgm
   :links: AV/SeniorAlgAnal/Turing2CON.css
   :scripts: AV/SeniorAlgAnal/Turing2CON.js
   :align: center

   (For :math:`\Sigma = \{a, b,c\}`) Move head to the right until a
   blank is found.
   We will use the notation :math:`R_{\#}` for this process.

|

.. inlineav:: Turing3CON dgm
   :links: AV/SeniorAlgAnal/Turing3CON.css
   :scripts: AV/SeniorAlgAnal/Turing3CON.js
   :align: center

   Two views of a simple machine to find the first blank square to the
   left, and then transition to machine :math:`M`.
   The version on the left shows this in greater detail.
   In the more abstract notation on the right, we use the notation
   :math:`L_{\#}`, and the transition to :math:`M` on the horizontal
   line is assumed to occur on seeing the first :math:`\#` symbol.

|

.. inlineav:: TuringCopyCON dgm
   :links: AV/SeniorAlgAnal/TuringCopyCON.css
   :scripts: AV/SeniorAlgAnal/TuringCopyCON.js
   :align: center

   Copy Machine: Transform :math:`\#w\underline{\#}` into
   :math:`\#w\#w\underline{\#}`.
   Note the difference between :math:`L_{\#}` in the start state
   (which means move left until seeing the first blank), and
   :math:`L\#` at the bottom (which means move left and then write a
   space).

|

.. inlineav:: TuringShiftCON dgm
   :links: AV/SeniorAlgAnal/TuringShiftCON.css
   :scripts: AV/SeniorAlgAnal/TuringShiftCON.js
   :align: center

   Shift a string right.


Turing Machine Extensions
~~~~~~~~~~~~~~~~~~~~~~~~~

When we give extentions or new functionality to a computing system,
sometimes they change something fundamental about the capabilies of
the system.
For example, when we add non-determinism to an algorithm, we **might**
change the cost of the underlying problem from exponential to
polynomial time.
But, other changes do nothing fundamental.
In terms of Turing machines, our concern is what the machine can do,
rather than how long it takes to do it.
Does non-determinism help us to solve the Halting problem?
No.
Likewise, the following extensions do not increase the power of Turing
Machines.

* Provide a two-way infinite tape

  .. inlineav:: TuringExt1CON dgm
     :links: AV/SeniorAlgAnal/TuringExt1CON.css
     :scripts: AV/SeniorAlgAnal/TuringExt1CON.js
     :align: center

  This does not give Turing machines new capability.
  To make this clear, we can simulate the behavior of a two-way
  infinite tape using a standard one-way infinite tape.
  Just bend infinite tape in the middle, and store both directions of
  the tape into a single cell.
  This requires a greatly expanded alphabet, because we now need to be
  able to represent any combination of two characters.
  This will need more states, and probably more time.
  But it does not allow anything new in terms of capability.

* Multiple tapes (each with its own head)

  Again, we can simulate this with encoding multiple symbols into a
  single table cell.
  For example, to simulate two tapes (each with a head), we encode in
  each cell the corresponding two symbols, and a two binary markers to
  indicate if the tape head is currently in the corresponding cell of
  the two tapes.

  .. inlineav:: TuringExt2CON dgm
     :links: AV/SeniorAlgAnal/TuringExt2CON.css
     :scripts: AV/SeniorAlgAnal/TuringExt2CON.js
     :align: center

* Multiple heads on one tape

  This is easier than encoding multiple tapes.
  We merely encode the heads onto the tape, and simulate moving them
  around.

* A two-dimensional ``tape``

  All that we need to do is find a mapping from 2D to 1D, which is
  fairly easy.
  One approach is to work in diagonals, in the order (0, 0), (0, 1),
  (1, 0), (0, 2), (1, 1), (2, 0), and so on.

  .. inlineav:: TuringExt3CON dgm
     :links: AV/SeniorAlgAnal/TuringExt3CON.css
     :scripts: AV/SeniorAlgAnal/TuringExt3CON.js
     :align: center

* Non-determinism

  We can simulate nondeterministic behavior in sequence, doing all
  length 1 computations, then length 2, etc., until we reach a halt
  state for one of the non-deteriministic choices.
  So we see that while non-determinism can save a lot of time, it does
  not change what can (eventually) be done.
