From book Semantics with Applications, An Appetizer (by Nielson and Nielson)
# Introduction 
## The While Language
In the Software Verification course, we illustrate the various forms of semantics on a very simple imperative programming language called **While**. To begin, we must specify its syntax, which syntactic notation is based on **Backus-Naur Form (BNF)** of context-free grammars. We firstly list the *syntactic categories* and give a meta-variable used to range over the constructs of each category. 

We will use:
- $n$ will range over numerals, denoted as $\textbf{Num}$;
- $x$ will range over variables, denoted as $\textbf{Var}$;
- $a$ will range over arithmetic expressions, denoted as $\textbf{Aexp}$;
- $b$ will range over boolean expressions, denoted as $\textbf{Bexp}$;
- $S$ will range over statements, denoted as $\textbf{Stm}$

Supposing that the structure of numerals and variables is given elsewhere, we will focus on the structure of the other constructs:
- $a::= n\ |\ x\ |\ a_1 + a_2\ | \ a_1 * a_2\ |\ a_1 - a_2$;
- $b ::= \text{true } | \text{ false } |\ a_1 = a_2\ |\ a_1 \leq a_2\ |\ \neg b\ |\ b_1 \wedge b_2$;
- $S ::= x := a\ | \text{ skip }|\ S_1;S_2\ |\text{ if }b \text{ then } S_1 \text{ else } S_2\ | \text{ while } b \text{ do } S$

## Semantics of Expressions
Assuming for a moment that the numerals are in binary system, their syntax can be specified by $$n ::= 0\ |\ 1\ |\ n\ 0\ |\ n\ 1$$In order to determine the number represented by a numeral, we can define a function $$\mathcal{N}: \textbf{Num} \rightarrow \mathbb{Z}$$This is called a **semantic function**, since it defines the semantics of numerals. Keep in mind that we want $\mathcal{N}$ to be a total function because we want to determine a unique number for each numeral of $\textbf{Num}$. If $n \in \textbf{Num}$, we write $\mathcal{N}[\![n]\!]$ for the application of $\mathcal{N}$ to $n$. The semantic function $\mathcal{N}$ is defined by the following *semantic equations*: 
$$
\begin{aligned}
\mathcal{N}[\![0]\!] &= \textbf{0} \\\\ \mathcal{N}[\![1]\!] &= \textbf{1} \\\\ \mathcal{N}[\![n\ 0]\!] &= \textbf{2} • \mathcal{N}[\![n]\!] \\ \\ \mathcal{N}[\![n\ 1]\!] &= \textbf{2} • \mathcal{N}[\![n]\!] \ + \textbf{1}
\end{aligned}
$$
In here, $\textbf{0}$ and $\textbf{1}$ are mathematical numbers; $\textbf{•}$ and $\textbf{+}$ are arithmetic operations on numbers.

> [!INFO] Example: Calculate the number $\mathcal{N}[\![101]\!]$
> $$
> \begin{aligned}
> \mathcal{N}[\![101]\!] &= \textbf{2} • \mathcal{N}[\![10]\!] + \textbf{1} \\
> &= \textbf{2} • (\textbf{2} • \mathcal{N}[\![1]\!]) + \textbf{1} \\
> &= \textbf{2} • (\textbf{2} • \textbf{1}) + \textbf{1} \\
> &= \textbf{5}
> \end{aligned}
> $$

The general technique, called **Compositional Definition**, that we adopt in the definition of the syntax and semantics can be summarised as follows:
1. The syntactic category is specified by an abstract syntax giving the basis elements and the composite elements. The composite elements have a unique decomposition into their immediate constituents;
2. The semantics is defined by compositional definition of a function: there is a semantic equation (or clause) for each of the basis elements of the syntactic category and one for each of the method for constructing composite elements. The clause for composite elements are defined in terms of the semantics of the immediate constituents of the elements.
The proof technique that we apply, called **Structural Induction**, is closely connected with the approach to defining semantic functions and can be summarised as follows:
- Prove that the property holds for all the basis elements of the syntactic category;
- Prove that the property holds for all the composite elements of the syntactic category. By assuming that the property holds for all the immediate constituents of the element, which is called **induction hypothesis**, and prove that it also holds for the element itself.

