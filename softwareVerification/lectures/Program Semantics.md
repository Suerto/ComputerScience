# Introduction 
## The While Language
In the **Software Verification** course, we illustrate the various forms of semantics on a very simple imperative programming language called **While**. To begin, we must specify its syntax, which syntactic notation is based on **Backus-Naur Form (BNF)** of context-free grammars. We firstly list the *syntactic categories* and give a meta-variable used to range over the constructs of each category. 
We will use:
- $n$ to range over numerals, denoted as $\textbf{Num}$;
- $x$ to range over variables, denoted as $\textbf{Var}$;
- $a$ to range over arithmetic expressions, denoted as $\textbf{Aexp}$;
- $b$ to range over boolean expressions, denoted as $\textbf{Bexp}$;
- $S$ to range over statements, denoted as $\textbf{Stm}$
Supposing that the structure of numerals and variables is given elsewhere, we will focus on the structure of the other constructs:
- $a::= n\ |\ x\ |\ a_1 + a_2\ | \ a_1 * a_2\ |\ a_1 - a_2$;
- $b ::= \text{true } | \text{ false } |\ a_1 = a_2\ |\ a_1 \leq a_2\ |\ \neg b\ |\ b_1 \wedge b_2$;
- $S ::= x := a\ | \text{ skip }|\ S_1;S_2\ |\text{ if }b \text{ then } S_1 \text{ else } S_2\ | \text{ while } b \text{ do } S$
## Semantics of Expressions
Assuming for a moment that the numerals are in binary system, their syntax can be specified by $$n ::= 0\ |\ 1\ |\ n\ 0\ |\ n\ 1$$In order to determine the number represented by a numeral, we define a function $$\mathcal{N}: \textbf{Num} \rightarrow \mathbb{Z}$$This is called a **semantic function**, since it defines the semantics of numerals. Keep in mind that we want $\mathcal{N}$ to be a total function because we want to determine a unique number for each numeral of $\textbf{Num}$. If $n \in \textbf{Num}$, we write $\mathcal{N}[\![n]\!]$ for the application of $\mathcal{N}$ to $n$. Moreover, the application of a semantic function to a syntactic entity will be written within the syntactic brackets "$[\![$" and "$]\!]$". The semantic function $\mathcal{N}$ is defined by the following *semantic equations*: 
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
From now on, let's assume that numerals are in decimal notation and have their normal meaning (so $\mathcal{N}[\![69]\!] = \textbf{69} \in \mathbb{Z}$).
### Semantic Functions
The meaning of an expression depends on the values bound to the variables that occur in it. We shall then introduce the concept of a **state**: to each variable the state will associate its current value and we represent it as a function from variables to values: $$\textbf{State} = \textbf{Var} \rightarrow \mathbb{Z}$$Each state $s$ specifies a value, written $s\ x$, for each variable $x$ of $\textbf{Var}$.
Given the arithmetical expression $a$ and a state $s$, we can determine the value of the expression. From that, we can define the meaning of arithmetic expressions as a total function $\mathcal{A}$ that takes two (2) arguments: the syntactic construct and the state: $$\mathcal{A}: \textbf{Aexp} \rightarrow (\textbf{State} \rightarrow \mathbb{Z})$$This means that $\mathcal{A}$ takes its parameters one at a time.
Assuming the existence of the function $\mathcal{N}$ defining the meaning of numerals, we can define the function $\mathcal{A}$ by defining its value $\mathcal{A}[\![a]\!]s$ on each arithmetic expression $a$ and state $s$. 
$$
\begin{aligned}
\mathcal{A}[\![n]\!]s &= \mathcal{N}[\![n]\!] \\ \\ \mathcal{A}[\![x]\!]s &= s\ x \\ \\ \mathcal{A}[\![a_1 + a_2]\!]s &= \mathcal{A}[\![a_1]\!]s + \mathcal{A}[\![a_2]\!]s \\ \\ \mathcal{A}[\![a_1 - a_2]\!]s &= \mathcal{A}[\![a_1]\!]s - \mathcal{A}[\![a_2]\!]s \\ \\ \mathcal{A}[\![a_1 \star a_2]\!]s &= \mathcal{A}[\![a_1]\!]s \star \mathcal{A}[\![a_2]\!]s
\end{aligned}$$We can quickly understand that:
- The clause for $n$ reflects that the value of $n$ in any state is $\mathcal{N}[\![n]\!]$;
- The value of a variable $x$ in state $s$ is the value bound to $x$ in $s$, so $s\ x$;
- The value of the composite expression $a_1 + a_2$ is the sum of the values of $a_1$ and $a_2$ in state $s$;
- The value of the composite expression $a_1 - a_2$ is the difference of the values of $a_1$ and $a_2$ in state $s$;
- The value of the composite expression $a_1 \star a_2$ is the product of the values of $a_1$ and $a_2$ in state $s$;

