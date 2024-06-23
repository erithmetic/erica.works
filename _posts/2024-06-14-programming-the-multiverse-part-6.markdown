---
layout: post
title: "Programming the Multiverse: Part 6 - Kickbacks"
date: 2024-06-14 21:12:17 -0500
categories: quantum_computing
---

It's not just a phase! One tool that QC gives us is an entirely new dimension to work with, which we call phase.

_Note: this post is part of my series, [Programming the Multiverse](/programming-the-multiverse-part-1/)_

We've looked at the internals of a couple gates but let's dive into the Hadamard gate. Here is its internal magic matrix:

$$
\left[ \begin{array}{cc}
  \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}}
  \\
  \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{array} \right]
$$

Wait, what's up with that negative $$\frac{1}{\sqrt{2}}$$?!

Remember our qubit in the $$\ket{0}$$ state, represented by $$1\ket{0} + 0\ket{1}$$. If we put it through the H gate, we get:

$$
\frac{1}{\sqrt{2}}
\left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right]
\left[ \begin{array}{c} 1 \\ 0 \end{array} \right]
=
\frac{1}{\sqrt{2}}
\left[ \begin{array}{c} 1 \\ 1 \end{array} \right]
$$

_Note: the $$\frac{1}{\sqrt{2}}$$ in front of the matrix is a shortcut for having to write the fraction four times inside the matrix. It's a distributive multiplication on all the items._

Now let's feed it a qubit in the $$\ket{1}$$ state, represented by $$0\ket{0} + 1\ket{1}$$.

$$
\frac{1}{\sqrt{2}}
\left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right]
\left[ \begin{array}{c} 0 \\ 1 \end{array} \right]
=
\frac{1}{\sqrt{2}}
\left[ \begin{array}{c} 1 \\ -1 \end{array} \right]
$$

So this means we have a qubit in this state: $$\frac{1}{\sqrt{2}}\ket{0} - \frac{1}{\sqrt{2}}\ket{1}$$ or generalized as $$a\ket{0} - b\ket{1}$$. That negative `b` indicates a **negative phase**.

## A new dimension

Earlier I mentioned that a qubit is like a spinning die where you might decide a roll of 1 through 3 represents a classical bit equal to 0 and a roll of 4 through 6 represents a classical bit equal to 1.

![A screenshot of the above code and drawing in a jupyter notebook](../images/multiverse-part-6/colored-dice.jpg){: height="200" }

Phase is like being able to dynamically set the color of the die. For example we could say when it's blue, it's a positive phase and when it's red it's a negative phase.

The phase does not affect the probability of whether the qubit is a $$\ket{0}$$ or a $$\ket{1}$$. But it comes into play when we apply gates to it.

<!-- Remember when we said the qubit is represented by this formula $$a\ket{0} + b\ket{1}$$ such that $$\vert{a}\vert^{2} + \vert{b}\vert^{2} = 1$$. When we have a negative phase, we put the negative inside b's absolute value, like so: $$\vert{a}\vert^{2} + \vert{-1 * b}\vert^{2} = 1$$ so that the probability still adds up to 1 as expected. So no matter the phase, `a` and `b` remain the same and our probability still adds up. -->

## Getting kickbacks

In fact, the ability to change phase gives us a new tool &mdash; **phase kickback**! Let's see how it works.

In order for the kickback to happen we have to be using a controlled gate, like our CNOT. We also have to put the control qubit into superposition (using the Hadamard gate).

Let's create a circuit that does just that but initializes qubit $$q_1$$ to the $$\ket{1}$$ state.

![A CNOT with an H gate on the control](../images/multiverse-part-6/kickback-circuit.png){: height="200" }

Here's the code to generate the above circuit:

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import Aer
from qiskit.visualization import array_to_latex

q = QuantumRegister(2, 'q')
c = ClassicalRegister(2, 'c')
qc = QuantumCircuit(q, c)

qc.h(0)
qc.x(1)
qc.cx(0,1)
qc.h(0)
```

Starting out, our qubits are in the following states:

$$
q_0 = 1\ket{0} + 0\ket{1}
$$

$$
q_1 = 0\ket{0} + 1\ket{1}
$$

Let's use Qiskit's statevector output to see what $$q_0$$ and $$q_1$$ look like after applying the gates:

```python
from qiskit.quantum_info import Statevector

