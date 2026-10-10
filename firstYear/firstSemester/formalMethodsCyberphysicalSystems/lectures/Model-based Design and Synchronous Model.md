## 1.1 - Synchronous Reactive Components
**Model-based design (MBD)** is a particular technique adopted by software engineering that build mathematical and visual representations of systems behaviour and physical components using *block diagrams*. 
Its specific purpose is to test and validate systems performance across multiple scenarios before building the physical hardware. 
Thanks to this technique, it is therefore possible to generate production code for embedded deployment from validated systems, cutting the majority of the development cycles that would be required if we adopted a "traditional" way. Moreover, using a mathematical and visual representation of our system, it is possible to omit the use of physical prototypes by spotting problems and bugs during the virtual experimentations.

MBD can adopt 2 distinct types of computation regarding data. More specifically, functional vs reactive computation defines in which way data is evaluated, computed and trasformed:
	- **Functional Computation**: given an input, the program produces an output. Using this type of computation, the focus is on the *what* to compute, meaning that given the same input it yields the same output without any side effects. It is mainly used in transformational algorithms, where state changes are explicit and predictable;
	- **Reactive Computation**: its main focus is on *when* and *how* data changes and it's driven by events, signals, asynchronously. It is used for event-driven systems and embedded systems that must react immediately to unpredictable external impulses.

Another important aspect is the execution model, so how control flows and whether operations one after another or multiple computations overlap/progress independently. There are 2 different computations:
	- **Sequential Computation**: it executes steps one after another in a strict, predictable linear order where operations and state transitions occur in a rigid, predetermined step-by-step sequence within a single execution thread or clock cycle. Considering 2 different blocks A and B, A must fully finish processing its input and compute its output before block B can begin, even if they aren't related at all. 
	- **Concurrent Computation**: it structures a system as multiple independent processes that can handle events or execute logic overlapping in time. In this computation,  models are broken down into multiple independent, interacting components or tasks that progress simultaneously

The core aspect of the synchronous model is the **Synchronous Hypothesis**: 
	*Time needed to execute the update code is negligible compared to delay between successive input arrivals*
This means that the reactive system is infinitely faster than its environment: when an input arrives, the system reacts, compute its output and update its states instantaneously. This hypothesis is important for two main factors:
	- It **eliminates race conditions** because everything happens simultaneously and instantaneously in the logical model;
	- It **guarantees determinism** because given the same sequence of inputs, a synchronous model will _always_ produce the exact same sequence of outputs, making it mathematically rigorous and safe for safety-critical system.
In real-world contexts, hardware doesn't have zero-time execution because CPUs takes clock cycles and buses take time to transport data. That's where compilers and code generators come to help because tools that adopt this particular hypothesis use the zero-time logical model and mathematically check for causality loops; then, they compile it into deterministic, sequential target code that runs fast enough on real hardware to *approximate* instantaneous response.

Two main concepts in MBD and its definition are **Syntax** and **Semantics**:
	- The former gives us the instruments to describe a component, using variable declarations, types, update code, ...
	- The latter provides the component's description using mathematical concepts like sets, relations, functions, ...
Semantics must be formal because, when they are, computers can analyse them and allows:
	 - **Formal Verification and Model Checking**, which consists in proving mathematically that a system will _never_ reach an unsafe state;
	 - **Automatic Code Generation:** Translating the model directly into C, C++, or VHDL with 100% determinism because the compiler knows the exact mathematical meaning of every single block and connection.

The language adopted by Synchronous Model is **Synchronous Reactive Component (SRC)**. 

![[Screenshot 2026-09-30 at 09.53.22.png]]

We can see from left to right:
	- An input variable, named **in** of type **bool**;
	- A state variable, name **x** of type **bool**, initialised to 0;
	- The update code, which, in each round, in response to an input, produces an output and update state;
	- An output variable, named **out** of type **bool**;
The general representation of a SRC is the following:

![[Screenshot 2026-09-30 at 10.22.23.png]]

