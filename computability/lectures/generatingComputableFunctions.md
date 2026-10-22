# Basic Functions
First we note that some particularly simple functions are computable and from these, we can build more complicated computable functions using specific techniques.

> [!NOTE] Basic Computable Functions
> The following basic functions are computable:
> 	a) The Zero Function: $z : \mathbb{N}^n \rightarrow \mathbb{N}$ $z(\overset{\rightarrow}{x}) = 0 \ \forall \overset{\rightarrow}{x} = (x_1, ..., x_n) \in N^n$;
> 	b) The Successor Function: $s: \mathbb{N} \rightarrow \mathbb{N} \ s(x) = x + 1 \ \forall x \in \mathbb{N}$;
> 	c) The Projection Function: $U^n_j : \mathbb{N}^n \rightarrow \mathbb{N} \ U^n_j(\overset{\rightarrow}{x}) = x_j \ \forall \overset{\rightarrow}{x} \in \mathbb{N}^n$
> These functions correspond to the arithmetic instruction for the URM.

From these functions, we can then build more complicated computable functions.
# Joining Programs Together
An example of program build occurs when we have program $P$ and program $Q$ and we want to write a program for the composite procedure: first we execute $P$, then we execute $Q$. To do something like this, we first need to account two technical points.
Suppose that $P = I_1, I_2, ..., I_s$. A computation under $P$ is completed when the *next instruction of the computation* is $I_v$, for some $v > s$; we then require the computation under our composite program to proceed to the first instruction of the program $Q$. This will occur if $v = s + 1$, but not in any other cases. Thus for building composite programs we must confine our attention to programs that invariably stops because the next instruction is $I_{s+1}$. It is only jump instruction that can cause a program to stop in a non-standard form.

> [!SUCCESS] Definition of *Standard Form* of a Program
> A program $P = I_1, I_2, ..., I_s$ is in *standard form* if, for every jump instruction $J(m, n, q)$ in $P$ we have $q \leq s + 1$.

Let's now assume that the programs $P$ and $Q$ are in standard form. The second problem when joining $P$ and $Q$ concerns the jump instruction in $Q$: a jump $J(m, n, q)$ occurring in $Q$ commands a jump to the $q^{th}$ instruction of $Q$, if $r_m = r_n$. But the $q^{th}$ instruction of $Q$ will become the $(s + q)^{th}$ instruction in the composite program; each jump $J(m, n, q)$ in $Q$ must be modified to become $J(m, n, s + q)$ in the composite program if the sense is to be preserved.

> [!SUCCESS] Definition of *Concatenation* of Programs
> Let $P$ and $Q$ be programs of length $s$, $t$ respectively, in standard form. 
> The *concatenation* of $P$ and $Q$, written $PQ$, is the program $I_1, I_2, ..., I_s, I_{s+1}, ..., I_{s+t}$ where $P = I_1, ..., I_s$ and the instructions $I_{s+1}, ..., I_{s+t}$ are the instructions of $Q$ with each jump $J(m, n, q)$ replaced by $\\ J(m, n, s + q)$

With this definition, it is clear that the effect of $PQ$ is as desired: any computation under $PQ$ is identical to the corresponding computation under $P$ followed by the computation under $Q$ whose initial configuration is the final configuration from the computation under $P$.

Let's suppose now that we want to compose a program $Q$ having a given program $P$ as a subroutine. To write $Q$ it's important to find some registers that are unaffected by computations under $P$.
- Since $P$ is finite, there is a smallest number $u$ such that none of the registers $R_v$ for $v > u$ is mentioned in P (just to clarify, if $Z(n)$, $S(n)$, $T(m, n)$ or $J(m, n, q)$ are instructions in $P$, then $m,\ n \leq u$).
- During the computation under $P$, the contents of $R_v$ for $v > u$ remains unaltered and has no effect on the values of $r_1, ..., r_u$. Then, when writing our program $Q$, the registers $R_v$ can be used to store information without affecting any computation under the subroutine $P$ and we denote $u$ by $\rho(P)$
- Suppose that $P$ is a program in standard form designed to compute a function $f(x_1, ..., x_n)$. When using $P$ as a subroutine in a larger program the inputs $x_1, ..., x_n$ for which $f(x_1, ..., x_n)$ is desired may be held in registers $R_{l_1}, ..., R_{l_n}$rather than $R_1, ..., R_n$ as the program $P$ requires; in addition to this, the output $f(x_1, ..., x_n)$ may be required fro future purposes to be in some register $R_l$ rather than the conventional $R_1$;
- Finally, the working registers $R_1, ..., R_{\rho(P)}$ for $P$ may contain unwanted information. 

