---
layout: post
title: "Classical Error Correction"
date: 2025-05-12 16:55:00 -0600
categories: quantum error-correction
---
In this diagram, each circle represents a parity-check bit $z_i$, and each intersection represents a message bit $a, b, c, d$. By construction, each parity-check bit only involves the three message bits contained in its corresponding circle.

If we now send the 7-bit message $y = \mathcal{E}(abcd) = abcdz_1z_2z_3$, we can show that any single-bit error will be correctable. Indeed, let's see what happens if an error occurs on bit $a$. In this case, we can read in the diagram above that both $z_1$ and $z_2$ partiy chekcs will be violated, while $z_3$ will remain equal to $b + c + d$. This can only happen if $a$ is flipped, which allows us to correct the error. A similar reasoning can be performed for the other bits. This code, called the $[7, 4]$ - **Hamming code**, is a $[7, 4, 3]$-code with a rate $R = 4/7 \approx 0.57$, which is already better than the rate $R \approx 0.33$ of the 3-repetition code, for the same distance.

The construction presented here can be generalized, leading to a whole family of Hamming codes defined for any $n$ of the form $n = 2^r - 1$. We won't go into the details of this construction here, butr if you're interested, I encourage you to watch this 3Blue1Brown video on the topic. The main takeaway from the general Hamming code construction is that we only need a logarithmic number of parity checks to correct all single-bit errors. More precisely, Hamming codes are $[2^r - 1, w^r - r - 1, 3]$-codes, with a rate $R = \frac{2^r-r-1}{2^r-1}$ that converges to 1 when $ r \rightarrow \infty$.

However, Hamming codes have a low distance that doesn't increase with the codeword length, and they would therefore be impractical in very noisy systems. So we need a more general framework that would allow us to find new codes with better characteristics. That framework is the one of linear codes.

## Linear codes
This idea of transmitting both the message and some parity-check bits can be generalized with the notion of linear code. A linear code consists in using a matrix $\bm{G}$--called **generator matrix**-- as our code, i.e.

$$
\begin{aligned} \bm{y} = \bm{G} \bm{x}. \end{aligned}
$$

If our message $x$ has length $k$ and is complemented by $m$ partiy checks, such that $n = k + m$ is the size of $y$, we can write $G$ as

$$
\begin{aligned} \bm{G}=\left(\begin{matrix} I_k \\ \hline \bm{A} \end{matrix} \right) \end{aligned}
$$

with $I_k$ the $k \times k$ identity matrix (used to reproduce the message in the code) and $\bm{A}$ an $m \times k$ matrix that performs the partiy checks. In this notation, all the matrix operations are performed modulo 2. For instance, the generator matrix of the Hamming code can be written