> [!INFO] Example: Supposing that $s\ x = \textbf{3}$, calculate $\mathcal{A}[\![x+1]\!]s$
> $$
> \begin{aligned}
> \mathcal{A}[\![x+1]\!]s &= \mathcal{A}[\![x]\!]s + \mathcal{A}[\![1]\!]s \\
> &= (s\ x) + \mathcal{N}[\![1]\!] \\
> &= \textbf{3} + \textbf{1} \\
> &= \textbf{4}
> \end{aligned}
> $$

As for boolean expressions, the values are truth values, so in a similar way we can define their meanings by a total function from $\textbf{State}$ to $\textbf{T}$ $$\mathcal{B}: \textbf{Bexp} \rightarrow (\textbf{State} \rightarrow \textbf{T})$$where $\textbf{T}$ consists of the truth values $\textbf{tt}$ for true and $\textbf{ff}$ for false. $$\begin{aligned}
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
\end{aligned}$$
> [!INFO] Examples of Free Variables Sets
> FV(x + 1) = { x }
> FV(x + y * x) = { x, y }
 
In a similar way, we can also define the set FV($b$) of free variables in a boolean expression $b$: $$\begin{aligned}
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
We do also have a notion of substitution or updating for states. We define $\\ s[y \mapsto v]$ to be the state that is like $s$ except that the value bound to $y$ is $v$:$$
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
- $[\text{ass}_\text{sos}]$ says that in a state $s$, $x := a$ is executed to yield a final state $s[x \mapsto \mathcal{A}[\![a]\!]s]$, which is like $s$ except that $x$ has the value $\mathcal{A}[\![a]\!]s$;
- Similarly, $[\text{skip}_\text{sos}]$ says that $\texttt{skip}$ does not change the state;
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
### Properties of the Semantics
For structural operational semantics, it is useful to conduct proofs by induction on the length of the finite derivation sequences considered:
1. We firstly prove that the property holds for all derivation sequences of length equals to 0;
2. Prove that the property holds for all finite derivation sequences:
	- Assuming that the property holds for all derivation sequence of length at most $k$, which will be our *inductive hypothesis*, we show that it holds for derivation sequences of length equals to $k + 1$
#### Lemma 2.19 (Decomposition Lemma)
If $\langle S_1;S_2,\ s\rangle \Rightarrow^k s''$, then there exists a state s' and natural numbers $k_1$ and $k_2$such that $\langle S_1, s\rangle \Rightarrow^{k_1} s'$ and $\langle S_2, s'\rangle \Rightarrow^{k_2} s''$, where $k = k_1 + k_2$.

