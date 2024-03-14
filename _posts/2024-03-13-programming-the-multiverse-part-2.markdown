---
layout: post
title: "Programming the Multiverse: Part 2 - Some Basic Gates"
date: 2024-03-13 21:12:17 -0500
categories: quantum_computing
---

We're going to start with some basics. I mean, suuuuper basic. But it's an important first step. Many quantum computers today act as FPGAs - like hardware that you can program. The most popular way to write quantum algorithms these days is to use a library like Qiskit to define a quantum circuit. This circuit can then be "transpiled" and run on a real quantum computer hosted by IBM or Google. You can also just run your circuit in a simulator that runs on your local machine. There are some programming languages emerging for defining quantum algorithms but they're pretty esoteric.

## Ye olde logick gates

You may remember these logical gate diagrams, like for the NOT gate:

![A logical NOT gate symbol](../images/multiverse-part-2/not-gate.png)

This logical gate transforms a single bit, 0 into a 1 or a 1 into a 0. It's one of the basic building blocks of every CPU and computing device. Bits are combined to represent bytes of data and the logical gates "move the bits around" to transform the data.

In classical computers, in a sense the _data_ is moving around through the gates. You can think of each gate as a function that takes data in, may modify it, then output a data result. For example, a NOT gate can be though of as a function `not(x)` where `not(0) == 1` and `not(1) == 0`. The functions combine into higher order functions that may represent mathematical operations or hardware commands.

<!-- ![A circuit diagram for a NOT gate](../images/multiverse-part-2/transistor-not.jpg)

Above is a circuit diagram for a NOT gate (one of many implementations). If you remember your computer engineering, a computer system represents the value "0" as a lower voltage amount coming down a circuit wire. A "1" is a higher voltage. The NOT gate takes a low voltage as input and in turn outputs a higher voltage. The opposite happens if a high voltage is input. -->

![Logical gates combined into an adder](../images/multiverse-part-2/logic-gates-vlsi.png)

Utlimately these logic gates form the thousands of building blocks that combine into our modern day, general purpose "integrated circuits" (e.g. CPUs). Our modern programming languages define a series of signals that get sent through this maze of gates. But it's important to note that every integrated circuit can implement the same logical gates using different types of electrical circuits.

## The fancy new quantum gates

You've seen how logic gates work in classical computers operating on bits of information represented by electrical pulses. But we need to completely forget about all of that. We're in quantum world now! Starting out, QC and classical will seem similar, but we'll see that QC is a complete shift in how we think of computing.

Now let's dive into some of the foundational quantum gates. For now we're just going to focus on their behavior but just know that the way these logical gates are implemented in actual quantum coputers vary.

### Quantum circuit diagrams

![A basic quantum circuit diagram](../images/multiverse-part-2/basic-circuit-diagram.png){: height="200" }

Here is the most basic quantum circuit diagram in the world. It does absolutely nothing, except measure the values to two qubits. Let's break it down:

`q0` and `q1` are two qubits. By convention, they are intialized to $$\ket{0}$$.

Whoah, wait a minute! What's this $$\ket{0}$$ thing? Well for now you can think of it just like a regular binary 0 from the classical world. $$\ket{1}$$ represents a 1.

Back to the diagram, the `c` represents our "classical register." You can think of this like the wire where our classical computer will read from to get results from our computation. There's only one way to get data from a qubit - that is to "measure it." That is what those meter icons represent. You measure a qubit and the result gets sent to the classical register. This touches on a core feature of quantum computing - the idea of superposition, measurement, and killing Schrödinger's cat - but we'll get to that in the future.

For now, you can think of us measuring qubit `q0` and outputing the result to classical register `c0`, as well as measuring qubit `q1` and putting the result in register `c1`. Our old classical computers can then read from the register to get our results.

So from the above diagram we'd read the values of `c0` and `c1` and get `00` because `q0` and `q1` are both initialized to $$\ket{0}$$.

### SWAP Gate

![Alt text](../images/multiverse-part-2/quantum-swap-gate.jpg){: height="200" }

A swap gate is very simple. It simply swaps the inputs. If `q0` is $$\ket{1}$$ and `q1` is $$\ket{0}$$ then after the gate `q0` becomes $$\ket{0}$$ and `q1` becomes $$\ket{1}$$. Our registers would read `01` after the measurements.
