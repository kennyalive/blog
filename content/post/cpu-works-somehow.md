---
title: "The Cpu Works Somehow"
date: 2025-12-28T10:30:15+02:00
---

You can create a simple CPU by using a bunch of transistors to build logic gates (AND, OR, NAND, etc.), then connecting those gates so they run actual logic and computations. However, I could not understand how such simple primitives could perform fairly complicated operations in a very small number of machine cycles - sometimes even in a single cycle.

### Confusion

I will use addition as an example. In a simple approach, addition evaluates the carry for each bit, and each carry depends on the carry from the previous bit, forming a long chain of dependencies. The modern designs optimize this process (carry-lookahead algorithm) and can evaluate carry bits in parallel at the cost of more transistors. There is still non-trivial amount of logic along a single execution path.

My mental model was that a single machine cycle executes only very simple logic, like evaluating a single gate, and only the next cycle can use the output of that gate.

### Specific Example

Integer addition on modern CPUs usually takes one cycle for the ALU to produce a result.

In a simple case, when addition of two binary numbers does not produce carries, it can be implemented as a per-bit XOR operation. It can be done in a single cycle:

```
  1001b (=9)
+ 0010b (=2)
= 1011b (=11)
```

Now consider an example that generates carry bits:

```
  1001b (=9)
+ 0001b (=1)
= 1010b (=10) - correct result
= 1000b (=8) - wrong result if carry is not propagated
```

The per-bit XOR approach does not work here. When we add the least significant bits (1 + 1), we get 0 in the least significant bit position, and we need to propagate the carry to the next bit. CPUs typically have dedicated circuitry to compute the carry bits.

### How it actually works

This book by Charles Petzold helped me clarify how all of this works. It is very accessible for people who know little about electronics. It introduces the basics and ends with building an actual CPU from the 1970s. 

![cpu](/cpu-works-somehow/code-book.jpg#center)

I eventually realized that my mental model was wrong. CPU computation is not a sequence of tiny discrete steps but rather signal propagation through circuitry. A single machine cycle defines a time window during which signals can propagate through multiple logic gates.

This means that fairly complex logic can be implemented within a single cycle (or a few cycles) by combining parallel and dependent parts of the computation. You can think of this as a hierarchy: within each level the gates operate in parallel, and for each sequential dependency a new level is introduced.

For example, if signals can propagate through eight gates within a single cycle, then a single-cycle operation can contain up to eight dependent levels of this hierarchy. At the same time, each level can contain many gates working in parallel - for example, one gate per bit of a 64-bit number.

Let's get back to carry bit computation:

* Bit 0 carry logic does not depend on any previous bits
* Bit 1 carry logic connects to bit 0 related circuitry
* Bit 2 carry logic connects to bit 1 and bit 0 related circuitry
* ...
* Bit 7 carry logic connects to all previous bits (0 through 6)

You can imagine the map of connections as a triangle. The carry logic for bit `i` has connections to all previous bits. In practice, these are not necessarily direct connections. The intuition is simply that the wiring for bit 7 is more complex than for bit 0.

```
bits: 7 6 5 4 3 2 1 0
0     * * * * * * *
1     * * * * * *
2     * * * * *
3     * * * *
4     * * *
5     * *
6     *
```

Here's a photo of carry bit computation in the adder of 8008 CPU. It also looks like a basic graphics primitive.

![cpu](/cpu-works-somehow/8008-carry-look-ahead.jpg)

*Intel 8008 die photo by Ken Shirriff, via happytrees.org. Used under CC BY 4.0. Cropped from the original.*
(https://happytrees.org/dieshots/Timeline_on_Intel_chips#/media/File:Intel-8008-die-adjusted.jpg)

This resolves the confusion I had. The logic that the ALU executes during a single cycle is not necessarily as simple as OR or AND. As in the addition example, it can be a complex formula that computes carry input for bit 7 based on all the previous bits.

I would not be surprised if, in principle, it were possible to simulate one frame of a Mario game in a single cycle - assuming a low enough clock frequency!
