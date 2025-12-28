---
title: "The Cpu Works Somehow"
date: 2025-12-28T10:30:15+02:00
---

You can create a simple CPU by using a bunch of transistors to build logic gates (such as AND, OR, NAND, NOR, and NOT), then connecting those gates so they run actual logic and computations. However, I could not understand how these primitives can perform an addition operation with a latency of a single cycle.

On a fundamental level, this was always a splinter in my mind: you know how CPUs operate at relatively low level - ISA, registers, caches, microarchitecture, but you still can't connect all of that to a simple addition. Even at a deeper level you know you couldn't build an electromechanical computer in a post-fab era if needed.

### Confusion

Addition evaluates the carry for each bit, and each carry depends on the carry from the previous bit, forming a long chain of dependencies. I learnt that modern designs compute the carry for bit `i` as a function of all previous bits, which removes serial dependencies but this also means a non-trivial amount of computation (especially for a single cycle). My mental model was that a single cycle is mostly limited to very simple operations (or a group of such operations that can run in parallel). For example, you can run OR in parallel for all bits of two numbers, but more complex logic would require additional cycles.

### Specific Example

Integer addition on modern CPUs usually takes one cycle for the ALU to produce a result.

The bits of two numbers can be added in parallel, you just run OR logic per bit. Can be
done in one cycle:

```
  1001b (=9)
+ 0010b (=2)
= 1011b (=11)
```

In the above example, we did not have to think about carry. Now consider the following:

```
  1001b (=9)
+ 0001b (=1)
= 1010b (=10) - correct result
= 1000b (=8) - wrong result if carry is not propagated
```

Here, per-bit addition does not work. When we add the least significant bits (1 + 1), we get 0 in the least significant bit position, and the carry is propagated to the next bit. Fundamentally, addition is a sequence of **dependent** steps.

My best subconscious explanation was that CPU designers built something advanced and involved, something like Japanese students doing computation with their fingers, just translated into transistors. To be honest, I never thought deeply about this. I assumed there was some approach that made it work, but in the end it all relied on the CPU being able to run only basic logic-gate operations within a single cycle (definitely not algorithms with many steps) - a discrete machine at its core. I never questioned this assumption.

### How it actually works

This book by Charles Petzold helped me clarify how it all works. It is very good for people who know almost nothing about electronics. It introduces the basics and ends with building an actual CPU from the 1970s. 

![cpu](/cpu-works-somehow/code-book.jpg#center)

I realized that my mental model was wrong and that CPU computations have an analog nature. Current propagates through the circuitry and creates voltage patterns on the output connections - that is how the electrons compute. The complexity of computation is limited by the complexity of the circuitry and the enabled connections (via transistors), but it can compute quite a lot, not just a single or a few gates. A single cycle needs to be long enough for the electrons to propagate and to establish output voltages.

Let's get back to carry bit computation:

* Bit 0 carry logic does not depend on any previous bits, there are no connections to the previous outputs
* Bit 1 carry logic connects to the bit 0 circuitry
* Bit 2 carry logic connects to bit 1 and bit 0 circuitry
* ...

You can imagine the map of connections as a triangle. The carry logic for bit `i` has `i-1` connection to the previous bits.

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

This resolves the confusion I had. The logic that the ALU executes during a single cycle is not necessarily as simple as OR or AND. It can be a complex formula that computes carry input for bit 7 based on all the previous bits.

I would not be surprised if it were possible to simulate one frame of a Mario game in a single cycle, assuming the CPU runs at a low enough frequency and we have large enough transistor budget!

This does not discuss pipelined execution. Modern CPUs can split an instruction into multiple stages, but this does not help with the carry bit computation logic, which is a fine-grained dependency between individual bits.
