# Algorithms or Effective Procedures
An **algorithm** or **effective procedure** is a mechanical rule, or automatic method, or programme for performing some mathematical operation.

The input of the algorithm is the raw data or object on which the operation is to be performed and the output is the result of the operation. 
The algorithm is the procedure or method that is carried out by our computer to obtain the output from the input.
When an algorithm is used to calculate the values of a numerical function, then the function is described as *computable*.

>[!SUCCESS] General Definition of Computable Function
>A function $f$ is **computable** if there exists an algorithm such that the induced function is $f$.
# Unlimited Register Machine (URM)
Our mathematical idealisation of a computer is called an *unlimited register machine (URM)*. 
The URM has an infinite number of **registers** labelled $R_1, R_2, ...$, each of which at any moment of time contains a natural number $n \in \mathbb{N}$; the number contained in the register $R_n$ is denoted with $r_n$.

>[!INFO] Visual Representation of the URM Registers
>![[ComputableFunctions_URMRegisters.png]]

The contents of these register can be potentially altered by the URM in response to certain *instructions* that it can recognise and they correspond to very simple operations used in performing calculations with numbers. A finite list of instructions constitutes a *program*. The instructions of a program are taken from a small instruction set:
- **Zero Instruction**: For each $n = 1, 2, 3, ...$ there is a *zero instruction* $Z(n)$ and the response of the URM to the instruction $Z(n)$ is to change the content of the register $R_n$ to $0$, leaving all the others unaltered, denoted by $R_n \leftarrow 0$;

 > [!INFO] Execute the operation $Z(3)$ on the following URM configuration
 > ![[ComputableFunctions_ZeroFunction_1.png]]results in![[ComputableFunctions_ZeroFunction_2.png]]
 
- **Successor Instruction**: For each $n = 1, 2, 3, ...$ there is a *successor instruction* $S(n)$ and the response of the URM to the instruction $S(n)$ is to increase the content of the register $R_n$ by $1$, leaving all the others unaltered, denoted by $R_n \leftarrow r_n + 1$;

 > [!INFO] Execute the operation $S(5)$ on the following URM configuration
 > ![[ComputableFunctions_ZeroFunction_2.png]]results in![[ComputableFunctions_SuccessorFunction.png]]
 
- **Transfer Instruction**: For each $m = 1, 2, 3, ...$ and $n = 1, 2, 3, ...$ there is a *transfer instruction* $T(m, n)$ and the response is to replace the content of $R_n$ by the number contained in $R_m$, leaving all the others unaltered, denoted by $R_n \leftarrow r_m$;

 > [!INFO] Execute the operation $T(5, 1)$ on the following URM configuration
 > ![[ComputableFunctions_SuccessorFunction.png]]results in![[ComputableFunctions_TransferFunction.png]]
 
- **Jump Instruction**: For each $m = 1, 2, 3, ...$, $n = 1, 2, 3, ...$ and $q = 1, 2, 3, ...$ there is a *jump instruction* $J(m, n, q)$ and the response is the following:
	- Supposing that this instruction is encountered in a program $P$; the content of the $R_m$ and $R_n$ are compared and:
		- If $r_m = r_n$, the URM proceeds to the $q$-th instruction of $P$;
		- If $r_m \neq r_n$, the URM proceeds to the next instruction of $P$
	- If the jump is impossible because $P$ has less than $q$ instructions, then the URM stops operation.
## Computations
To perform a computation, the URM must be provided with a program $P$ and an initial configuration of the registers $R_1, R_2, R_3, ...$.
Let's now suppose that $P$ consists of $s$ instructions $I_1, I_2, ..., I_s$; the URM begins the computation by obeying $I_1$, then $I_2$, then $I_3$ and so on unless a jump instruction is encountered. In this case, the URM proceeds to the instruction prescribed by $J(m, n, q)$ and the current contents of $R_m$ and $R_n$.

We can describe the operation of the URM under a program $P = I_1, I_2, ..., I_s$ as follows. The URM starts by obeying instruction $I_1$ and, at any future stage in the computation, the URM is obeying the instruction $I_k$. Then, having done so it proceeds to the next instruction in the computation:
- If $I_k$ is not a jump instruction, the next instruction is $I_{k + 1}$;
- If $I_k$ is a jump instruction, the next instruction is $\begin{cases} I_q \text{ if } r_m = r_n \\ I_{k + 1} \text{ otherwise} \end{cases}$
The URM then proceeds as long as possible and the computation stops when and only when there is no next instruction. 