### Semantic Functions
The meaning of an expression depends on the values bound to the variables that occur in it. We shall then introduce the concept of a **state**: to each variable the state will associate its current value and we represent it as a function from variables to values $$\textbf{State} = \textbf{Var} \rightarrow \mathbb{Z}$$Each state $s$ specifies a value, written $s\ x$, for each variable $x$ of $\textbf{Var}$.
Given the arithmetical expression $a$ and a state $s$, we can determine the value of the expression. From that, we can define the meaning of arithmetic expressions as a total function $\mathcal{A}$ that takes two (2) arguments: the syntactic construct and the state $$\mathcal{A}: \textbf{Aexp} \rightarrow (\textbf{State} \rightarrow \mathbb{Z})$$This means that $\mathcal{A}$ takes its parameters one at a time.
Assuming the existence of the function $\mathcal{N}$ defining the meaning of numerals, we can define the function $\mathcal{A}$ by defining its value $\mathcal{A}[\![a]\!]s$ on each arithmetic expression $a$ and state $s$. 
$$
\begin{aligned}
\mathcal{A}[\![n]\!]s &= \mathcal{N}[\![n]\!] \\ \\ \mathcal{A}[\![x]\!]s &= s\ x \\ \\ \mathcal{A}[\![a_1 + a_2]\!]s &= \mathcal{A}[\![a_1]\!]s + \mathcal{A}[\![a_2]\!]s \\ \\ \mathcal{A}[\![a_1 - a_2]\!]s &= \mathcal{A}[\![a_1]\!]s - \mathcal{A}[\![a_2]\!]s \\ \\ \mathcal{A}[\![a_1 * a_2]\!]s &= \mathcal{A}[\![a_1]\!]s * \mathcal{A}[\![a_2]\!]s
\end{aligned}$$

> [!INFO] Example: Supposing that $s\ x = \textbf{3}$, calculate $\mathcal{A}[\![x+1]\!]s$
> $$
> \begin{aligned}
> \mathcal{A}[\![x+1]\!]s &= \mathcal{A}[\![x]\!]s + \mathcal{A}[\![1]\!]s \\
> &= (s\ x) + \mathcal{N}[\![1]\!] \\
> &= \textbf{3} + \textbf{1} \\
> &= \textbf{4}
> \end{aligned}
> $$

As for boolean expressions, the values are truth value, so in a similar way we can define their meanings by a total function from $\textbf{State}$ to $\textbf{T}$ $$\mathcal{B}: \textbf{Bexp} \rightarrow (\textbf{State} \rightarrow \textbf{T})$$where $\textbf{T}$ consists of the truth values $\textbf{tt}$ for true and $\textbf{ff}$ for false. $$\begin{aligned}
	 &\mathcal{B}[\![\texttt{true}]\!]s &=& \ \texttt{tt} \\ \\
	 &\mathcal{B}[\![\texttt{false}]\!]s &=& \ \texttt{ff} \\ \\
	 &\mathcal{B}[\![a_1 = a_2]\!]s &=& \ \begin{cases} \texttt{tt} \ \textnormal{if} \ \mathcal{A}[\![a_1]\!]s = \mathcal{A}[\![a_2]\!]s \\ \texttt{ff} \ \textnormal{if} \ \mathcal{A}[\![a_1]\!]s \neq \mathcal{A}[\![a_2]\!]s \end{cases} \\ \\
	&\mathcal{B}[\![a_1 \leq a_2]\!]s &=& \ \begin{cases} \texttt{tt} \ \textnormal{if} \ \mathcal{A}[\![a_1]\!]s \leq \mathcal{A}[\![a_2]\!]s \\ \texttt{ff} \ \textnormal{if} \ \mathcal{A}[\![a_1]\!]s \nleq \mathcal{A}[\![a_2]\!]s \end{cases} \\ \\
	&\mathcal{B}[\![\neg b]\!]s &=& \ \begin{cases} \texttt{tt} \ \textnormal{if} \ \mathcal{B}[\![b]\!]s = \texttt{ff} \\ \texttt{ff} \ \textnormal{if} \ \mathcal{B}[\![b]\!]s = \texttt{tt} \end{cases} \\ \\
	&\mathcal{B}[\![b_1 \land b_2]\!]s &=& \ \begin{cases} \texttt{tt} \ \textnormal{if} \ \mathcal{B}[\![b_1]\!]s = \texttt{tt} \textnormal{ and } \mathcal{B}[\![b_2]\!]s = \texttt{tt} \\ \texttt{ff} \ \textnormal{if} \ \mathcal{B}[\![b_1]\!]s = \texttt{ff} \textnormal{ and } \mathcal{B}[\![b_2]\!]s = \texttt{ff}\end{cases}\end{aligned}$$

