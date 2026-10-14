A $\textbf{functional}$ component produces output when supplied with inputs, and its behaviour can be mathematically described using a mapping between inputs and output values. On the other hand, a $\textbf{reactive}$ component maintains an internal state and interacts with other components via inputs and outputs in an ongoing manner. We will firstly focus on a discrete and synchronous model of reactive computation in which all components execute in a sequence of rounds. In each round, a reactive component reads its input and, based on its current state and inputs, computes outputs and updates its internal state.
# Reactive Components

> [!INFO] Reactive Component $\texttt{Delay}$ Example
> ![[SRC_Delay_Component.png]]
## Variables, Valuations and Expressions
To explain the aspects of the definition of a component precisely, we use **typed** variables. The most commonly used types are:
- $\texttt{nat}$ to denote the set of natural numbers;
- $\texttt{int}$ to denote the set of integer numbers;
- $\texttt{real}$ to denote the set of real numbers;
- $\texttt{bool}$ to denote set of boolean values;
- An enumerated type containing a finite number of symbolic constants
Given a set $V$ of typed variables, we define a **valuation** over $V$ as a type-consistent assignment to all the variables in $V$. We use $Q_V$ to denote the set of all valuations over $V$.
A typed expression $e$ over a set $V$ of typed variables is constructed by using variables in $V$, constants, and primitive operations over types corresponding to there variables. As for numerical types like $\texttt{nat}$, $\texttt{int}$ and $\texttt{real}$ we will se arithmetic operations such addition, division, multiplication and subtraction and comparison operators like $=$ and $\leq$. For booleans:
- negation ($\neg$);
- conjunction ($\wedge$);
- disjunction ($\lor$);
- implication ($\rightarrow$)
## Inputs, Outputs and States
Given a Component $C$, we define the set $I$ of typed input variables, the set $O$ of typed output variables and the set $S$ of typed state variables, which all should be finite and disjoint from one another.

> [!INFO] Reactive Component $\texttt{Delay}$ Example
> For the $\texttt{Delay}$ component, $I = {in}$, $O = {out}$, $S = {x}$.

To represent a component, we draw rectangular boxes. For each input, there is an incoming arrow upon this box and for each output there is an outgoing arrow. These arrows are labeled with the names and types of the corresponding variables. The state variables are listed inside the component box.
- An input in a reactive component $C$ is a valuation over the set $I$ of its input variables and the set of all possible inputs is $Q_I$. 
- An output of a reactive component $C$ is a valuation over the set $O$ of its output variables and the set of all possible outputs is $Q_O$.
- A state of a reactive component $C$ is a valuation over the set $S$ of its state variables and the set of all possible states is $Q_S$.

> [!INFO] Reactive Component $\texttt{Delay}$ Example
> For the $\texttt{Delay}$ component, an input is a boolean value for the variable $in$, an output is a boolean value for the variable $out$ and a state is a boolean value for the variable $x$. Each of the sets $Q_I$, $Q_O$, $Q_S$ contains the two elements {0, 1}.
## Initialisation
To describe the dynamics of the component, we must specify the initial states and how the component reacts to a given input in each state. The initialisation of a component, denoted $\textit{Init}$, specifies the initial values for all the state variables in $S$. Whenever a state variable is defined, the corresponding initial values are described using an assignment. Sometimes we want to specify multiple possible initial values to allow modeling of situations where initial conditions are only partially known. For this, we will use the $\texttt{choose}$ construct which returns an arbitrarly chosen value from its argument set.

> [!INFO] Reactive Component $\texttt{Delay}$ Example
> For the $\texttt{Delay}$ component, consider an alterative declaration for the variable $x$ given by $\texttt{bool } x := \texttt{choose } \{0,\ 1\}$.

A state $q$ of the reactive component $C$ is called **initial state** if, for every state variable $x$, the value $q(x)$ is consistent with the initialisation of the variable $x$ and the set of all initial states is denoted $[\![Init]\!]$.