Every SRC is a tuple of sets and expressions in this form: ***$C = (I, O, S, Init, React)$***:
- A set named $I$ of input variables, whose can have many types, like int, bool, real, tuples, ... and the valuation space ${Q_I}$, which is the set of all possible combinations of values that those input variables can take during any given round on their defined types;
- A set named $O$ of typed output variables and the valuation space $Q_O$, which is the set of all possible combinations of values that those variables can take during any given round;
- A set named $S$ of typed state variables and the valuation set is denoted as $Q_S$. The state is internal to the component and is passed during every round of computation. 
- Every state variable must be initialised and initialisation set is denoted by $Init$. That set is a sequence of assignments to state variables and the set $[\![Init]\!]$ of initial states is a subset of $Q_S$;
- Execution in each round is given by the code fragment $React$, that is a sequence of assignments and conditionals that assign output variables and update state variables. The set $[\![React]\!]$ of reactions where each reaction is of the form $(old)state \rightarrow^{(input/output)} (new)state$ is a subset of $Q_S \times Q_I \times Q_O \times Q_S$ 

Syntax errors also represent an important aspect of SRC, since update code is expected to satisfy a specific amount of requirements:
	- Types of variables and expression must match;
	- Output variables must be written before being read;
	- Output variables must be explicitly assigned a value;
	- Input variables cannot be written;
In all these cases, a syntax error is returned.

Finally, a component can have multiple reactions, like nondeterministic reaction if for example the update can be chosen from a specific set of values (like for example the code `x := choose { in, x }`), and also with conditional assignments (like `if x ≠ in then { ... }`). The general form for statements is if $test$ then $statement_1$ else $statement_2$)

Besides of the classical variable types like boolean, numbers and so on, there is also another particular variable type named `event`. An event can be either absent or present; in the second case, it has a value:
	- `event x` means that x ranges over { $\top$, $\bot$ } (present, absent);
	- `event(bool) x` means that x ranges over { $\bot$, 0, 1 };
	- `event(nat) x` means that x ranges over { $\bot$, 0, 1, 2, ... }
If we wanna check if the event variable is present we use the `?`, so `x?` means "is x present?"; if we wanna assign a value to the event we use the `!`, so `x!v` means `x := v`.
If we do not assign any value to an output event, then it is absent by default. Event-triggered components executes only in those rounds where input events are present. 
Here follows an example of a event-triggered component:

![[SRC_Event_Example.png]]

Another example is the following:
"Issue the output event every 60-th time the input event is present"

## 1.2 - Composition
Composition is a particular technique used in MBD that allows us to structure and build complex models starting from simpler ones.
To do so, we need 3 main operations that allow us to combine components in order to create a higher order component.
These are:
	- **Instantiation (or Renaming)**: trivially, to make 2 components compose together we need to instantiate them, meaning that we need to build a model of our component in order to work with it. If we, hypothetically, need to have more copies of the same instance, we need to do also a renaming operation, in which we change the state variables' names in order to avoid naming clashes. This is mandatory because if we had 2 different components with the same state variable name, our system wouldn't know exactly which one to use;
	- **Parallel Composition**: it is represented by the `||` symbol. In this operation, we combine our components so they run concurrently reacting to the same logical clock ticks in the synchronous model. In a parallel composition, if Component A outputs a value that Component B takes in input, the composition operation binds those variables together.
	- **Encapsulation**: once two components are wired together, the signal passing between them is an internal matter of the composition and encapsulation prevents the rest of the system or the environment itself from messing with those variables. In this way, we can guarantee to have information hiding and modularity, ensuring that external components can only interact with the system through its public inputs and outputs.
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
Tasks are essentially atomic blocks of code which specifies the variables that reads and writes. To represent the order of execution of tasks, we use a **Task Graph**, where vertices are the tasks and edges denote the precedence, which is represented with the $\prec$ symbol. Everything of the left side is executed BEFORE everything on the right one. Graph have to be acyclic in order to avoid race conditions. 

Here's an example:
![[SRC_Tasks.png]]

Here $A1$ and $A2$ are tasks and $A1$ reads "x" and writes "out", while $A2$ reads "in" and writes "x". If we want to represent the fact that "A1" is executed before "A2", so that it has the precedence, we write $A1 \prec A2$. If a task reads/writes more than one variable, we just comma them ($var_1, var_2$  $\rightarrow$ $var_3, var_4$).

We can also have unordered tasks, meaning that there are multiple schedules, which are linear ordering of tasks that are valid, without depending on the execution's order. 
An important keyword used to indicate a dependency of an output variable from an input variable is **awaits**. Here's an example:

![[SRC_Task_Graph.png]]

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