## Properties of the Semantics
We will be interested in two kinds of properties for expressions: one is that their values do not depend on values of variables that do not occur in them; the other is that if we replace a variable with an expression, then we could as well have made a similar change in the state.
### Free Variables
The **free variables** of an arithmetic expression $a$ are defined to be the set of variables occurring in them. Formally, we compositionally define the subset FV($a$) of $\textbf{Var}$: $$\begin{aligned}
FV(n) &= \emptyset \\ \\
FV(x) &= \{\ x\ \} \\ \\
FV(a_1 + a_2) &= FV(a_1)\ \cup \ FV(a_2) \\ \\
FV(a_1 - a_2) &= FV(a_1)\ \cup \ FV(a_2)  \\ \\
FV(a_1 \star a_2) &= FV(a_1)\ \cup \ FV(a_2)  \\ \\
\end{aligned}$$In a similar way, we can also define the set FV($b$) of free variables in a boolean expression $b$: $$\begin{aligned}
FV(true) &= \emptyset \\ \\
FV(false) &= \emptyset \\ \\
FV(a_1 = a_2) &= FV(a_1)\ \cup \ FV(a_2) \\ \\
FV(a_1 \leq a_2) &= FV(a_1)\ \cup \ FV(a_2)  \\ \\
FV(\neg b) &= FV(b)  \\ \\
FV(b_1 \wedge b_2) &= FV(b_1)\ \cup \ FV(b_2) 
\end{aligned}$$
#### Lemma 1.12
Let $s$ and $s'$ be two states satisfying $s\ x = s'\ x$ $\forall x \in FV(a)$. Then $$\mathcal{A}[\![a]\!]s = \mathcal{A}[\![a]\!]s'$$
**Proof:** $\forall a \in Aexp.\ P(a) := \forall s, s' \in State.\ \forall x \in FV(a).\ s\ x = s'\ x \rightarrow \mathcal{A}[\![a]\!]s = \mathcal{A}[\![a]\!]s'$    

*Base Cases* 
By definition of $a$, we know that it can be either $a := n$, $a := x$, or another arithmetical expression:
1) Let's consider the case $a = n$: 
	- By substituting $a$ with $n$, our proposition $P$ becomes $$P(n) := \forall s, s' \in State.\ \forall x \in FV(n).\ sx = s'x \rightarrow \mathcal{A}[\![n]\!]s = \mathcal{A}[\![n]\!]s'$$
	- From the definition of **Free Variables**, we know that $FV(n) = \emptyset$, so now our proposition $P$ becomes $$P(n) := \forall s, s' \in State.\ \mathcal{A}[\![n]\!]s = \mathcal{A}[\![n]\!]s'$$
	- Now, from the definition of **Arithmetical Expression**, we know that $$\mathcal{A}[\![n]\!]s = \mathcal{N}[\![n]\!]$$so the evaluation doesn't depend on $s$
	- Our proposition $P(n)$ therefore holds!