> [!INFO] Reactive Component $\texttt{Delay}$ Example
> For the $\texttt{Delay}$ component, the set $[\![Init]\!]$ contains the single initial state that assigns the value $0$ to $x$.
## Update
The computation of a component in response to an input in each round is given by its reaction description, denoted as $React$. If the component in state $s$, when supplied with input $i$, can produce output $o$ and update its state to $t$, we write a **reaction** in the following form $$s \rightarrow^{i/o} t$$
> [!INFO] Reactive Component $\texttt{Delay}$ Example
> For the $\texttt{Delay}$ component, the reaction description $React$ is a sequence of two assignments: $$out:= x;\ x := in$$That is, in a state $s$, given an input $i$, the component copies the state of the output and updates the state to the current input. The component has therefore 4 possible reactions: $$0 \rightarrow^{0/0}0;\ 0 \rightarrow^{1/0}1;\ 1 \rightarrow^{0/1}0;\ 1 \rightarrow^{1/1}1$$

The set of all possible reactions of the component is the semantics of the reaction description and is denoted as $[\![React]\!]$. The reaction description is a sequence of statements, where each statement is either an assignment or a conditional. 
- The assignment is of the form $x:=e$, where $e$ is an expression of the same type of $x$
- The conditional is of the form $\text{if } b \text{ then } statement_1 \text{ else } statement_2$, where $b$ is a boolean expression and $statement_1$ and $statement_2$ are code fragments given as sequences of assignment and conditional
Given a state $s$ and an input $i$, to find the possible reactions of the component, we execute the reaction description code: if we can execute it without any error and if the values assigned to all the output variables give the output valuation $o$, while the updated values of all the state variables give the state valuation $t$, then this contributes the reaction.
There are, however, special cases in which an execution is not possible:
- When the code tries to execute an assignment $x:=e$, we expect $x$ to be either an output variable or a state variable; an attempt to assign a value to an input or an undeclared variable is an error. To be able to evaluate the expression $e$, it should refer only to state variables, input variables, and those output variables assigned values by previously executed assignment statements.
- If the code does not assign values to all output variables, then this execution cannot define a valid reaction
The reaction description code can also use local variables which are auxiliary variables used to store results of intermediate computations.

>[!SUCCESS] Synchronous Reactive Component
>A **Synchronous Reactive Component (SRC) C** is described by:
>- A finite set $I$ of typed input variables defining the set $Q_I$ of inputs;
>- A finite set $O$ of typed output variables defining the set $Q_O$ of outputs;
>- A finite set $S$ of typed state variables defining the set $Q_S$ of states;
>- An initialisation $Init$ defining the set $[\![Init]\!] \subseteq Q_S$ of initial states;
>- A reaction description $React$ defining the set $[\![React]\!]$ of reactions of the form $s \rightarrow^{i/o} t$, where $s$, $t$ are states, $i$ is an input and $o$ is an output
## Executions
The operational semantics of a component can be captured by defining its executions. First, we initialise all the variables to obtain an initial valid state; then, the execution proceeds for a finite number of rounds in which value for input variables are chosen. The code in the reaction description is then executed to determine its output and update its state.
Formally, an execution of an SRC $C$ of length $k$, where $k \geq 0$, consists of a finite sequence of the form $$s_0 \rightarrow^{i_1/o_1} s_1 \rightarrow^{i_2/o_2} s_2 \rightarrow^{i_3/o_3} s_3\ ...\ s_{k-1} \rightarrow^{i_k/o_k} s_k$$
## Extended-State Machines

> [!INFO] Extended-State Machine $\texttt{Switch}$ Example
> ![[Screenshot 2026-10-12 at 15.08.22.png]]

State machine are used for describing the behaviour in model-based design.
In the state-machine notation, there is an implicit state variable called $mode$ of the state machine, which ranges over an enumerated type. 

> [!INFO] Extended-State Machine $\texttt{Switch}$ Example
> In Extended-State Machine $\texttt{Switch}$, $\texttt{mode}$ ranges over the enumerated type $\{\texttt{off }, \texttt{on}\}$

The description using the modes of the machine is augmented with additional state variables and the initialisation arrow into the initial mode is labeled with the declaration of these additional state variables along with their initial values. 

> [!INFO] Extended-State Machine $\texttt{Switch}$ Example
> In Extended-State Machine $\texttt{Switch}$, there is a single additional state variable $x$ of type $\texttt{int }$, and it is initialised to the value $0$.

