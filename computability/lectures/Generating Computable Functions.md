# Basic Functions
First we note that some particularly simple functions are computable and from these, we can build more complicated computable functions using specific techniques.

> [!NOTE] Basic Computable Functions
> The following basic functions are computable:
> 	a) The Zero Function: $z : \mathbb{N}^n \rightarrow \mathbb{N}$ $z(\overset{\rightarrow}{x}) = 0 \ \forall \overset{\rightarrow}{x} = (x_1, ..., x_n) \in N^n$;
> 	b) The Successor Function: $s: \mathbb{N} \rightarrow \mathbb{N} \ s(x) = x + 1 \ \forall x \in \mathbb{N}$;
> 	c) The Projection Function: $U^n_j : \mathbb{N}^n \rightarrow \mathbb{N} \ U^n_j(\overset{\rightarrow}{x}) = x_j \ \forall \overset{\rightarrow}{x} \in \mathbb{N}^n$
> These functions correspond to the arithmetic instruction for the URM.
# Joining Programs Together

> [!SUCCESS] Definition of *Standard Form* of a Program
> A program $P = I_1, I_2, ..., I_s$ is in *standard form* if, for every jump instruction $J(m, n, q)$ in $P$ we have $q \leq s + 1$.

Let's now assume that the programs $P$ and $Q$ are in standard form. The problem when joining $P$ and $Q$ concerns the jump instruction in $Q$: a jump $J(m, n, q)$ occurring in $Q$ commands a jump to the $q$-th instruction of $Q$, if $r_m = r_n$. But the $q$-th instruction of $Q$ will become the $(s + q)$-th instruction in the composite program; each jump $J(m, n, q)$ in $Q$ must be modified to become $J(m, n, s + q)$ in the composite program if the sense is to be preserved.

> [!SUCCESS] Definition of *Concatenation* of Programs
> Let $P$ and $Q$ be programs of length $s$, $t$ respectively, in standard form. 
> The *concatenation* of $P$ and $Q$, written $PQ$, is the program $I_1, I_2, ..., I_s, I_{s+1}, ..., I_{s+t}$ where $P = I_1, ..., I_s$ and the instructions $I_{s+1}, ..., I_{s+t}$ are the instructions of $Q$ with each jump $J(m, n, q)$ replaced by $\\ J(m, n, s + q)$

With this definition, it is clear that the effect of $PQ$ is as desired: any computation under $PQ$ is identical to the corresponding computation under $P$ followed by the computation under $Q$ whose initial configuration is the final configuration from the computation under $P$.

Let's suppose now that we want to compose a program $Q$ having a given program $P$ as a subroutine. To write $Q$ it's important to find some registers that are unaffected by computations under $P$.
- Since $P$ is finite, there is a smallest number $u$ such that none of the registers $R_v$ for $v > u$ is mentioned in P (just to clarify, if $Z(n)$, $S(n)$, $T(m, n)$ or $J(m, n, q)$ are instructions in $P$, then $m,\ n \leq u$).
- During the computation under $P$, the contents of $R_v$ for $v > u$ remains unaltered and has no effect on the values of $r_1, ..., r_u$. Then, when writing our program $Q$, the registers $R_v$ can be used to store information without affecting any computation under the subroutine $P$ and we denote $u = \rho(P)$
- Suppose that $P$ is a program in standard form design to compute a function $f(x_1, ..., x_n)$. When using $P$ as a subroutine in a larger program the inputs $x_1, ..., x_n$ for which $f(x_1, ..., x_n)$ is desired may be held in registers $R_{l_1}, ..., R_{l_n}$rather than $R_1, ..., R_n$ as the program $P$ requires; in addition to this, the output $f(x_1, ..., x_n)$ may be required fro future purposes to be in some register $R_l$ rather than the conventional $R_1$;
- Finally, the working registers $R_1, ..., R_{\rho(P)}$ for $P$ may contain unwanted information. 

We can modify $P$ to take account of all of these points:
- We write $P[l_1, ..., l_n \rightarrow l]$ and this program has the effect of computing $f(r_{l_1}, ..., r_{l_n})$ and placing the result in $R_l$;
- The only registers affected by this program are, at most, $R_1, R_2, ..., R_{\rho(P)}$ and $R_l$
# Substitution
A common methodology to create new function from old ones is to substitute functions into other function, known also as **Composition of Functions**. 