We can modify $P$ to take account of all of these points:
- We write $P[l_1, ..., l_n \rightarrow l]$ and this program has the effect of computing $f(r_{l_1}, ..., r_{l_n})$ and placing the result in $R_l$;
- The only registers affected by this program are, at most, $R_1, R_2, ..., R_{\rho(P)}$ and $R_l$
# Composition (or Substitution)
A common methodology to create new function from old ones is to substitute functions into other function, known also as **Composition of Functions**. We can therefore show how applying this procedure to computable functions, the resulting functions are also computable. We say that $\mathcal{C}$ is *closed* under composition.

> [!SUCCESS] Composition of Functions 
Given $f: \mathbb{N}^k \rightarrow \mathbb{N}$ and $g_1, ..., g_k: \mathbb{N}^n \rightarrow \mathbb{N}$ computable functions.
We define $h: \mathbb{N}^n \rightarrow \mathbb{N}$ such that: $$h(\overset{\rightarrow}{x}) = f(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$$where $\overset{\rightarrow}{x} = (x_1, ..., x_k)$.
We can say that $h(\overset{\rightarrow}{x})$ is defined if and only if $g_1(\overset{\rightarrow}{x}), .., g_k(\overset{\rightarrow}{x})$ are defined and $g_1(\overset{\rightarrow}{x}), .., g_k(\overset{\rightarrow}{x}) \in Dom(f)$

> [!INFO] Proof
> Suppose that $F, G_1, ..., G_k$ are programs in standard form which respectively compute $f, g_1, ..., g_k$. We will write a program $H$ that embodies the following procedure for computing $h$:
> "Given $\overset{\rightarrow}{x}$, use the programs $G_1, ..., G_k$ to compute in succession $g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x})$, making a note of these values as they are obtained. Then, use the program $F$ to compute $f(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$"
> 
> Since we must take care to avoid losing information needed at later stages in the procedure, we put $m = max(n, k, \rho(P), \rho(G_1), ..., \rho(G_k))$ and we start storing $\overset{\rightarrow}{x}$ in $R_{m +1}, ..., R_{m + n}$ and the registers $R_{m + n + 1}, ..., R_{m + n + k}$ will be used to store the values of $g_i(\overset{\rightarrow}{x})$ as they are computed for $i = 1, 2, ..., k$. These registers are completely ignored by computations under $F, G_1, ..., G_k$. We can represent it in this way:
> ![[ComputableFunctions_CompositionRegisters.png]]
> And write it this way:
> $$\begin{aligned}&T(1, m + 1) \\ &. \\ &. \\ &. \\ &T(n, m + n) \\ &G_1[m + 1, m + 2, ..., m + n \rightarrow m + n + 1] \\ &. \\ &. \\ &. \\ &G_k[m + 1, m + 2, ..., m + n \rightarrow m + n + k] \\ &F[m + n + 1, ..., m + n + k \rightarrow 1]\end{aligned}$$A computation $H(\overset{\rightarrow}{x})$ will stop if and only if each computation $G_i(\overset{\rightarrow}{x})$ stops $\forall i \ s.t\ 1 \leq i \leq k$ and the computation $F(g_1(\overset{\rightarrow}{x}), ..., g_k(\overset{\rightarrow}{x}))$ stops, exactly as required.

# Primitive Recursion
**Recursion** is a method of defining functions by specifying each of its values in terms of previously defined values, and possible using other already defined functions.
> [!SUCCESS] Primitive Recursion
Given the following functions $f: \mathbb{N}^n \rightarrow \mathbb{N}$ and $g: \mathbb{N}^{n + 2} \rightarrow \mathbb{N}$: $$f(\overset{\rightarrow}{x}) \\ \\g(\overset{\rightarrow}{x}, y, z)$$We can define a new function $h: \mathbb{N}^{n + 1 } \rightarrow \mathbb{N}$ as follows: $$\begin{cases}h(\overset{\rightarrow}{x}, 0) = f(\overset{\rightarrow}{x}) \\ \\ h(\overset{\rightarrow}{x}, y + 1) = g(\overset{\rightarrow}{x}, y, h(\overset{\rightarrow}{x}, y))\end{cases}$$

>[!SUCCESS] $\mathcal{C}$ is *closed* under primitive recursion
>Suppose that $f(\overset{\rightarrow}{x})$ and $g(\overset{\rightarrow}{x}, y, z)$ are computable functions, where $\\ \overset{\rightarrow}{x} = (x_1, ..., x_n)$; then the function $h(\overset{\rightarrow}{x}, y)$ obtained from $f$ and $g$ by recursion is computable.

>[!INFO] Proof
>Let $F$ and $G$ be programs in standard form which compute the functions $f(\overset{\rightarrow}{x})$ and $g(\overset{\rightarrow}{x}, y, z)$. We will devise a program $H$ for the function $h(\overset{\rightarrow}{x}, y)$  given by recursion.
>Given an initial configuration $x_1, ..., x_n, 0, ...$, $H$ will first compute $h(\overset{\rightarrow}{x}, 0)$ (so using $F$) and then, if $y \neq 0$, $H$ will use $G$ to compute successively $h(\overset{\rightarrow}{x}, 1)$, $h(\overset{\rightarrow}{x}, 2)$, ..., $h(\overset{\rightarrow}{x}, y)$ and then stop.
>Let $m = max(n + 2), \rho(F), \rho(G)$; we begin by storing $\overset{\rightarrow}{x}$, $y$ in $R_{m + 1}, ..., R_{m + n + 1}$; the next 2 registers will be used to store the current value of the number $k$ and $h(\overset{\rightarrow}{x}, k)$ for $k = 1, 2, ..., y$. Writing $t = m + n$, a typical configuration is the following
>
>![[ComputableFunctions_RecursionRegisters.png]]
>
>And we can write it in this way: $$\begin{aligned}&T(1, m + 1) \\ &. \\ &. \\ &. \\ &T(n + 1, m + n + 1) \\ &F[1, 2, ..., n \rightarrow t + 3] \\ I_q\ &J(t+2, t + 1, p) \\ &G[m + 1, ..., m + n, t + 2, t + 3 \rightarrow t + 3] \\ &S(t + 2) \\ &J(1, 1, q) \\ I_p \ &T(t + 3, 1)\end{aligned}$$
>
>Hence, $h$ is computable.

## Examples of Primitive Recursion Computable Functions
### Sum: $h(x, y) = x + y$

$\begin{cases}x + 0 = x \\ \\ x + (y + 1) = (x + y) + 1\end{cases}$

> [!INFO] Proof that Sum is Computable by Primitive Recursion
> $f(x) = x = U^1_1(x)$
> $g(x, y, z) = z + 1$ where $z = x + y$
> So it's a definition by recursion from the computable functions $x$ and $\\ z + 1$.
### Product: $h(x, y) = x * y$

$\begin{cases} x * 0 = 0 \\ \\ x * (y + 1) = (x * y) + x \end{cases}$

> [!INFO] Proof that Product is Computable by Primitive Recursion
> $f(x) = 0$
> $g(x, y, z) = z + y$
> So it's a definition by recursion from the computable functions $\textbf{0}(x)$ and $\\ z + x$.

### Exponential: $h(x, y) = x^y$

$\begin{cases} x^0 = 1 \\ \\ x^{y + 1} = (x^y) * x \end{cases}$

### Predecessor: $h(y) = y\ \dot{-}\ 1$

$\begin{cases} 0\ \dot{-}\ 1 = 0 \\ \\ (y + 1)\ \dot{-}\ 1 = y \end{cases}$

### Difference: $h(x, y) = x\ \dot{-}\ y$

$\begin{cases} x\ \dot{-}\ 0 = x \\ \\ x \dot{-}\ (y + 1) = (x\ \dot{-}\ y)\ \dot{-}\ 1 \end{cases}$

### Sign: $sg(x) = \begin{cases}0 \text{ if } y = 0 \\ \\ 1 \text{ if } y > 0\end{cases}$

$\begin{cases}sg(0) = 0  \\ \\ sg(y + 1) = 1\end{cases}$

### Negated Sign: $\overset{-}{sg}(x) = \begin{cases}1 \text{ if } y = 0 \\ \\ 0 \text{ if } y > 0\end{cases}$ 
### Absolute Value: $|x - y| = (x\ \dot{-}\ y) + (y\ \dot{-}\ x)$

### Minimum: $min(x, y) = x\ \dot{-}\ (x\ \dot{-}\ y)$

### Maximum: $max(x, y) = x + (y\ \dot{-}\ x)$

### Remainder: $rm(x, y)$

$\begin{cases}rm(x, 0) = 0 \\ \\ rm(x, y + 1) = (rm(x, y) + 1) * sg(x\ \dot{-}\ (rm(x, y) + 1))\end{cases}$

## Define a computable function by cases
Let $f_1, ..., f_n : \mathbb{N}^n \rightarrow \mathbb{N}$ be computable total functions and $Q_1(\overset{\rightarrow}{x}), ..., Q_n(\overset{\rightarrow}{x}) \subseteq \mathbb{N}^n$ decidable predicates such that $\forall \overset{\rightarrow}{x}\ \exists!y : Q_j(\overset{\rightarrow}{x})$ holds.
Then $f: \mathbb{N}^n \rightarrow \mathbb{N}$ given by $$f(\overset{\rightarrow}{x}) = \begin{cases}f_1(\overset{\rightarrow}{x})\ \text{ if } Q_1(\overset{\rightarrow}{x}) \\ \\ f_2(\overset{\rightarrow}{x})\ \text{ if } Q_2(\overset{\rightarrow}{x}) \\ . \\ . \\ . \\ f_n(\overset{\rightarrow}{x})\ \text{ if } Q_n(\overset{\rightarrow}{x})\end{cases}$$is computable.

>[!INFO] Proof
>$f(\overset{\rightarrow}{x}) = f_1(\overset{\rightarrow}{x}) * \mathcal{X}_{Q_1}(\overset{\rightarrow}{x}) + f_2(\overset{\rightarrow}{x}) * \mathcal{X}_{Q_2}(\overset{\rightarrow}{x}) + ... + f_n(\overset{\rightarrow}{x}) * \mathcal{X}_{Q_n}(\overset{\rightarrow}{x})$
>and since we know that $f_1, ..., f_n$ are computable functions, the sum is a computable operation, and the product is a computable operation as well, $f$ is computable by composition.

## Algebra of Decidability
Let $Q_1(\overset{\rightarrow}{x})$, $Q_2(\overset{\rightarrow}{x})$ be decidable predicates. Then
1) $\neg Q_1(\overset{\rightarrow}{x})$;
2) $Q_1(\overset{\rightarrow}{x}) \wedge Q_2(\overset{\rightarrow}{x})$;
3) $Q_1(\overset{\rightarrow}{x}) \lor Q_2(\overset{\rightarrow}{x})$;
are also decidable predicates.