The reactions are specified using node-switches, which are depicted as an edge between two modes and has an associated boolean guard condition and a code fragment to update variables:
- If the guard-condition is the expression $Guard$ and the code to update variables is $Update$, then the edge is annotated with $Guard \rightarrow Update$;
- If the guard-condition always holds, then it is omitted and the edge is annotated only with the code $Update$;
- If the update code doesn't modify any variables, then it is omitted and the edge is annotated only with the guard-condition $Guard?$
# Properties of Components
### Finite-State Components
In many embedded applications, it suffices to consider types with only finitely many values: the type bool and enumerated types are finitely values, whereas the numerical types such as $\texttt{nat}$, $\texttt{int}$ and $\texttt{real}$ are not finite. When all the variables of a component have finite types, the set $Q_I$ of inputs, the set $Q_O$ of outputs and set $Q_S$ of states are all finite.

>[!SUCCESS] Finite-State Component
>A synchronous reactive component $C$ is said to be **finite-state** if the type of each of its input, output and state variables is finite
## Event-Triggered Components
In the synchronous execution model of reactive components, the notion of a round is global and each component participates in every round. This may not be realistic in some scenarios and we want to allow the possibility of a component to specify its own notion of a round. To model this behaviour, we use a new input variable of type $\texttt{event}$, which is the enumerated type {$\top,\ \bot$}, where $\top$ denotes that the event is present and $\bot$ that it is absent. We also allow the event type to be parametrised by another type so that the event can be either present or absent with a value belonging to the parameter type:
- $\texttt{event(bool)}$ has three possible values: { $\bot$, 0, 1 };
For an event variable $x$, the boolean expression $x?$, meaning $x$ is present, stands for the expression $x \neq \bot$. While input, output and local variables can be of type $\texttt{event}$, a state variable cannot be of this type. 
By default, an output event variable is absent, if the output variable is not explicitly assigned a value during a round.
For a synchronous reactive component $C$, an output variable $y$ is said to be latched if there exists a state variable $x$ such that in every reaction $s \rightarrow^{i/o} t$ of the component, the value of the output variable $y$ is the updated value of the state variable $x$: $o(y) = t(x)$.

> [!SUCCESS] Event-Triggered Component
> For a synchronous reactive component $C = (I, O, S, Init, React)$, a set $J \subseteq I$ of input variables is said to be a **trigger** if:
> 	- Every input variable in $J$ is of type $\texttt{event}$;
> 	- Every output variable is either latched or is of type $\texttt{event}$;
> 	- If $i$ is an input with all events in $J$ absent, then for all states $s$, if $s \rightarrow^{i/o} t$ is a reaction, then $s = t$ and $o(y) = \bot$ for every output variable $y$ of type $\texttt{event}$
> A component $C$ is said to be **event-triggered** if there exists a subset $J \subseteq I$ of its input variables such that $J$ is a trigger of $C$.

## Deterministic and Non-Deterministic Components
For a given sequence of inputs, the component is **deterministic** if it has a unique execution producing a unique sequence of outputs. Such behaviour is ensured if the component has a single initial state and, in every state, for a given input, there is exactly one possible reaction.

> [!SUCCESS] Deterministic Component
> A synchronous reactive component $C$ is said to be **deterministic** if:
> 1. $C$ has a single initial state;
> 2. For every state $s$ and every input $i$, there is precisely one output $o$ and one state $t$ such that $s \rightarrow^{i/o} t$ is a reaction of $C$

In contrast, nondeterministic components can respond with different output sequences of the same input sequence. Such components are useful for modeling parts of the system that are not fully designed for capturing constraints on the environment.

## Input-Enabled Components
All the components we have seen so far have a common property: in every state and for every input, the component has at least one reaction. This property makes the components input-enabled. For a given sequence of inputs, an input enabled component has at least one corresponding execution.

> [!SUCCESS] Input-Enabled Component
> For a synchronous reactive component $C$, an input $i$ is said to be **enabled** in a state $s$ if there exists an output $o$ and a state $t$ such that $s \rightarrow^{i/o} t$ is a reaction of $C$. 
> The component $C$ is said to be **input-enabled** if every input is enabled in every state.