$$
\begin{aligned} \bm{G} = \left(\begin{matrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ \hline 1 & 1 & 0 & 1 \\ 1 & 0 & 1 & 1 \\ 0 & 1 & 1 & 1 \end{matrix} \right) \end{aligned}
$$

since

$$
\begin{aligned} \bm{G}\bm{x} = \left(\begin{matrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ \hline 1 & 1 & 0 & 1 \\ 1 & 0 & 1 & 1 \\ 0 & 1 & 1 & 1 \end{matrix} \right) \left(\begin{matrix} a \\ b \\ c \\ d\end{matrix}\right) \end{aligned} = \left(\begin{matrix} a \\ b \\ c \\ d \\ a + b + d \\ a + c + d \\ b + c + d\end{matrix}\right) = \left(\begin{matrix}a \\ b \\ c \\ d \\ z_1 \\ z_2 \\ z_3\end{matrix}\right)
$$

Two important remarks about generator matrices:

1. Elementary operations on the rows and columns of $\bm{G}$ don't change the code. Indeed, the code is defined as the image of $\bm{G}$, which is invariant under similarity transformations. Using Gaussian reduction, it is therefore always possible to transform $\bm{G} to have the form

$$
\begin{aligned} \bm{G}=\left(\begin{matrix} I_k \\ \hline \bm{A} \end{matrix} \right) \end{aligned}
$$

In other words, any linear code can be seen as a message supplemented with partiy checks!

2. The codeswords of a code descibed by $\bm{G}$ can be found by taking all the linear combinations of the columns of $\bm{G}$ (the vector $\bm{x}$ in the definition indicates which columns you select or not). Therefore, to find all the codewords, just calculate all the $\bm{y}$ of the form $\bm{y} = a_1\bm{c_1} + ...a_k\bm{c_k}$ where $\bm{c_i}$ is the $i^{th}$ column of $\bm{G}$ and $a_1, ...a_k \in \{0,1\}$.

An equivalent picture to describe linear codes is through the **partiy-check matrix**, defined as an $m \times n$ matrix $\bm{H}$ such that

$$
\bm{H}\bm{y} = 0
$$

if and only if $\bm{y}$ is a codeword. In other words, the space of codewords can be defined as the kernel of $\bm{H}$. The intuition is that linear codes can always be defined as a set of codewords obeying a certain system of linear equations, defined by the parity checks. For instance, the codewords of Hamming code obey the following system:

$$
\begin{aligned} a + b + d + z_1 = 0 \\ a + c + d + z_2 = 0 \\ b + c + d + z_3 = 0 \end{aligned}
$$

(since $a + b + d = z_1 \Leftrightarrow a + b + d + z_1 = 0$ when working modulo 2)

Therefore, the partiy-check matrix of the Hamming code can be written

$$
\bm{H} = \begin{aligned} \left(\begin{array}{cccc|ccc} 1 & 1 & 0 & 1 & 1 & 0 & 0 \\ 1 & 0 & 1 & 1 & 0 & 1 & 0 \\ 0 & 1 & 1 & 1 & 0 & 0 & 1 \\ \end{array}\right) \end{aligned}
$$

## Decoding linear codes
Designing a code with good characteristics, such as a high rate and a high distance, is not enough to make it practical: you need to show how to decode it efficiently. As discussed before, decoding consists in finding the original message given its noisy encoded version. We then define an **efficient decoder** as an algorithm able to accomplish this task  in polynomial time, i.e. with a time complexity that grows polynomially with the size $n$ of the code.

To see why decoding can be a difficult problem, let's consider the general task of decoding a linear code, when errors follow a certain distribution $P(\bm{e})$. As we've seen in the previous section, decoding a linear code can be reduced to finding the most likely error given a received syndrome. Let $\bm{H}$ denote the parity-check matrix of our code and $\bm{s} = \bm{H}\bm{e}$ the received syndrome. The goal of an ideal decoder would be to find the vector $\bm{e}$ that maximizes the probability $P(\bm{e} \vert \bm{s})$. Using Bayes rule, we can write this probability as:

$$
\begin{aligned} P(\bm{e} \vert \bm{s}) = \frac{P(\bm{s} \vert \bm{e})P(\bm{e})}{P(\bm{s})} \end{aligned}
$$

Since P(\bm{s}) doesn't depend explicitely on $\bm{e}$, we can ignore it when solving the maximization problem over $\bm{e}$. Next, we notice that any predicted error will have to obey $\bm{H}\bm{e} = \bm{s}$, so $P(\bm{s} \vert \bm{e})$ is either 1 or 0, depending on whether this equation is satisfied or not. In other words,

$$
\begin{aligned} P(\bm{s} \vert \bm{e}) = 1_{\bm{H}\bm{e}=\bm{s}} \end{aligned}
$$

Therefore, we can rewrite our optimization problem as:

$$
\begin{aligned} \max_{\bm{e}\in \{0.1\}^n} P(\bm{e}) \; \text{ s.t. } \; \bm{H}\bm{e}=\bm{s} \end{aligned}
$$

An important special case is when errors are independent and identically distributed, such that

$$
\begin{aligned} P(\bm{e}) = \prod_{i=1}^n P(e_i) \end{aligned}
$$

Writing $P(e_i = 1) = p, P(e_i = 0) = (1 - p)$, and denoting $\vert \bm{e} \vert$ the Hamming weight of $\bm{e}$ (i.e. the number of 1 in $\bm{e}$), we can rewrite the equation above as

$$
\begin{aligned} P(\bm{e}) = p^{\vert \bm{e} \vert} (1-p)^{n-\vert \bm{e} \vert} \end{aligned}
$$

This expression only depends on the weight of $\bm{e}$, and if the probability of error $p < 0.5$, it increases when lowering the weight. In other words, our optimization problem reduces to finding the error of minimum weight that satisfies $\bm{H}\bm{e} = \bm{s}$:

$$
\begin{aligned} \min_{\bm{e} \in \{0,1\}^n} \vert \bm{e} \vert \; \text{ s.t. } \; \bm{H}\bm{e} = \bm{s} \end{aligned}
$$

Any decoder that explicitely solves this optimization problem is a **Maximum a Posteriori (MAP) decoder**, as we are maximizing the posterior distribution $P(\bm{e}|\bm{s})$, and is considered to be an ideal decoder.

So how do we solve the MAP decoding problem? A naive idea would be to simply search through all error vectors and find one that has minimum weight and obeys the constraint. Since there are $2^n$ possible error vectors, the time complexity of this algorithm would scale exponentially with $n$ and definitely not be efficient. So can we do better? Unfortunately, the answer is no in general: this constrained optimization problem can be shown to be NP-complete, meaning that, most likely, no polynomial-time algorithm will ever solve it.

What we do from there really depends on the code that is being decoded. Some partiy-check matrices have a particular structure that allows the construction of polynomial-time algorithms that solve the MAP decoding problem. It's for instance the case with Hamming codes and repetition codes. More generally, certain heuristics can be used as approximations to MAP decoding, and lead to high performance in practice. The main example of high-performance heuristic is the **belief propagation algorithm**, a linear-time iterative algorithm that exploits the fact that the probability $P(\bm{e} \vert \bm{s})$ can often be factorized over a graph (in our case the Tanner graph). This algorithm is used extensively by the classical error-correction community, and has recently started to become popular in the quantum community as well, so I will try to dedicate a blog post to it.

Before ending this post, there is one last important notion I want you to know, as it is frequently used in quantum error correction: code duality.