> [!INFO] Proof
> 1) $\mathcal{X}_{\neg Q_1}(\overset{\rightarrow}{x}) = 1\ \dot{-}\ sg(\mathcal{X}_{Q_1}(\overset{\rightarrow}{x}))$;
> 2) $\mathcal{X}_{Q_1 \wedge Q_2}(\overset{\rightarrow}{x}) = \mathcal{X}_{Q_1}(\overset{\rightarrow}{x}) * \mathcal{X}_{Q_2}(\overset{\rightarrow}{x})$;
> 3) $\mathcal{X}_{Q_1 \land Q_2}(\overset{\rightarrow}{x}) = sg(\mathcal{X}_{Q_1}(\overset{\rightarrow}{x}) + \mathcal{X}_{Q_2}(\overset{\rightarrow}{x}))$

## Bounded Sum and Bounded Product
Recursion can be used to establish the computability of functions obtained by other functions building techniques. 
Assuming $f: \mathbb{N}^{n +1} \rightarrow \mathbb{N}$ is a total computable function:
1) We can define a function $h : \mathbb{N}^n \rightarrow \mathbb{N}$ such that $\\ h(\overset{\rightarrow}{x}, y) = f(\overset{\rightarrow}{x}, 0) + f(\overset{\rightarrow}{x}, 1) + ... + f(\overset{\rightarrow}{x}, y - 1)$ as the *bounded sum* $\Sigma_{z < y}f(\overset{\rightarrow}{x}, z)$
   
   $\begin{cases}h(\overset{\rightarrow}{x}, 0) = 0 \\ \\ h(\overset{\rightarrow}{x}, y + 1) = h(\overset{\rightarrow}{x}, y) + f(\overset{\rightarrow}{x}, y)\end{cases}$ 
   
   which is a total computable function;
