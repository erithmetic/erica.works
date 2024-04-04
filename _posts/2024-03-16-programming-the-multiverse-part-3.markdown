---
layout: post
title: "Programming the Multiverse: Part 3 - Superposition and Probability"
date: 2024-03-16 21:12:17 -0500
categories: quantum_computing
---

In part 3 we'll introduce some of the math behind qubits.

_Note: this post is part of my series, [Programming the Multiverse](/programming-the-multiverse-part-1/)_

## More on the H gate

Last time, we introduced some basic gates, including the H gate. We saw that the H gate puts a qubit into a **Superposition** where it has a 50% chance of being a $$\ket{0}$$ or a 50% chance of being a $$\ket{1}$$ when measured.

What happens if we combine two H gates?

![Two H gates in series](../images/multiverse-part-3/double-h.png){: height="200" }

If we input a qubit set to $$\ket{0}$$ through the two H gates, we get&hellip; a $$\ket{0}$$! Likewise, if we input a qubit set to $$\ket{1}$$ we get a $$\ket{1}$$.

What's going on? Here's where we have to finally delve into a little bit of math.

## It's all probability

The most important thing to remember is that qbits are never really representing actual data values, but they represent _probabilities_ of seeing a 0 or a 1. And the gates are just manipulating those probabilities.

When we say a qubit is $$\ket{0}$$ or $$\ket{1}$$ what we're saying is there's a 100% probability of seeing a 0 or a 1.

Every qubit can be described by a formula:

$$\ket{\psi} = a\ket{0} + b\ket{1}$$

If you recall, we pronounce it "ket-zero" and "ket-one." We're using "bra-ket" notation here. No, this does not have anything to do with anesthesia and intimate apparel, it's just signifying that we're talking about vectors (more on that later).

The variables _a_ and _b_ are called the **Probability Amplitude**. The way this formula works is that _a_ and _b_ have to add up, such that $$\vert{a}\vert^{2} + \vert{b}\vert^{2} = 1$$. So in this formula, the total probability is 1 (100%) and the squares of the probabilities, _a_ and _b_ add up to 1.

In the case of a qubit that is definitely 0, we have $$1\ket{0} + 0\ket{1}$$. We can drop the $$\ket{1}$$ since it's multiplied by 0. Similarly, a definite 1 is $$0\ket{0} + 1\ket{1}$$ which simplifies to $$\ket{1}$$.

For a qubit that is in superposition, we have a 50% chance of a 0 or a 1. So if we solve backward from:

$$\vert{a}\vert^{2} + \vert{b}\vert^{2} = 1$$

and get:

$$\vert{\sqrt{\frac{1}{2}}}\vert^{2} + \vert{\sqrt{\frac{1}{2}}}\vert^{2} = 1$$

So that the squares cancel out the square roots:

$$\frac{1}{2} + \frac{1}{2} = 1$$

So then _a_ and _b_ are both equal to $$\sqrt{\frac{1}{2}}$$ which simplifies to $$\frac{1}{\sqrt{2}}$$.

Our final state vector is: $$\ket{\psi} = \frac{1}{\sqrt{2}}\ket{0} + \frac{1}{\sqrt{2}}\ket{1}$$

If we had a 25% chance of 0 and a 75% chance of 1, it would be $$\ket{\psi} = \frac{1}{\sqrt{4}}\ket{0} + \frac{\sqrt{3}}{\sqrt{4}}\ket{1}$$

## Back to the H gate

So the H gate is doing something to manipulate those probability amplitudes, _a_ and _b_. But How? And how does it move each one from 0% or 100% to 50%? Stay tuned for Part 4.

Next article: [Part 4 - The Guts of Gates](/programming-the-multiverse-part-4/)