2) Let's now consider the case $a = x$:
	- By substituting $a$ with $n$, our proposition $P$ becomes $$P(x) := \forall s, s' \in State.\ \forall x \in FV(x).\ s\ x = s'\ x \rightarrow \mathcal{A}[\![x]\!]s = \mathcal{A}[\![x]\!]s'$$
	- From the definition of **Free Variables**, we know that $FV(x) = \{\ x\ \}$, so now our proposition $P$ becomes $$P(x) := \forall s, s' \in State.\ \forall x \in \{\ x\ \}.\ s\ x = s'\ x \rightarrow \mathcal{A}[\![x]\!]s = \mathcal{A}[\![x]\!]s'$$
	- Now, from the definition of **Arithmetical Expression**, we know that $$\mathcal{A}[\![x]\!]s = s\ x$$Therefore, our proposition can be written as $$P(x) = \forall s, s' \in State.\ \forall x \in \{\ x\ \}.\ s\ x = s'\ x \rightarrow s\ x = s'\ x $$which is a tautology, therefore also $P(x)$ holds!
*Inductive Steps*
- Let's now consider the arithmetical expression $a = a_1 + a_2$:
	- By substituting $a$ with $n$, our proposition $P$ becomes $$P(a_1 + a_2) = \forall s,\ s' \in State.\ \forall x \in FV(a_1 + a_2).\\ s\ x = s'\ x \rightarrow \mathcal{A}[\![a_1 + a_2]\!]s = \mathcal{A}[\![a_1 + a_2]\!]s'$$
	- From the definition of **Free Variables**, we know that $$FV(a_1 + a_2) = FV(a_1) \cup FV(a_2)$$so now our proposition $P$ becomes $$P(a_1 + a_2) = \forall s,\ s' \in State.\ \forall x \in FV(a_1) \cup FV(a_2).\\ s\ x = s'\ x \rightarrow \mathcal{A}[\![a_1 + a_2]\!]s = \mathcal{A}[\![a_1 + a_2]\!]s'$$
	- Now, from the definition of **Arithmetical Expression**, we know that $$\mathcal{A}[\![a_1 + a_2]\!]s = \mathcal{A}[\![a_1]\!]s + \mathcal{A}[\![a_2]\!]s$$so our proposition becomes $$P(a_1 + a_2) = \forall s,\ s' \in State.\ \forall x \in FV(a_1) \cup FV(a_2).\\ s\ x = s'\ x \rightarrow \mathcal{A}[\![a_1]\!]s + \mathcal{A}[\![a_2]\!]s = \mathcal{A}[\![a_1]\!]s' + \mathcal{A}[\![a_2]\!]s'$$
	- From this, the inductive hypothesis is that the property $P$ holds on the subexpressions $a_1$ and $a_2$, so we know that both $P(a_1)$ and $P(a_2)$ hold. By that, we can rewrite the proposition as follows: $$P(a_i) = \forall s,\ s' \in State. \forall x \in FV(a_i). s\ x = s'\ x \rightarrow \mathcal{A}[\![a_i]\!]s = \mathcal{A}[\![a_i]\!]s'$$
	- Since both $FV(a_1)$ and $FV(a_2)$ are a subset of $FV(a_1) \cup FV(a_2)$ we have that $\forall x \in FV(a_1) s\ x = s'\ x$ and at the same time $\\ \forall x \in FV(a_2) s\ x = s'\ x$
	- Thus, we get $\mathcal{A}[\![a_1]\!]s = \mathcal{A}[\![a_1]\!]s'$ and $\mathcal{A}[\![a_2]\!]s = \mathcal{A}[\![a_2]\!]s'$ concluding our demonstration. $a_1 - a_2$ and $a_1 * a_2$ follow the same pattern.