> [!INFO] Proof of Decomposition Lemma
> The proof is by induction on the number $k$, which is the length of the derivation sequence. 
> Our proposition is that 
> *Base Case*:
> - $k = 0$:  $P(0) := \langle S_1;S_2, s \rangle \Rightarrow^0 s''$ but remember that $\langle S_1;S_2, s \rangle \Rightarrow^0$ would mean that $\langle S_1;S_2, s \rangle$ is a stuck configuration, which is never the case. The premise $\langle S_1;S_2, s \rangle \Rightarrow^0 s''$ is false, and $P(0)$ trivially (or vacuously) holds.
> *Inductive  Step*:
> - The hypothesis is that $$\langle S_1;S_2, s \rangle \Rightarrow^{k + 1} s''$$ so that means that $$\langle S_1; S_2, s \rangle \Rightarrow \langle \gamma, s_0 \rangle \Rightarrow^k s''$$
> - There are 2 rules for deriving the small step $$\langle S_1; S_2, s \rangle \Rightarrow \langle \gamma, s_0 \rangle$$ $[comp_1]$ and $[comp_2]$
> 	- Assume that we applied the rule $[comp_1]$, $\langle S_1; S_2, s\rangle \Rightarrow \langle S_1'; S_2, s_0 \rangle$
> 	- As a consequence, $\langle \gamma, s_0 \Rightarrow $
>
#### Lemma 2.20 (Composition Lemma)
$$\langle S_1, s \rangle \Rightarrow^k s' \rightarrow \langle S_1; S_2, s \rangle \Rightarrow^k \langle S_2, s' \rangle $$
##### Corollary
$$\langle S_1, s \rangle \Rightarrow^* s' \rightarrow \langle S_1; S_2, s \rangle \Rightarrow^* \langle S_2, s' \rangle $$
## The Semantic Function $\mathcal{S}_{sos}$
The meaning of statements can be summarised by a partial function from $\textbf{State}$ to $\textbf{State}$ as follows: $$\mathcal{S}_{sos}: \textbf{Stm} \rightarrow (\textbf{State} \hookrightarrow \textbf{State})$$It is given by $$\mathcal{S}_{sos}[\![S]\!]s = \begin{cases}s' \text{ if  } \langle S, s \rangle \Rightarrow^* s' \\ \uparrow \text{ otherwise}\end{cases}$$And given the fact that the Structural Operational Semantics is deterministic by the given property $$\forall S,s, \gamma, \gamma'.\ \langle S, s\rangle \Rightarrow \gamma \ \wedge \langle S, s \rangle \Rightarrow \gamma' \rightarrow \gamma = \gamma'$$
This function is said to be **well-defined**.

Thanks to these properties and these theorems of Composition and Decomposition we can define the **Statement Semantic Equivalence**: 

$S_1$ and $S_2$ are said to be SOS equivalent if $\forall s$:
- $\langle S_1, s \rangle \Rightarrow^* \gamma$ if and only if $\langle S_2, s \rangle \Rightarrow^* \gamma$, whenever $\gamma$ is a configuration that is either stuck or terminal;
- There is an infinite derivation sequence starting in $\langle S_1, s \rangle$ if an only if there is one starting in $\langle S_2, s \rangle$.
But since we are considering the case where there are no stuck configuration thanks to the deterministic property of the Structural Operational Semantics, the property takes the following form: $$S_1 \triangleq S_2 \iff \forall s, s'. \langle S_1, s \rangle \Rightarrow^* s' \iff \langle S_2, s\rangle \Rightarrow^* s'$$
# More on Operational Semantics 
## Non-Sequential Language Constructs
To illustrate the power and weakness of operational semantics, and in particular structural operational semantics, we shall consider various extensions of the language $\textbf{While}$.
### Abortion
We first extend the $\textbf{While}$ language with the $\texttt{abort}$ statement, which idea is that $\texttt{abort}$ stops the execution of the complete program. This means that $\texttt{abort}$behaves differently from $\texttt{while true do skip}$ in that it causes the execution to stop rather than loop. Differently from $\texttt{skip}$, any statement following $\texttt{abort}$ will never be executed. The new formal syntax is the following: $$S ::= x := a\ | \texttt{ skip }|\ S_1;S_2\ |\text{ if }b \text{ then } S_1 \text{ else } S_2\ | \text{ while } b \text{ do } S \ |\ \texttt{abort}$$The fact that $\texttt{abort}$ stops the execution of the program may be modelled by ensuring that the configurations of the form $\langle \texttt{abort}, s\rangle$ are stuck.