1) We can define a function $k: \mathbb{N}^n \rightarrow \mathbb{N}$ such that $\\ k(\overset{\rightarrow}{x}, y) = f(\overset{\rightarrow}{x}, 0) \star f(\overset{\rightarrow}{x}, 1) \star ... \star f(\overset{\rightarrow}{x}, y - 1)$ as the *bounded product* $\Pi_{z < y}f(\overset{\rightarrow}{x}, z)$ which is a total computable function as well.
## Bounded Quantification
Given $Q(\overset{\rightarrow}{x}, z) \subseteq \mathbb{N}^{k +1}$ decidable predicate.
1) $P_1(\overset{\rightarrow}{x}, y) = \forall z < y. Q(\overset{\rightarrow}{x}, z)$;
2) $P_2(\overset{\rightarrow}{x}, y) = \exists z < y. Q(\overset{\rightarrow}{x}, z)$
## Bounded Minimalisation
Given $f: \mathbb{N}^{n + 1} \rightarrow \mathbb{N}$ a total function, we define $h: \mathbb{N}^{n + 1} \rightarrow \mathbb{N}$ such that $$h(\overset{\rightarrow}{x}, y) = \begin{cases} \text{minimum } z : z < y \wedge f(\overset{\rightarrow}{x}, z) = 0 \\ \\ y \text{ otherwise}\end{cases} = \mu z < y. f(\overset{\rightarrow}{x}, y)$$
# Minimalisation
There exists a third important operation which generates further computable functions, named *unbounded minimalisation*, or just *minimalisation*.

