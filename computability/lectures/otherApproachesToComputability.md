# Partial Recursive Functions
From the Church-Turing Thesis, we know that *A function is computable by an effective procedure if and only if it is computable by a [Your model of computation]-machine*.

And in our case, the Thesis is rewritten as *A function is computable by an effective procedure if and only if it is computable by a URM-machine*.

We define the class $\mathcal{R}$ of partial recursive functions and we prove that $\mathcal{R} = \mathcal{C}$.

> [!SUCCESS] Definition of Partial Recursive Functions Class
> The class of *partial recursive functions* $\mathcal{R}$ is the "least class" of function which:
> - Contains the basic functions of zero, successor and projection;
> - Is closed by composition, primitive recursion and (unbounded) minimalisation
> Namely, we define a class of function $\mathcal{A} \subseteq \mathcal{F}$ *rich* if:
> - Contains the basic functions of zero, successor and projection;
> - Is closed by composition, primitive recursion and (unbounded) minimalisation
> This means that $\mathcal{R}$ is such that:
> -  $\mathcal{R}$ is rich;
> - $\forall\ \mathcal{A} \subseteq \mathcal{F}$ rich, $\mathcal{R} \subseteq \mathcal{A}$
> Note that:
> - $\mathcal{F}$ is rich;
> - if $\mathcal{A}_i\ i \in I\ \mathcal{A}_i$ rich, then $\underset{i \in I}{\cap}\mathcal{A_i}$ is rich
> - $\mathcal{R} = \underset{\mathcal{A} \text{ rich}}{\cap} \mathcal{A}$

Therefore, equivalently, $\mathcal{R}$ is the class of functions which can be obtained from zero, successor, projection using composition, primitive recursion and minimalisation a finite number of times.

> [!INFO] Theorem $\mathcal{C} = \mathcal {R}$
> - $\mathcal{R} \subseteq \mathcal{C}$: $\mathcal{C}$ is rich, $\mathcal{R}$ is the least rich class $\rightarrow$ $\mathcal{R} \subseteq \mathcal{C}$;
> - $\mathcal{C} \subseteq \mathcal{R}$: let $f \in \mathcal{C}. f: \mathbb{N}^k \rightarrow \mathbb{N}$
> There is a URM-Program $P$ for $f$ such that $$\begin{aligned}&c(\overset{\rightarrow}{x}, t) = \begin{cases} \text{contents of } R_1 \text{ after } t \text{ steps in the computation } P(x) \text{, if } P(x) \text{ hasn't \\already stopped} \\ \\ \text{the final contents of } R_1 \text{ if } P(x) \text{ has stopped after \\fewer than } t \text{ steps}\end{cases} \\ \\ &j(\overset{\rightarrow}{x}, t) = \begin{cases}\text{number of the next instruction, when } t \text{ steps of the \\computation } P(x) \text{ have been performed } \\\\ 0 \text{ if } P(x) \text{ bas stopped after } t \text{ steps or fewer }\end{cases}\end{aligned}$$
> $j$ and $c$ are clearly total functions. 
> 	- If $f(\overset{\rightarrow}{x})\downarrow$, then $P(\overset{\rightarrow}{x})\downarrow$ after $\mu t.j_p(\overset{\rightarrow}{x}, t)$ steps;
> 	- If $f(\overset{\rightarrow}{x},t)\uparrow$ , then also $P(\overset{\rightarrow}{x})$ diverges. So $\mu t.j_P(\overset{\rightarrow}{x}, t)\uparrow$
> 
> 	We see that $f(\overset{\rightarrow}{x}) = c(\overset{\rightarrow}{x}, \mu t.j_P(\overset{\rightarrow}{x}, t))\ \forall \overset{\rightarrow}{x}$