Statevector(qc).draw('latex')
```

When we examine our resulting state vector, we see:

$$
\frac{1}{2} |00\rangle- \frac{1}{2} |01\rangle+\frac{1}{2} |10\rangle+\frac{1}{2} |11\rangle
$$

This respresents the probabilities of the combined states of $$q_0$$ and $$q_1$$. We now have a 25% chance of getting a state where $$q_0$$ is $$\ket{0}$$ and $$q_1$$ is $$\ket{1}$$ but that the phase is negative. But what is this really telling us? Let's break it down.

If we expand out the state $$-\ket{01}$$, we essentially have:

$$
q_0 = 1\ket{0} + 0\ket{1}
$$

$$
q_1 = 0\ket{0} - 1\ket{1}
$$

Just by running the control qubit, $$q_0$$, through the H gate, we were able to modify the phase of the target qubit, $$q_1$$! Cool!

![Touched by a Qubit in Superposition, a new TV show](../images/multiverse-part-6/touched-by-a-qubit.jpg){: height="300" }

Note that above, the negative is only applied to the probability amplitude of getting a $$\ket{1}$$ (the $$b$$ term) and in $$q_0$$'s case, a negative zero wouldn't make sense.

## So what's actually going on in the land of matrices?

In this post we let qiskit/python do all the matrix math here, but it's interesting and useful to show how you would actually use matrix multiplication to represent what just happend.

Previously, in [part 4](/programming-the-multiverse-part-4/), we did some simple tensor products with a single gate matrix and a pair of qubits represented as a 4-row state vector. But now that we have multiple gates in a row, we have to do some more tricks.

In order to do the math for multiple gates in a row, we do a dot product to the series of gates on the state vector. Let's see how this works for two SWAP gates in a row:

![A diagram of two SWAP gates in series](../images/multiverse-part-6/swap-series-diagram.png)

When we have gates in series, we simply do a dot product of each gate's matrices and the initial state vector. For our example, let's say $$q_1 = \ket{1}$$.

$$
\left[ \begin{array}{cccc}
1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 0 & 1
\end{array} \right]
\cdot
\left[ \begin{array}{cccc}
1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 0 & 1
\end{array} \right]
\cdot
\left[ \begin{array}{c} 0 \\ 1 \\ 0 \\ 0 \end{array} \right]
$$

If we only did one swap, we'd end up with $$q_0 = \ket{1}$$ and $$q_1 = \ket{0}$$. But with two swaps, we end up with $$q_0 = \ket{0}$$ and $$q_1 = \ket{1}$$ again. Let's do the matrix multiplication on the first two matrices representing the gates:

$$
\left[ \begin{array}{cccc}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{array} \right]
\cdot
\left[ \begin{array}{c} 0 \\ 1 \\ 0 \\ 0 \end{array} \right]
$$

Notice that cool pattern of ones along the diagonal? That's called an **identity matrix**. It means the matrix does not change a vector you do a dot proct with. So our original state vector is unchanged after two swaps, as we'd expect.

### When gates are uneven

In our kickback example, we did different gate operations on each qubit. So how do we represent that with matrices? If you said, "let's use the same tensor product alchemy we used to create our 4-row state vector from two qubits," you'd be exactly right!

Looking at our kickback diagram, we have 3 columns of gates:

![Diagram showing each gate in series split into columns A, B, and C](../images/multiverse-part-6/kickback-circuit-columns.png)

The entire circuit (the "composite system") can be built from the following equation:

$$
\ket{\psi} = A \cdot B \cdot C \cdot (q_0 \otimes q_1)
$$

For $$A$$ we can do a tensor product of the H and X gates:

$$
A
=
\left[ \begin{array}{cc}
  \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}}
  \\
  \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{array} \right]
\otimes
\left[ \begin{array}{cc}
  0 & 1
  \\
  1 & 0
\end{array} \right]
=
\begin{bmatrix}
  0 & 0 & \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2}  \\
  0 & 0 & \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2}  \\
  \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2} & 0 & 0  \\
  \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2} & 0 & 0  \\
\end{bmatrix}
$$

For $$B$$ we just use the regular CNOT matrix since it spans all our qubits.

For $$C$$ we need to use some cleverness. Nothing happens to $$q_1$$ in column C, right? That means that we can think of it as if we applied one of those identity matrices that does nothing to the qubit! So just like we did a tensor product for A, we'll do it for C between the H and the identity.

$$
C
=
\left[ \begin{array}{cc}
  \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}}
  \\
  \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{array} \right]
\otimes
\left[ \begin{array}{cc}
  1 & 0
  \\
  0 & 1
\end{array} \right]
=
\begin{bmatrix}
\frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2} & 0 & 0  \\
 \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2} & 0 & 0  \\
 0 & 0 & \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2}  \\
 0 & 0 & \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2}  \\
 \end{bmatrix}
$$

Our full equation is:

$$
\ket{\psi}
=
\begin{bmatrix}
  0 & 0 & \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2}  \\
  0 & 0 & \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2}  \\
  \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2} & 0 & 0  \\
  \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2} & 0 & 0  \\
\end{bmatrix}
\cdot
\begin{bmatrix}
  1 & 0 & 0 & 0  \\
  0 & 0 & 0 & 1  \\
  0 & 0 & 1 & 0  \\
  0 & 1 & 0 & 0  \\
\end{bmatrix}
\cdot
\begin{bmatrix}
\frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2} & 0 & 0  \\
 \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2} & 0 & 0  \\
 0 & 0 & \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2}  \\
 0 & 0 & \frac{\sqrt{2}}{2} & - \frac{\sqrt{2}}{2}  \\
 \end{bmatrix}
\cdot
(q_0 \otimes q_1)
$$

All of this multiplies out to:

$$
\ket{\psi}
=
\begin{bmatrix}
\frac{1}{2} & - \frac{1}{2} & \frac{1}{2} & \frac{1}{2}  \\
 - \frac{1}{2} & \frac{1}{2} & \frac{1}{2} & \frac{1}{2}  \\
 \frac{1}{2} & \frac{1}{2} & \frac{1}{2} & - \frac{1}{2}  \\
 \frac{1}{2} & \frac{1}{2} & - \frac{1}{2} & \frac{1}{2}  \\
 \end{bmatrix}
\cdot
(q_0 \otimes q_1)
$$

So that is how we do all the matrix math on complex series of gates on many qubits.

One thing to note here is that as the number of qubits increases, the size of the matrices needed to represent the whole quantum circuit exponentially increases. It becomes apparent that to simulate a quantum computer with a classical computer doing matrix multiplication it requires a ton of computing resources to execute. In fact, simulating around 50 qubits becomes impossible with today's classical computers.
