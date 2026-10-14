A reactive component interacts with the environment via inputs and outputs. A requirement for a component is a specification of acceptable and desired sequences of outputs in response to inputs. Requirements, which are desirable property of the executions of the system, can be classified in two categories:
- *Safety Requirements* which assert that "nothing bad ever happens";
- *Liveness Requirements* which assert that "something good eventually happens";
For safety requirements, violation of a requirement can be demonstrated by a finite execution that illustrates the undesirable behaviour and such requirements are captured by composing the system with a *monitor* that observes the inputs and outputs of the system and enters an *error* state if an undesirable behaviour is detected.
# Safety Specifications
## Invariants of Transition Systems
A safety requirement for a system classifies its states into *safe* and *unsafe* and asserts that an unsafe state is never encountered during an execution of the system.
### Transition Systems
A **Transition System** is specified using variables whose valuations describe possible states of the system. The initialisation describes the initial values for each of the system variables and transitions of the system describe how the state evolves and are typically specified using a sequence of assignments and conditional statements that update the state variables.
As seen for Synchronous Reactive Components, we use $Init$ to denote the syntactic description of the initialisation, with an associated semantics $[\![Init]\!]$ denoting the corresponding set of initial states. We then use $Trans$ to denote the syntactic description of the transitions, with an associated semantics $[\![Trans]\!]$ is a set of pairs of states.

>[!SUCCESS] Transition System
>A transition system $T$ has:
>- A finite set $S$ of typed state variables defining the set $Q_S$ of states;
>- An initialisation $Init$ defining the set of $[\![Init]\!] \subseteq Q$ of initial states;
>- A transition description $Trans$ defining the set $[\![Init]\!] \subseteq Q_S \times Q_S$ of transitions between states

With each synchronous reactive component $C = (I, O, S, Init, React)$, there is a naturally associated transition system: the set of state variables is $S$, the initialisation is $Init$ and the transition description $Trans$ is obtained from the reaction description $React$ by declaring the input and output variables to be local variables, where the input variables are assigned nondeterministically chosen values. Consequently, the set of transitions contains pairs of states $(s, t)$ such that $s \rightarrow^{i/o} t$ is a reaction for some input $i$and some output $o$.
### Reachable States
An execution of a transition system starts in an initial state and proceeds by following the transitions specified by $Trans$. States encountered during executions are reachable states of the system.

>[!SUCCESS] Reachable States of Transition System
>An execution of a transition system $T$ consists of a finite sequence of the form $s_0,\ s_1,\ ...,\ s_k$ such that:
>1. For $0 \leq j \leq k$, each $s_j$ is a state of $T$;
>2. $s_0$ is an initial state of $T$;
>3. For $1 \leq j \leq k$, $(s_{j-1},\ s_j)$ is a transition of $T$
>For such execution, the state $s_k$ is said to be a reachable state of $T$
### Invariants
For a transition system $T$, a property is a boolean-valued expression over the state variables of $T$. A state $q$ of $T$ satisfies the property $\phi$ if $\phi$ evaluates to 1 when all variables are assigned according to the valuation $q$. The set of all states that satisfy the property $\phi$ is denoted $[\![\phi]\!]$.

A property is said to be an invariant of the transition system if all the reachable states of the system satisfy the property.

> [!SUCCESS] Invariant of a Transition System
> For a transition system $T$, a property $\phi$ of $T$ is an invariant of $T$ if every reachable state of $T$ satisfies $\phi$.
> 
> A property $\phi$ of a transition system $T$ is an invariant if and only if the negation property $\neg \phi$ is not reachable.
## Role of Requirements in System Design
To illustrate the use of invariants as safety requirements in the design of embedded controllers, let's consider a system of traffic lights for a railroad.

> [!INFO] Nondeterministic Reactive Component $\texttt{Train}$
> ![[Screenshot 2026-10-12 at 15.19.16.png]]
## Safety Monitors

>[!SUCCESS] Safety Monitor
>A $\textit{safety monitor}$ for a reactive component $C$ with input variables $I$ and output variables $O$ consists of a synchronous reactive component $M$ such that:
>1. The set of input variables of $M$ is a subset of the variables $I \cup O$;
>2. The set of output variables of $M$ is disjoint from the variables $I \cup O$;
>3. The reaction description of $M$ is given as an extended-state machine, along with a subset $F$ of the modes declared as accepting.
>The component $C$ satisfies the monitor specification if the property $M.mode \notin F$ is an invariant of the composed system $C || M$.

The requirement that the input variables of the monitor $M$ are the input/output variables of the component $C$ means that $M$ can "observe" the behaviour of $C$ in terms of its interaction with other components. 
The requirement that the output variables of $M$ are neither the inputs nor the outputs of $C$ ensures that the behaviour of $C$ is not modified by the monitor $M$ and that is compatible with $M$.
# Verifying Invariants