> [!SUCCESS] Composition of Functions
Suppose that we are given $f: \mathbb{N}^k \rightarrow \mathbb{N}$ and $g_1, ..., g_k: \mathbb{N}^n \rightarrow \mathbb{N}$ computable functions.
We define $h: \mathbb{N}^n \rightarrow \mathbb{N}$ such that: $$h(\overset{\rightarrow}{x}) = f(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$$where $\overset{\rightarrow}{x} = (x_1, ..., x_k)$.
We can say that $h(\overset{\rightarrow}{x})$ is defined if and only if $g_1(\overset{\rightarrow}{x}), .., g_k(\overset{\rightarrow}{x})$ are defined and $g_1(\overset{\rightarrow}{x}), .., g_k(\overset{\rightarrow}{x}) \in Dom(f)$

> [!INFO] Proof
> Suppose that $F, G_1, ..., G_k$ are programs in standard form which respectively compute $f, g_1, ..., g_k$. We will write a program $H$ that embodies the following procedure for computing $h$:
> "Given $\overset{\rightarrow}{x}$, use the programs $G_1, ..., G_k$ to compute in succession $g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x})$, making a note of these values as they are obtained. Then, use the program $F$ to compute $f(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$"
> 
> Since we must take care to avoid losing information needed at later stages in the procedure, we put $m = max(n, k, \rho(P), \rho(G_1), ..., \rho(G_k))$ and we start storing $\overset{\rightarrow}{x}$ in $R_{m +1}, ..., R_{m + n}$ and the registers $R_{m + n + 1}, ..., R_{m + n + k}$ will be used to store the values of $g_i(\overset{\rightarrow}{x})$ as they are computed for $i = 1, 2, ..., k$. These registers are completely ignored by computations under $F, G_1, ..., G_k$. We can represent it in this way:
> ![[ComputableFunctions_CompositionRegisters.png]]
> And write it this way:
> $$\begin{aligned}&T(1, m + 1) \\ &. \\ &. \\ &. \\ &T(n, m + n) \\ &G_1[m + 1, m + 2, ..., m + n \rightarrow m + n + 1] \\ &. \\ &. \\ &. \\ &G_k[m + 1, m + 2, ..., m + n \rightarrow m + n + k] \\ &F[m + n + 1, ..., m + n + k \rightarrow 1]\end{aligned}$$A computation $H(\overset{\rightarrow}{x})$ will stop if and only if each computation $G_i(\overset{\rightarrow}{x})$ stops $\forall i \ s.t\ 1 \leq i \leq k$ and the computation $F(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$ stops

# Primitive Recursion
**Recursion** is a method of defining functions by specifying each of its values in terms of previously defined values, and possible using other already defined functions.
Given the following functions $f: \mathbb{N}^n \rightarrow \mathbb{N}$ and $g: \mathbb{N}^{n + 2} \rightarrow \mathbb{N}$: $$f(\overset{\rightarrow}{x}) \\ \\g(\overset{\rightarrow}{x}, y, z)$$We can define a new function $h: \mathbb{N}^{n + 1 } \rightarrow \mathbb{N}$ as follows: $$\begin{cases}h(\overset{\rightarrow}{x}, 0) = f(\overset{\rightarrow}{x}) \\ \\ h(\overset{\rightarrow}{x}, y + 1) = g(\overset{\rightarrow}{x}, y, h(\overset{\rightarrow}{x}, y))\end{cases}$$
>[!INFO] Recursion
>Suppose that $f(\overset{\rightarrow}{x})$ and $g(\overset{\rightarrow}{x}, y, z)$ are computable functions, where $\\ \overset{\rightarrow}{x} = (x_1, ..., x_n)$; then the function $h(\overset{\rightarrow}{x}, y)$ obtained from $f$ and $g$ by recursion is computable.

>[!SUCCESS] Proof
>Let $F$ and $G$ be programs in standard form which compute the functions $f(\overset{\rightarrow}{x})$ and $g(\overset{\rightarrow}{x}, y, z)$. We will devise a program $H$ for the function $h(\overset{\rightarrow}{x}, y)$  given by recursion.
>Given an initial configuration $x_1, ..., x_n, 0, ...$, $H$ will first compute $h(\overset{\rightarrow}{x}, 0)$ (so using $F$) and then, if $y \neq 0$, $H$ will use $G$ to compute successively $h(\overset{\rightarrow}{x}, 1)$, $h(\overset{\rightarrow}{x}, 2)$, ..., $h(\overset{\rightarrow}{x}, y)$ and then stop.
>Let $m = max(n + 2), \rho(F), \rho(G)$; we begin by storing $\overset{\rightarrow}{x}$, $y$ in $R_{m + 1}, ..., R_{m + n + 1}$; the next 2 registers will be used to store the current value of the number $k$ and $h(\overset{\rightarrow}{x}, k)$ for $k = 1, 2, ..., y$. Writing $t = m + n$, a typical configuration is the following
>
>![[ComputableFunctions_RecursionRegisters.png]]
>
>And we can write it in this way: $$\begin{aligned}&T(1, m + 1) \\ &. \\ &. \\ &. \\ &T(n + 1, m + n + 1) \\ &F[1, 2, ..., n \rightarrow t + 3] \\ I_q\ &J(t+2, t + 1, p) \\ &G[m + 1, ..., m + n, t + 2, t + 3 \rightarrow t + 3] \\ &S(t + 2) \\ &J(1, 1, q) \\ I_p \ &T(t + 3, 1)\end{aligned}$$
>
>Hence, $h$ is computable.