From the structural operational semantics point of view, it is clear that $\texttt{skip}$and $\texttt{abort}$ cannot be semantically equivalent since $$\langle \texttt{skip}, s \rangle \Rightarrow s$$is the only derivation sequence for $\texttt{skip}$ starting in $s$ and $$\langle \texttt{abort}, s\rangle$$is the only derivation sequence for $\texttt{abort}$ starting in $s$.
Similarly, $\texttt{abort}$ cannot be semantically equivalent to $\texttt{while true do skip}$.
### Non-Determinism
The second extension of the $\textbf{While}$ language has statements given by $$S ::= x := a\ | \texttt{ skip }|\ S_1;S_2\ |\texttt{ if }b \texttt{ then } S_1 \texttt{ else } S_2\ | \texttt{ while } b \texttt{ do } S\ |\ S_1 \texttt{ or } S_2$$The idea here is that in $S_1 \texttt{ or } S_2$ we can non-deterministically choose to execute either $S_1$ or $S_2$. When specifying the structural operational semantics, we extend with the following axioms: $$\begin{aligned}&[\text{or}^1_{sos}] &\langle S_1 \texttt{ or } S_2, s\rangle \Rightarrow \langle S_1, s \rangle\\ \\ &[\text{or}^2_{sos}] &\langle S_1 \texttt{ or } S_2, s\rangle \Rightarrow \langle S_2, s \rangle\end{aligned}$$
### Parallelism
The next extension of the $\textbf{While}$ language is a parallel construct. The syntax of expressions is given by $$S ::= x := a\ | \texttt{ skip }|\ S_1;S_2\ |\text{ if }b \text{ then } S_1 \text{ else } S_2\ | \text{ while } b \text{ do } S \ |\ S_1 \texttt{ par } S_2$$The idea is that both statements of $S_1 \texttt{ par } S_2$ have to be executed but that the execution can be interleaved. To express this in the structural operational semantics, we add these rules: $$\begin{aligned} &[\text{par}^1_{sos}] \ \ \ \ \dfrac{\langle S_1, s\rangle \Rightarrow \langle S_1', s' \rangle}{\langle S_1 \texttt{ par } S_2, s\rangle \Rightarrow \langle S_1' \texttt{ par } S_2, s'\rangle} \\ \\ &[\text{par}^2_{sos}] \ \ \ \ \dfrac{\langle S_1, s\rangle \Rightarrow s'}{\langle S_1 \texttt{ par } S_2, s\rangle \Rightarrow \langle S_2, s'\rangle} \\ \\&[\text{par}^3_{sos}] \ \ \ \ \dfrac{\langle S_2, s\rangle \Rightarrow \langle S_2', s' \rangle}{\langle S_1 \texttt{ par } S_2, s\rangle \Rightarrow \langle S_1 \texttt{ par } S_2', s'\rangle} \\ \\ &[\text{par}^1_{sos}] \ \ \ \ \dfrac{\langle S_2, s\rangle \Rightarrow s' }{\langle S_1 \texttt{ par } S_2, s\rangle \Rightarrow \langle S_1 , s'\rangle} \\\end{aligned}$$
# Denotational Semantics
As we have seen in Operational Semantics, and more specifically, in Structural Operational Semantics, we were interested in *how* a program is executed. In Denotational Semantics, we are more focused on the *effect* of executing a program and by that, we mean an association between initial states and final states. The idea to do that is to define a semantic function for each syntactic category, which maps each syntactic construct to a mathematical object that describes the effect of executing that construct.
## Direct Style Denotational Semantics
The effect of executing a statement $S$ is to change the state so we shall define the *meaning* of $S$ to be a partial function on states: $$\mathcal{S}_{ds} : \textbf{Stm} \rightarrow (\textbf{State} \hookrightarrow \textbf{State})$$This is also the same for Structural Operational Semantics and the need for partiality is because of possible non-terminating situations like $\texttt{while true do skip}$. The definition of this semantics is as follows: $$\begin{aligned} \mathcal{S}_{ds}[\![x := a]\!]s &= s[x \mapsto \mathcal{A}[\![a]\!]s] \\ \\ \mathcal{S}_{ds}[\![\texttt{skip}]\!] &= \text{id} \\ \\ \mathcal{S}_{ds}[\![S_1; S_2]\!] &= \mathcal{S}_{ds}[\![S_2]\!]\ \circ\ \mathcal{S}_{ds}[\![S_1]\!] \\ \\ \mathcal{S}_{ds}[\![\text{if } b \text{ then } S_1 \text{ else } S_2]\!] &= \text{cond}(\mathcal{B}[\![b]\!], \mathcal{S}_{ds}[\![S_1]\!], \mathcal{S_{ds}}[\![S_2]\!]) \\ \\ \mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!] &= \text{FIX } F \\ \\ \text{where } F\ g &= \text{cond}(\mathcal{B}[\![b]\!], g \circ \mathcal{S}_{ds}[\![S]\!], \text{id})\end{aligned}$$
For assignment, the clause $$\mathcal{S}_{ds}[\![x := a]\!]s = s[x \mapsto \mathcal{A}[\![a]\!]s]$$ensures that if $\mathcal{S}_{ds}[\![x := a]\!]s = s'$, then $s'\ x = \mathcal{A}[\![a]\!]s$ and $s'\ y = s\ y$ for $y \neq x$.