Given $f: \mathbb{N}^{n + 1} \rightarrow \mathbb{N}$ not necessarily total, we define a function $h: \mathbb{N}^n \rightarrow \mathbb{N}$ such that $h(\overset{\rightarrow}{x}) = \mu y.f(\overset{\rightarrow}{x}, y)$ in such a way that if $f$ is computable, then also $h$ is computable. We can have two major problems: 
1) For some $\overset{\rightarrow}{x}$ there may not be any $y$ such that $f(\overset{\rightarrow}{x}, y) = 0$;
2) Assuming that $f$ is computable, the procedure may not terminate if $f$ is not total, even if $y$.
We are therefore led to the $\mu$ operator, which yields computable functions from computable functions.

> [!SUCCESS] Definition of Minimalisation
> For any function $f(\overset{\rightarrow}{x}, y)$
> $$\mu y.f(\overset{\rightarrow}{x}, y) = \begin{cases}\text{the least } y \text{ such that:} \\ 1) f(\overset{\rightarrow}{x}, z) \text{ is defined } \forall x \leq y \\ 2) f(\overset{\rightarrow}{x}, y) = 0 \text{ if such a } y \text{ exists} \\ \\ \uparrow \text{ if there no such } y\end{cases}$$

>[!SUCCESS] $\mathcal{C}$ is closed under Minimalisation
> If $f(\overset{\rightarrow}{x}, y)$ is computable then so is the function $h(x) = \mu y. f(\overset{\rightarrow}{x}, y)$

> [!INFO] Proof
> Suppose that $\overset{\rightarrow}{x} = (x_1, ..., x_n)$ and $F$ is a program in standard form that computes the function $f(\overset{\rightarrow}{x}, y)$. Let $m = max(n + 1, \rho(F))$ We write a program $H$ that embodies the natural algorithm for $h$: for $k = 0, 1, 2, ...$, compute $f(\overset{\rightarrow}{x}, k)$ until a value of $k$ is found such that $f(\overset{\rightarrow}{x}, k) = 0$. 
> The value of $\overset{\rightarrow}{x}$ and the current value of $k$ will be stored in registers $R_{m + 1}, ..., R_{m + n + 1}$ before computing $f(\overset{\rightarrow}{x}, k)$. This translates into the following program $H$ for $h$: $$\begin{aligned} &T(1, m + 1) \\ &. \\ &. \\ &. \\ &T(n, m + n) \\ I_p\ \ &F[m + 1, m + 2, ..., m + n + 1 \rightarrow 1] \\ &J(1, m + n + 2, q) \\ &S(m + n + 1) \\ &J(1, 1, p) \\ I_q \ \ &T(m + n + 1, 1)&\end{aligned}$$