### Substitutions
We will also be interested in replacing each occurrence of a variable $y$ in an arithmetic expressions $a$ with another arithmetic expression $a_0$. This is called **substitution** and we write $a[y \mapsto a_0]$ for the arithmetic expression obtained. The formal definition is the following: $$
\begin{aligned}
n[y \mapsto a_0] &\triangleq n \\ \\
x[y \mapsto a_0] &\triangleq \begin{cases} &a_0 &\text{ if } x = y \\ &x &\text{ if } x \neq y\end{cases} \\ \\
(a_1+a_2)[y \mapsto a_0] &\triangleq (a_1[y \mapsto a_0]) + (a_2[y \mapsto a_0]) \\ \\
(a_1*a_2)[y \mapsto a_0] &\triangleq (a_1[y \mapsto a_0]) \star (a_2[y \mapsto a_0]) \\ \\
(a_1-a_2)[y \mapsto a_0] &\triangleq (a_1[y \mapsto a_0]) - (a_2[y \mapsto a_0])
\end{aligned}$$
We do also have a notion of substitution or updating for states. We define $s[y \mapsto v]$ to be the state that is like $s$ except that the value bound to $y$ is $v$:$$
(s[y \mapsto v])\ x \triangleq \begin{cases} &v &\text{ if } x = y \\ &s\ x &\text{ if } x \neq y\end{cases}
$$
# Operational Semantics
The key role of a statement in our $\textbf{While}$ language is to change the state. So while the semantics of arithmetic and boolean expressions only inspect the state in order to determine the value of the expression, the semantics of statements will modify the state as well.
In an operational semantics, we are concerned about **HOW** to execute programs and in **HOW** the states are modified during the execution of the statement.
We shall then consider two different approaches:
- **Natural Semantics**: its purpose is to describe how the overall results of executions are obtained, also called **big-step** operational semantics;
- Structural Operational Semantics: its purpose is to describe how the individual steps of the computations take place, also called **small-step** operational semantics
For both kinds of operational semantics, the meaning of statements will be defined by a transition system, which will have two types of configurations:
- $\langle S,\ s \rangle$ representing that the statement $S$ is to be executed from the state $s$
- $s$     representing a terminal state.
The terminal configuration will be the latter form. The transition relation will describe how the execution takes place.
## Structural Operational Semantics
As said, in a structural operational semantics, the emphasis is on the individual steps of the execution. The transition relation has the form $$\langle S, s \rangle \Rightarrow \gamma$$where $\gamma$ is either of the form $\langle S',\ s' \rangle$ or of the form $s'$. The transition expresses the first step of the execution of $S$ from the state $s$. That said, there are two possible outcomes:
- If $\gamma$ is of the form $\langle S',\ s' \rangle$, then the execution of $S$ from $s$ is not completed and the remaining computation is expressed by the intermediate configuration $\langle S',\ s' \rangle$;
- If $\gamma$ is of the form $s'$, then the execution of $S$ from $s$ has terminated and the final state is $s'$
We shall then say that $\langle S, s \rangle$ is **stuck** if there is no $\gamma$ such that $\langle S, s \rangle \Rightarrow \gamma$.
The definition of $\Rightarrow$ is given by the following axioms: 
$$
\begin{aligned}
&[\text{ass}_\text{sos}] &\langle x := a,\ s\rangle \Rightarrow s[x \mapsto \mathcal{A}[\![a]\!]s] \\ \\
&[\text{skip}_{\text{sos}}] & \langle \text{skip},\ s\rangle \Rightarrow s \\ \\
&[\text{comp}^1_\text{sos}] &\dfrac{\langle S_1,\ s \rangle \Rightarrow \langle S'_1,\ s' \rangle}{\langle S_1;S_2,\ s \rangle \Rightarrow \langle S'_1;S_2,\ s' \rangle} \\ \\
&[\text{comp}^2_\text{sos}] &\dfrac{\langle S_1,\ s \rangle \Rightarrow s'}{\langle S_1;S_2,\ s \rangle \Rightarrow \langle S_2,\ s' \rangle} \\ \\
&[\text{if}^\texttt{tt}_\text{sos}] &\langle \text{if}\ b\ \text{then}\ S_1\ \text{else}\ S_2,\ s \rangle \Rightarrow \langle S_1,\ s \rangle\ \text{if}\ \mathcal{B}[\![b]\!]s = \texttt{tt} \\ \\
&[\text{if}^{\texttt{ff}}_\text{sos}] &\langle \text{if}\ b\ \text{then}\ S_1\ \text{else}\ S_2,\ s \rangle \Rightarrow \langle S_2,\ s \rangle\ \text{if}\ \mathcal{B}[\![b]\!]s = \texttt{ff} \\ \\
&[\text{while}_\text{sos}] &\langle \text{while } b\ \text{do } S,\ s \rangle \Rightarrow \langle \text{if } b \text{ then } (S; \text{while } b\ \text{do } S) \text{ else skip},\ s \rangle
\end{aligned}
$$
- The axiom $[\text{ass}_\text{sos}]$ says that in a state $s$, $x := a$ is executed to yield a final state $s[x \mapsto \mathcal{A}[\![a]\!]s]$, which is like $s$ except that $x$ has the value $\mathcal{A}[\![a]\!]s$;
- Similarly, the axiom $[\text{skip}_\text{sos}]$ says that $\texttt{skip}$ does not change the state;
- The rules $[\text{comp}^1_\text{sos}]$ and $[\text{comp}^2_\text{sos}]$ express that to execute $S_1; S_2$ in a state $s$ we first execute $S_1$ one step from $s$. Then there are two possible outcomes:
	- If the execution of $S_1$ has not been completed, we have to complete it before embarking on the execution of $S_2$;
	- If the execution of $S_1$ has been completed, we can start on the execution of $S_2$
	The first case is captured by $[\text{comp}^1_\text{sos}]$, while the second case is captured by $[\text{comp}^2_\text{sos}]$