For the $\texttt{skip}$ operation, no state change takes place, so the function $\text{id}$ is the identity function on $\textbf{State}$ so $\mathcal{S}_{ds}[\![\texttt{skip}]\!] = s$.

For sequencing, the clause is $$\mathcal{S}[\![S_1; S_2]\!] = \mathcal{S}_{ds}[\![S_2]\!]\ \circ\ \mathcal{S}_{ds}[\![S_1]\!]$$So the effect of executing $S_1;S_2$ is the functional composition of the effect of executing $S_1$ and that of executing $S_2$. Functional composition is defined such that if one of the functions is undefined on a given argument, then their composition is undefined as well. Given a state $s$, we have $$\mathcal{S}_{ds}[\![S_1;S_2]\!]s = (\mathcal{S}_{ds}[\![S_2]\!]\ \circ\ \mathcal{S}_{ds}[\![S_1]\!])s \\\\ = \begin{cases}s''\ \text{if there exists } s' \text{ such that } \mathcal{S}_{ds}[\![S_1]\!]s = s' \text{ and }  \mathcal{S}_{ds}[\![S_2]\!]s' = s'' \\ \\ \uparrow \text{  if } \mathcal{S}_{ds}[\![S_1]\!]s\uparrow \text{ or } \text{if there exists } s' \text{ such that } \mathcal{S}_{ds}[\![S_1]\!]s = s' \text{ but } \mathcal{S}_{ds}[\![S_2]\!]s\uparrow\end{cases}$$It therefore follows that the sequencing construct will only give a defined result if both components do.