### Task Graphs and Await Dependencies
Tasks are essentially atomic blocks of code which specifies the variables that reads and writes. To represent the order of execution of tasks, we use a **Task Graph**, where vertices are the tasks and edges denote the precedence, which is represented with the $\prec$ symbol. Everything of the left side is executed BEFORE everything on the right one. Graph have to be acyclic in order to avoid race conditions. 
For a synchronous reactive component *C*, with input vars *I*, output vars *O*, state vars *S* and local vars *L*, reaction description is given by a set of tasks, and precedence edges $\prec$ over these tasks, each task is specified by:
- The read-set *R* must be a subset of *I* $\cup$ S $\cup$ *O* $\cup$ *L*
- The write-set *W* must be a subset of S $\cup$ *O* $\cup$ *L*
- The update code to write vars in W based on values of vars in *R* and $[\![Update] \!]$ is a subset of $Q_R \times Q_W$ 
It's important to state that the Task Graphs have specific requirements that must be met:
- **The precedence relation $\prec$ must be acyclic:**
	- Given $A_1, A_2, ..., A_n$ tasks, if $A' \prec A$ then $A'$ must appear before $A$ in the ordering;
	- $A' \prec^+ A$ means that there is a path from task $A'$ to task $A$ in the task graph using precedences edges. This notation also means that $A'$ must appear before $A$ in every schedule;
	- **The acyclicity of a task graph is met if there at least one task schedule without any cycle**;
- **Each output variable is in the write-set of exactly one task**:
	- If output `y` is in the write set of task $A$, then as soon as $A$ executes the output `y` is available to the rest of the system;
	- If task $A$ writes output `y`, then `y` awaits an input variable `x`, denoted `y` $\succ$ `x` if either the task $A$ reads `x` or another task $A'$ reads `x` that $A' \prec^+ A$. Note that `y` cannot produced before `x` is supplied if it await `x`;
- **Output and local variables are written before being read**:
	- If an output variable or a local variable `y` is in the read-set of a task $A$, then `y` must be in write-set of some task $A'$ such that $A' \prec^+ A$
- **Tasks with a write conflict must be ordered**:
	- Write conflict between tasks $A$ and $A'$ occurs when there exists a variable that is written by $A$ and is either read or written by $A'$. If those tasks have a write-conflict, then the result depends on whether $A$ executes before or after $A'$ 

## Composing Components
### Block Diagrams
Composition is a particular technique used in MBD that allows us to structure and build complex models starting from simpler ones.
To do so, we need 3 main operations that allow us to combine components in order to create a higher order component.
These are:
	- **Instantiation (or Renaming)**: trivially, to make 2 components compose together we need to instantiate them, meaning that we need to build a model of our component in order to work with it. If we, hypothetically, need to have more copies of the same instance, we need to do also a **renaming** operation, in which we change the state variables' names in order to avoid naming clashes. This is mandatory because if we had 2 different components with the same state variable name, our system wouldn't know exactly which one to use;
	- **Parallel Composition**: it is represented by the `||` symbol. In this operation, we combine our components so they run concurrently reacting to the same logical clock ticks in the synchronous model. In a parallel composition, if Component A outputs a value that Component B takes in input, the composition operation binds those variables together.
	- **Encapsulation**: once two components are wired together, the signal passing between them is an internal matter of the composition and encapsulation prevents the rest of the system or the environment itself from messing with those variables. In this way, we can guarantee to have information hiding and modularity, ensuring that external components can only interact with the system through its public inputs and outputs.
### I/O Variable Renaming
Before composing an connecting our components, we may need to do a renaming operation so that there are no name conflicts among state variables of different components.
Let $C = (I, O, S, Init, React)$ be a synchronous reactive components, $x$ be an I/O variable and $y$ be a variable that is not a state, an input nor an output of $C$ such that the types of $x$ and $y$ are the same. The component obtained by renaming $x$ to $y$ in $C$, denoted as $C[x \mapsto y]$ is the synchronous reactive component obtained substituting the variable name $x$ by $y$.
### Parallel Composition
The parallel composition operation combines two components into a single component whose behaviour capture the synchronous interaction between the components running concurrently.
#### Compatibility in Variable Names
Let's consider $C_1 = (I_1, O_1, S_1, Init_1, React_1)$ and $C_2 = (I_2, O_2, S_2, Init_2, React_2)$.
Before we can compose these two, we need to check for compatibility in their variable declarations. 
- There should be no name conflicts concerning the state variable: if $x$ is a state variable of $C_1$, then no variable of $C_2$ should be called $x$. Such said, $S_1 \cap I_2/ \cap O_2/ \cap S_2 = \emptyset$;
- A variable can be an input variable to both the components and an output variable of one component can be an input variable to the other, but a variable cannot be an output of both the components, so only one component is responsible for controlling the value of any given variable. We can say it also $O_1 \cap O_2 = \emptyset$;
#### Product Variables
When we have $C_1$ and $C_2$ compatible, we want to define their parallel composition, denoted as $C_1 || C_2$ to be an another synchronous reactive component $C$. We define $C$ as the *synchronous product* of $C_1$ and $C_2$. 