- From the axioms $[\text{if}^\texttt{tt}_\text{sos}]$ and $[\text{if}^\texttt{ff}_\text{sos}]$ we see that the first step in executing a conditional is to perform the test and to select the appropriate branch;
- The axiom $[\text{while}_\text{sos}]$ shows that the first step in the execution of the $\texttt{while}$-construct is to unfold it one level; that is, to rewrite it as a conditional. The test will therefore be performed in the second step of the execution.
A **derivation sequence** of a statement $S$ starting in state $s$ is either:
- A finite sequence $$\gamma_0,\ \gamma_1,\ \gamma_2,\ ...,\ \gamma_k$$ also written $$\gamma_0 \Rightarrow \gamma_1 \Rightarrow \gamma_2 \Rightarrow ... \Rightarrow \gamma_k$$consisting of configurations satisfying $\gamma_0 = \langle S,\ s \rangle,\ \gamma_i \Rightarrow \gamma_{i+1}$ for $0 \leq i \leq k$, where $k \geq 0$ and where $\gamma_k$ is either a terminal or stuck configuration;
- An infinite sequence $$\gamma_0,\ \gamma_1,\ \gamma_2,\ ...$$also written $$\gamma_0 \Rightarrow \gamma_1 \Rightarrow \gamma_2 \Rightarrow ... $$consisting of configuration satisfying $\gamma_0 = \langle S,\ s \rangle,\ \gamma_i \Rightarrow \gamma_{i+1}$ for $0 \leq i$.

We shall write $\gamma_0 \Rightarrow^i \gamma_i$ to indicate that there are $i$ steps in the execution from $\gamma_0$ to $\gamma_i$ and we write $\gamma_0 \Rightarrow^* \gamma_i$ to indicate that there are a finite number of steps.

Given a statement $S$ in the language $\textbf{While}$ and a state $s$, it is always possible to find at least one derivation sequence that starts in the configuration $\langle S,\ s\rangle$: simply apply axioms and rules forever or until a terminal or stuck configuration is reached.
In analogy with the terminology of the previous section, we shall say that the execution of a statement $S$ on a state $s$:
- **Terminates** if and only if there is a finite derivation sequence starting with $\langle S,\ s\rangle$;
- **Loops** if and only if there is a infinite derivation sequence starting with $\langle S,\ s\rangle$
We shall then say that the execution of $S$ on $s$ *terminates successfully* if $\langle S,\ s\rangle \Rightarrow^* s'$ for some state $s'$; in our $\textbf{While}$ language, an execution terminates successfully if and only if it terminates because there are no stuck configurations. 