For conditional, the clause is $$\mathcal{S}_ds[\![\text{if } b \text{ then } S_1 \text{ else } S_2]\!] = \text{cond}(\mathcal{B}[\![b]\!], \mathcal{S}_{ds}[\![S_1]\!], \mathcal{S_{ds}}[\![S_2]\!])$$and the auxiliary function $\text{cond}$ has functionality $$\text{cond}: (\textbf{State} \rightarrow \text{T}) \times (\textbf{State} \hookrightarrow \textbf{State}) \times (\textbf{State} \hookrightarrow \textbf{State})$$and is defined by $$\text{cond}(p, g_1, g_2) = \begin{cases} g_1\ s \text{ if } p\ s = \textbf{tt} \\  g_2\ s \text{ if } p\ s = \textbf{ff}\end{cases}$$The first parameter to $\text{cond}$ is a function that, when supplied with an argument, will select either the second or the third parameter of $\text{cond}$ and then supply that parameter with the same argument. In that sense, we have $$\mathcal{S}_{ds}[\![\text{if } b \text{ then } S_1 \text{ else } S_2]\!]s = \text{cond}(\mathcal{B}[\![b]\!], \mathcal{S}_{ds}[\![S_1]\!], \mathcal{S_{ds}}[\![S_2]\!])s \\ \\ = \begin{cases}s' \text{ if } \mathcal{B}[\![b]\!]s = \textbf{tt} \text{ and }\mathcal{S}_{ds}[\![S_1]\!]s = s' \text{ or } \text{ if } \mathcal{B}[\![b]\!]s = \textbf{ff} \text{ and }\mathcal{S}_{ds}[\![S_2]\!]s = s' \\ \\ \uparrow \text{ if } \mathcal{B}[\![b]\!]s = \textbf{tt} \text{ and }\mathcal{S}_{ds}[\![S_1]\!]s\uparrow \text{ or } \text{ if } \mathcal{B}[\![b]\!]s = \textbf{ff} \text{ and }\mathcal{S}_{ds}[\![S_2]\!]\uparrow\end{cases}$$So if the selected branch gives a defined result then so does the conditional.

Defining the effect of $\texttt{while } b \texttt{ do } S$ is a major task and to motivate the actual definition, we first observe that the effect of $$\texttt{while } b \texttt{ do } S$$ must be equal to $$\texttt{if } b \texttt{ then } (S; \texttt{while } b \texttt{ do } S) \texttt{ else skip}$$Using the parts of $\mathcal{S}_{ds}$ that we have already defined, this gives $$\mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!] = \text{cond}(\mathcal{B}[\![b]\!], \mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!] \circ \mathcal{S}_{ds}[\![S]\!], \text{id})$$But we cannot use this as a definition since it wouldn't be a compositional definition as requested. What we can instead do is to express that $$\mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!]$$must be a *fixed point* of the functional $F$ defined by $$F\ g = \text{cond}(\mathcal{B}[\![b]\!], g\ \circ\ \mathcal{S}_{ds}[\![S]\!], \text{id})$$that is $\mathcal{S}_{ds}[\![\texttt{while } b \texttt{ do } S = F\ (\mathcal{S}_{ds}[\![\texttt{while } b \texttt{ do } S)$. By doing that, we get a compositional definition of $\mathcal{S}_{ds}$ because when defining $F$ we only apply $\mathcal{S}_{ds}$ to the immediate constituents of $\texttt{while } b \texttt{ do } S$ and not to the construct itself.
We can therefore write $$\begin{aligned} &\mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!] = \text{FIX } F \\ \\ &\text{where } F\ g = \text{cond}(\mathcal{B}[\![b]\!], g \circ \mathcal{S}_{ds}[\![S]\!], \text{id})\end{aligned}$$to indicate that $\mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!]$ is a fixed point of $F$. The functionality of the auxiliary function $\text{FIX}$ is $$\text{FIX}: ((\textbf{State} \hookrightarrow \textbf{State}) \rightarrow (\textbf{State} \hookrightarrow \textbf{State})) \rightarrow (\textbf{State} \hookrightarrow \textbf{State})$$
>[!INFO] Example $\texttt{while } \neg(x = 0) \texttt{ do skip}$ (Functional with more than one fixed point)
> We can verify that the corresponding functional $F$' is defined by $$(F'\ g)\ s = \begin{cases}\begin{aligned}&g\ s &\text{ if } s\ x \neq \textbf{0} \\ \\ &s &\text{ if } s\ x =\textbf{0}\end{aligned}\end{cases}$$Then, the function $g_1$ defined by $$g_1\ s = \begin{cases}\begin{aligned} &\uparrow &\text{ if } s\ x \neq \textbf{0} \\ \\ &s &\text{ if } s\ x =\textbf{0}\end{aligned}\end{cases}$$is a fixed point of $F$' because $$(F'\ g)\ s = \begin{cases}\begin{aligned}&g_1\ s &\text{ if } s\ x \neq \textbf{0} \\ \\ &s &\text{ if } s\ x =\textbf{0}\end{aligned}\end{cases} = \begin{cases}\begin{aligned}&\uparrow &\text{ if } s\ x \neq \textbf{0} \\ \\ &s &\text{ if } s\ x =\textbf{0}\end{aligned}\end{cases} = g_1\ s$$Next, we claim that the function $g_2$ defined by $$g_2\ s \uparrow \forall s$$cannot be a fixed point for $F$'. The reason is that if $s$' is a state with $s' \text{x} = \textbf{0}$, then $(F'\ g_2) s' = s'$, whereas $g_2\ s'\uparrow$.

