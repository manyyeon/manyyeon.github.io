---
layout: post
title: "Classical Error Correction"
date: 2025-05-12 16:55:00 -0600
categories: quantum error-correction
---
In this diagram, each circle represents a parity-check bit $z_i$, and each intersection represents a message bit $a, b, c, d$. By construction, each parity-check bit only involves the three message bits contained in its corresponding circle.

If we now send the 7-bit message $y = \mathcal{E}(abcd) = abcdz_1z_2z_3$, we can show that any single-bit error will be correctable. Indeed, let's see what happens if an error occurs on bit $a$. In this case, we can read in the diagram above that both $z_1$ and $z_2$ partiy chekcs will be violated, while $z_3$ will remain equal to $b + c + d$. This can only happen if $a$ is flipped, which allows us to correct the error. A similar reasoning can be performed for the other bits. This code, called the $[7, 4]$ - **Hamming code**, is a $[7, 4, 3]$-code with a rate $R = 4/7 \approx 0.57$, which is already better than the rate $R \approx 0.33$ of the 3-repetition code, for the same distance.

The construction presented here can be generalized, leading to a whole family of Hamming codes defined for any $n$ of the form 