Each state variable of a component is a state variable of the product. That is, the set $S$ of state variables of $C$ is the union $S_1 \cup S_2$.
Each output variable of a component is an output variable of the product. That is, the set $O$ of output variables of $C$ is the union $O_1 \cup O_2$.
Each input variable is an input variable of the product, provided it is not an output variable of the other component. That is, the set $I$ of input variables of $C$ is the set $(I_1 \cup I_2) \ O$
#### Product States
A state of the product $C$ assigns values to variables in $S_1$ as well as in $S_2$. The initial states of $C$ are obtained by choosing the values for variables in $S_1$, according to the initialisation $Init_1$ of the component $C_1$ and choosing the values for variables in $S_2$, according to the initialisation $Init_2$ of the component $C_2$.

If the two initialisation are given as sequences of assignment statements, the the initialisation $Init$ for the product can be defined as $Init_1;Init_2$ or viceversa.
#### Reaction Description of the Product
If the reaction descriptions of the two components $C_1$ and $C_2$ use local variables $L_1$ and $L_2$, then the set of local variables for the reaction description of the product is denoted by $L_1 \cup L_2$.

When outputs of a component are inputs to the other, we have to put some order constraints on how component should execute within a round, depicted by the task graph.

A special case of this product is the situation in which there is no communication between the components, then the components can be executed independently. 
#### Composing Task Graphs

#### Acyclicity of Await Dependencies
The cross-component precedence edges can leat to a cycle among precedence constraints. This problem can be traced to cycles in the input/output await dependencies. 

**The absence of mutual await dependency is a key for well-formed behaviour of the product**. 

> [!SUCCESS] Component Compatibility
> The component $C_1$ with input variables $I_1$, output variables $O_1$ and input-output await dependency relation $\succ_1 \subseteq O_1 \times I_1$, and $C_2$ with input variables $I_2$, output variables $O_2$ and input-output await dependency relation $\succ_2 \subseteq O_2 \times I_2$, are said to be compatible if:
> - The sets $O_1$ and $O_2$ are disjoint;
> - The relation $\succ_1 \cup \succ_2$ is acyclic
#### Interfaces
We can think of the inputs $I$, the outputs $O$ and await-dependency relation $\succ \subseteq O \times I$ as an *interface* for the component. To form block diagrams of multiple components, we can focus only on the interfaces of components to ensure compatibility and consistent usage, without knowing the internal details.

To sum everything up,
> [!SUCCESS] Component Composition
> 
#### Properties of Parallel Composition
Let $C_1$ and $C_2$ be compatible components. By the definition above, the product $C_1 || C_2$ is the same as the product $C_2 || C_1$. Thus, the parallel composition operation is *commutative* and also *associative*

### Output Hiding
If $y$ is an output variable of a component $C$, then the result of hiding $y$ in $C$denoted $C \ y$, gives a component that behaves the same way as the component $C$, but $y$ is no longer an output that is observable outside. This is achieved by removing $y$ from the set of output variables and declaring $y$ to be a local variable in the reaction description.



There are also a couple of rules and properties that we need to acknowledge:
	- In a composition, the components can have common input variables;
	- They cannot have common output variables, since for any given variable, there is a single component responsible for it;
	- They cannot have common state variables, since we have to rename variables to avoid conflict;
	- Input variable can be output of another and viceversa