>[!INFO] Notation of Terminating and Diverging Computations
>Let $a_1, a_2, ... \in \mathcal{N}$ be an infinite sequence and let $P$ be a program. We write:
>1. $P(a_1, a_2, ...)$ for the computation under $P$ with initial configuration $a_1, a_2, ...$;
>2. $P(a_1, a_2, ...)\downarrow$ to mean that the computation $P(a_1, a_2, ...)$ eventually stops;
>3. $P(a_1, a_2, ...)\uparrow$ to mean that the computation $P(a_1, a_2, ...)$ never stops;
>   
>In the majority of the initial configuration, we can consider all but finitely many of the $a_i$ to be $0$. Therefore, let $a_1, a_2, ..., a_n$ be a finite sequence of natural numbers. We write:
>4. $P(a_1, a_2, ..., a_n)$ for the computation $P(a_1, a_2, ..., a_n, 0, 0, ...)$;
>5. $P(a_1, a_2, ..., a_n)\downarrow$ to mean that $P(a_1, a_2, ..., a_n, 0, 0, ...)\downarrow$;
>6. $P(a_1, a_2, ..., a_n)\uparrow$ to mean that $P(a_1, a_2, ..., a_n, 0, 0, ...)\uparrow$
>A computation that stops is said to *converge*, and one that never stops is said to *diverge*.
# URM-Computable Functions

> [!SUCCESS] Definition of URM-Computable Function
> Let $f: \mathbb{N}^n \rightarrow \mathbb{N}$ be a partial function, $P$ a program and $a_1, a_2, ..., a_n, b \in \mathbb{N}$.
> 	- The computation $P(a_1, a_2, ..., a_n)$ **converges** to $b$ if $P(a_1, a_2, ..., a_n)\downarrow$ and in the final configuration $b$ is in $R_1$, written $P(a_1, a_2, ..., a_n)\downarrow b$;
> 	- $P$ **URM-computes** $f$ if, for every $a_1, a_2, ..., a_n, b$, $P(a_1, a_2, ..., a_n)\downarrow b$ if and only if $(a_1, a_2, ..., a_n) \in Dom(f)$ and $f(a_1, a_2, ..., a_n) = b$;
> - The function $f$ is **URM-computable** if there is a program $P$ that URM-computes $f$

The class of URM-computable functions is denoted by $\mathcal{C}$, and $n$-ary URM-computable functions by $\mathcal{C^n}$. Given that, we can say
- $\mathcal{C}^n = \{\ f\ |\ f : \mathbb{N}^n \rightarrow \mathbb{N} \text{ is URM-Computable }\}$;
- $\mathcal{C} = \underset{k \geq 1}{\cup} \mathcal{C}^n$

Given any program $P$ and $n \geq 1$, by thinking of the effect of $P$ on initial configurations of the form $a_1, a_2, ..., a_n, 0, 0, ...$ we see that there is a unique $n$-ary function that $P$ computes, denoted by $f^{(n)}_P$. From this declaration, we have $$f^{(n)}_P(a_1, ..., a_n) = \begin{cases}b \text{ if } P(a_1, ..., a_n)\downarrow b \\ \uparrow \text{ if } P(a_1, ..., a_n) \uparrow \end{cases}$$A particular computable function can be computed by many different programs; for instance, any program can be altered by adding instructions that have no effect. In terms of the notation, we can have different programs $P_1$ and $P_2$ such that $f^{(n)}_{P_1} = f^{(n)}_{P_2}$ for some (or all) $n$.

## Decidable Predicates
In mathematics, a common task is to **decide** whether numbers possess a given property and an algorithm for this operation would be an effective procedure that on inputs $x$ and $y$ gives output $Yes$ or $No$. Thus, we can say that the property (or predicate) is effectively decidable if this function $f$ is computable.

> [!SUCCESS] Definition of Decidable Predicate
Let $Q(x_1, x_2, ..., x_n) \subseteq N^n$ a predicate. 
The predicate is *decidable* if the *characteristic function* $$\mathcal{X}_Q: \mathbb{N}^n \rightarrow \mathbb{N} \\ \mathcal{X}_Q(x_1, ..., x_n) = \begin{cases}1 \text{ if } Q(\overset{\rightarrow}{x}) \text{ holds}\\ 0 \text{ if } Q(\overset{\rightarrow}{x}) \text{ doesn't hold}\end{cases}$$
is computable; $Q(\overset{\rightarrow}{x})$ is undecidable if $Q(\overset{\rightarrow}{x})$ is not decidable.