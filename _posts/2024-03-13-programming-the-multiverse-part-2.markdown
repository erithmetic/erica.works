---
layout: post
title: "Programming the Multiverse: Part 2 - Some Basic Gates"
date: 2024-03-13 21:12:17 -0500
categories: quantum_computing
---

We're going to start with some basics. I mean, suuuuper basic. But it's an important first step. Many quantum computers today act as FPGAs - like hardware that you can program. The most popular way to write quantum algorithms these days is to use a library like Qiskit to define a quantum circuit. This circuit can then be "transpiled" and run on a real quantum computer hosted by IBM or Google. You can also just run your circuit in a simulator that runs on your local machine. There are some programming languages emerging for defining quantum algorithms but they're pretty esoteric.

## Ye olde logick gates

Transistors are so 1940s (but my trans sisters are all super amazing! aaayyy!!) You may remember these logical gate diagrams, like for the NOT gate:

![A logical NOT gate symbol](../images/multiverse-part-2/not-gate.png)

This logical gate transforms a single bit, 0 into a 1 or a 1 into a 0. It's one of the basic building blocks of every CPU and computing device.

![A circuit diagram for a NOT gate](../images/multiverse-part-2/transistor-not.jpg)

Above is a circuit diagram for a NOT gate (one of many implementations). If you remember your computer engineering, a computer system represents the value "0" as a lower voltage amount coming down a circuit wire. A "1" is a higher voltage. The NOT gate takes a low voltage as input and in turn outputs a higher voltage. The opposite happens if a high voltage is input.

## The fancy new quantum gates

Here we go, your first quantum circuit!

![A circuit diagram for a quantum SWAP gate](../images/multiverse-part-2/transistor-not.jpg)