By default, **every output of every component of the composition is available to the outside world**, so that means that if $C_1$ has output vars $O_1$ and $C_2$ has output vars $O_2$ then the product $C_1 || C_2$ has output vars $$O_{C_1 || C_2} = O_1 \cup O_2$$A variable is an input of the product if it is an input of one of the components, and not an output of the other, so that means that if f $C_1$ has input vars $I_1$ and $C_2$ has input vars $I$ then the product $C_1 || C_2$ has input $$I_{C_1 || C_2} = (I_1 \cup I_2) - (O_1 \cup O_2)$$
Just like the outputs, the state variables follow the same operation, so if $C_1$ has state vars $S_1$ and $C_2$ has state vars $S$ then the product $C_1 || C_2$ has state vars
$$S_{C_1 || C_2} = S_1 \cup S_2$$
The state of the product is so a pair $(s_1, s_2)$ where $s_1$ is a state of $C_1$ and $s_2$ is a state of $C_2$ and if $C_1$ has $n_1$ states and $C_2$ has $n_2$ states then the product has $n_1n_2$ states.

About the initialisation, if $C_1$ has initialisation $Init_1$ and $C_2$ has initialisation $Init_2$ then the product $C_1 || C_2$ has initialisation $Init_1; Init_2$. An important aspect is that the initialisation order isn't relevant, so we could do the second component initialisation before the first one; this is helpful because we cannot have a non-constant value and we cannot even initialise the state variable to an input value since the state initialisation could, and most of the time is exactly like this, happen BEFORE any input is given.

Let's dive into these concepts with a proper example:

![[SRC_Composition.png]]

![[SRC_Composition_Example.png]]

The output variables set $O$ of $Delay_1 || Delay_2$ is $\{ temp, out \}$, because, as said before, all outputs variable is by default available to the outside world (in order to avoid having all the output variables to be public to the external environment, we use encapsulation. We'll talk about it later.)
The input variables set $I$ of $Delay_1 || Delay_2$ is { $in$ }, since it is the output of a component inside the composition and, therefore, not an input of the product itself.
The state variables of $Delay_1 || Delay_2$ are of course { $x_1, x_2$ } and their initialisation state is the pair $(0, 0)$.

Another important aspect that must be covered is the case of having an output variable of a component as the input variable of another and the viceversa. Dependently from the update code, we can have different scenarios thanks to **tasks**. 

Here's an example:
![[SRC_Tasks.png]]

Here $A1$ and $A2$ are tasks and $A1$ reads "x" and writes "out", while $A2$ reads "in" and writes "x". If we want to represent the fact that "A1" is executed before "A2", so that it has the precedence, we write $A1 \prec A2$. If a task reads/writes more than one variable, we just comma them ($var_1, var_2$  $\rightarrow$ $var_3, var_4$).

We can also have unordered tasks, meaning that there are multiple schedules, which are linear ordering of tasks that are valid, without depending on the execution's order. 
An important keyword used to indicate a dependency of an output variable from an input variable is **awaits**. Here's an example:

![[SRC_Task_Graph.png]]

An important factor of representing the SRCs are **Interfaces**, which are a simpler representation of the component without showing the update code of the component itself. It basically shows the input variables, the output variables and eventual await dependencies.

![[SRC_Interface.png]]

### Component Compatibility
Given Component $C_1$ with input vars $I_1$, output vars $O_1$ and await dependency relation $\succ_1$ and Component $C_2$ with input vars $I_2$, output vars $O_2$ and await dependency relation $\succ_2$, **the two components are compatible** if and only if:
1. The output set $O_1$ and $O_2$ are disjoint, meaning that there are not common outputs;
2. The relation of combined await dependencies is acyclic ($\succ_1 \cup \succ_2$)
When these conditions are met, **the components are compatible and therefore the Parallel Composition between them is possible**
If we find a shared output variable or a cycle, then we demonstrated that the components are not compatible.

In order to complete the composition between two or more components we need to hide to do an **output hiding (encapsulation) operation**. Given a component $C$ and an output variable $y$, the result of hiding $y$ in $C$ is the same component as $C$, but $y$ is no longer an output variable but rather a local variable, making it not more available to the outside world. 

## 1.3 - Extended State Machines
**Extended State Machine (ESM)** is a special SRC with a new variable called `mode` that can be whatever is needed. 

![[SRC_ESM_Example.png]]

In the above example, we define `mode` to range over `{on, off}` and every reaction corresponds to a mode switch (from on to off and viceversa).
If we don't press the button (press = 0) and we are in `off mode`, nothing happens; if we press it, we go into `on mode`. Then if we don't press it for 10 iterations, we go back to `off mode`, otherwise, we reset x.

ESM can be also non-deterministic:
![[SRC_ESM_Example_Non_Deterministic.png]]