Unfortunately, this is insufficient for defining $\mathcal{S}_{ds}[\![\text{while } b \text{ do } S]\!]$ because we have two major problems to deal with:
- There are functionals that have more than one fixed point;
- There are functionals that have no fixed point at all
Considering the previous example, the functional $F$' has more than one fixed point. Moreover, every function $g$' of $\textbf{State} \hookrightarrow \textbf{State}$ that satisfies $g'\ s = s \text{ if } s \text{ x} = 0$ will be a fixed point of $F$'.

>[!INFO] Example of a functional with no fixed points
>$$F_1\ g = \begin{cases}g_1 \text{ if } g = g_2 \\ \\ g_2 \text{ otherwise}\end{cases}$$If $g_1 \neq g_2$, then there will be no function $g_0$ such that $F_1\ g_0 = g_0$. Thus, $F_1$ has no fixed point at all.

## Fixed Point Theory
In order to resolve the two problems stated before, we will develop a framework where:
- We impose requirements on the fixed points and show that there is at most one fixed point fulfilling these requirements;
- All functionals originating from statements in $\textbf{While}$ do have a fixed point that satisfies there requirements.
To prepare for a framework that guarantees the existence of the desired fixed point $\text{FIX } F$, we shall reformulate the requirements to $\text{FIX } F$ in a more formal way. 
First of all, we formalise the requirement that $\text{FIX } F$ shares its result with all other fixed points. To do so, we define an *ordering* on partial functions of $\textbf{State} \hookrightarrow \textbf{State}$, by setting $$g_1 \sqsubseteq g_2$$when the partial function $g_1: \text{State} \hookrightarrow \text{State}$ shares its results with the partial function $g_2: : \text{State} \hookrightarrow \text{State}$ in the sense that $$\text{if } g_1\ s = s' \text{ then } g_2\ s = s'$$for all choices of $s$ and $s'$.
> ![INFO] Example of Partial Function Ordering
> Let $g_1$, $g_2$, $g_3$ and $g_4$ be partial functions in $\textbf{State} \hookrightarrow \textbf{State}$ defined as follows: $$\begin{aligned}&g_1\ s = s \text{ for all } s \\ \\ &g_2\ s = \begin{cases}s \text{ if } s \text{ x} \geq \textbf{0} \\ \\\uparrow \text{ otherwise}\end{cases} \\ \\ &g_3\ s = \begin{cases}s \text{ if } s \text{ x} = \textbf{0} \\ \\\uparrow \text{ otherwise}\end{cases} \\ \\ &g_4\ s = \begin{cases}s \text{ if } s \text{ x} \leq \textbf{0} \\ \\\uparrow \text{ otherwise}\end{cases}\end{aligned}$$Then we have $$\begin{aligned} &g_1 \sqsubseteq g_1, \\ &g_2 \sqsubseteq g_1, g_2 \sqsubseteq g_2, \\ &g_3 \sqsubseteq g_1, g_3 \sqsubseteq g_2, g_3 \sqsubseteq g_3, g_3 \sqsubseteq g_4 \\ &g_4 \sqsubseteq g_1, g_4 \sqsubseteq g_4\end{aligned}$$Note that it is neither the case that $g_2 \sqsubseteq g_4$ nor that $g_4 \sqsubseteq g_2$ because they do share the case $\text{x} = \textbf{0}$, but it should be for every case, which is not.