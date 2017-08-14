
# Implement a Planning Search

## Synopsis

This project includes skeletons for the classes and functions needed to solve deterministic logistics planning problems for an Air Cargo transport system using a planning search agent. 
With progression search algorithms like those in the navigation problem from lecture, optimal plans for each 
problem will be computed.  Unlike the navigation problem, there is no simple distance heuristic to aid the agent. 
Instead, you will implement domain-independent heuristics.
![Progression air cargo search](images/Progression.PNG)

- Part 1 - Planning problems:
	- READ: applicable portions of the Russel/Norvig AIMA text
	- GIVEN: problems defined in classical PDDL (Planning Domain Definition Language)
	- TODO: Implement the Python methods and functions as marked in `my_air_cargo_problems.py`
	- TODO: Experiment and document metrics
- Part 2 - Domain-independent heuristics:
	- READ: applicable portions of the Russel/Norvig AIMA text
	- TODO: Implement relaxed problem heuristic in `my_air_cargo_problems.py`
	- TODO: Implement Planning Graph and automatic heuristic in `my_planning_graph.py`
	- TODO: Experiment and document metrics
- Part 3 - Written Analysis

## Environment requirements
- Python 3.4 or higher
- Starter code includes a copy of [companion code](https://github.com/aimacode) from the Stuart Russel/Norvig AIMA text.  


## Project Details
### Part 1 - Planning problems
#### READ: Stuart Russel and Peter Norvig text:

"Artificial Intelligence: A Modern Approach" 3rd edition chapter 10 *or* 2nd edition Chapter 11 on Planning, available [on the AIMA book site](http://aima.cs.berkeley.edu/2nd-ed/newchap11.pdf) sections: 

- *The Planning Problem*
- *Planning with State-space Search*

#### GIVEN: classical PDDL problems

All problems are in the Air Cargo domain.  They have the same action schema defined, but different initial states and goals.

- Air Cargo Action Schema:
```
Action(Load(c, p, a),
	PRECOND: At(c, a) ∧ At(p, a) ∧ Cargo(c) ∧ Plane(p) ∧ Airport(a)
	EFFECT: ¬ At(c, a) ∧ In(c, p))
Action(Unload(c, p, a),
	PRECOND: In(c, p) ∧ At(p, a) ∧ Cargo(c) ∧ Plane(p) ∧ Airport(a)
	EFFECT: At(c, a) ∧ ¬ In(c, p))
Action(Fly(p, from, to),
	PRECOND: At(p, from) ∧ Plane(p) ∧ Airport(from) ∧ Airport(to)
	EFFECT: ¬ At(p, from) ∧ At(p, to))
```

- Problem 1 initial state and goal:
```
Init(At(C1, SFO) ∧ At(C2, JFK) 
	∧ At(P1, SFO) ∧ At(P2, JFK) 
	∧ Cargo(C1) ∧ Cargo(C2) 
	∧ Plane(P1) ∧ Plane(P2)
	∧ Airport(JFK) ∧ Airport(SFO))
Goal(At(C1, JFK) ∧ At(C2, SFO))
```
- Problem 2 initial state and goal:
```
Init(At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) 
	∧ At(P1, SFO) ∧ At(P2, JFK) ∧ At(P3, ATL) 
	∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3)
	∧ Plane(P1) ∧ Plane(P2) ∧ Plane(P3)
	∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL))
Goal(At(C1, JFK) ∧ At(C2, SFO) ∧ At(C3, SFO))
```
- Problem 3 initial state and goal:
```
Init(At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) ∧ At(C4, ORD) 
	∧ At(P1, SFO) ∧ At(P2, JFK) 
	∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3) ∧ Cargo(C4)
	∧ Plane(P1) ∧ Plane(P2)
	∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL) ∧ Airport(ORD))
Goal(At(C1, JFK) ∧ At(C3, JFK) ∧ At(C2, SFO) ∧ At(C4, SFO))
```

#### TODO: Implement methods and functions in `my_air_cargo_problems.py`
- `AirCargoProblem.get_actions` method including `load_actions` and `unload_actions` sub-functions
- `AirCargoProblem.actions` method
- `AirCargoProblem.result` method
- `air_cargo_p2` function
- `air_cargo_p3` function

#### TODO: Experiment and document metrics for non-heuristic planning solution searches
* Run uninformed planning searches for `air_cargo_p1`, `air_cargo_p2`, and `air_cargo_p3`; provide metrics on number of node expansions required, number of goal tests, time elapsed, and optimality of solution for each search algorithm. Include the result of at least three of these searches, including breadth-first and depth-first, in your write-up (`breadth_first_search` and `depth_first_graph_search`). 
* If depth-first takes longer than 10 minutes for Problem 3 on your system, stop the search and provide this information in your report.
* Use the `run_search` script for your data collection: from the command line type `python run_search.py -h` to learn more.

>#### Why are we setting the problems up this way?  
>Progression planning problems can be 
solved with graph searches such as breadth-first, depth-first, and A*, where the 
nodes of the graph are "states" and edges are "actions".  A "state" is the logical 
conjunction of all boolean ground "fluents", or state variables, that are possible 
for the problem using Propositional Logic. For example, we might have a problem to 
plan the transport of one cargo, C1, on a
single available plane, P1, from one airport to another, SFO to JFK.
![state space](images/statespace.png)
In this simple example, there are five fluents, or state variables, which means our state 
space could be as large as ![2to5](images/twotofive.png). Note the following:
>- While the initial state defines every fluent explicitly, in this case mapped to **TTFFF**, the goal may 
be a set of states.  Any state that is `True` for the fluent `At(C1,JFK)` meets the goal.
>- Even though PDDL uses variable to describe actions as "action schema", these problems
are not solved with First Order Logic.  They are solved with Propositional logic and must
therefore be defined with concrete (non-variable) actions
and literal (non-variable) fluents in state descriptions.
>- The fluents here are mapped to a simple string representing the boolean value of each fluent
in the system, e.g. **TTFFTT...TTF**.  This will be the state representation in 
the `AirCargoProblem` class and is compatible with the `Node` and `Problem` 
classes, and the search methods in the AIMA library.  


### Part 2 - Domain-independent heuristics
#### READ: Stuart Russel and Peter Norvig text
"Artificial Intelligence: A Modern Approach" 3rd edition chapter 10 *or* 2nd edition Chapter 11 on Planning, available [on the AIMA book site](http://aima.cs.berkeley.edu/2nd-ed/newchap11.pdf) section: 

- *Planning Graph*

#### TODO: Implement heuristic method in `my_air_cargo_problems.py`
- `AirCargoProblem.h_ignore_preconditions` method

#### TODO: Implement a Planning Graph with automatic heuristics in `my_planning_graph.py`
- `PlanningGraph.add_action_level` method
- `PlanningGraph.add_literal_level` method
- `PlanningGraph.inconsistent_effects_mutex` method
- `PlanningGraph.interference_mutex` method
- `PlanningGraph.competing_needs_mutex` method
- `PlanningGraph.negation_mutex` method
- `PlanningGraph.inconsistent_support_mutex` method
- `PlanningGraph.h_levelsum` method


#### TODO: Experiment and document: metrics of A* searches with these heuristics
* Run A* planning searches using the heuristics you have implemented on `air_cargo_p1`, `air_cargo_p2` and `air_cargo_p3`. Provide metrics on number of node expansions required, number of goal tests, time elapsed, and optimality of solution for each search algorithm and include the results in your report. 
* Use the `run_search` script for this purpose: from the command line type `python run_search.py -h` to learn more.

>#### Why a Planning Graph?
>The planning graph is somewhat complex, but is useful in planning because it is a polynomial-size approximation of the exponential tree that represents all possible paths. The planning graph can be used to provide automated admissible heuristics for any domain.  It can also be used as the first step in implementing GRAPHPLAN, a direct planning algorithm that you may wish to learn more about on your own (but we will not address it here).

>*Planning Graph example from the AIMA book*
>![Planning Graph](images/eatcake-graphplan2.png)

### Part 3: Written Analysis
#### TODO: Include the following in your written analysis.  
- Provide an optimal plan for Problems 1, 2, and 3.
- Compare and contrast non-heuristic search result metrics (optimality, time elapsed, number of node expansions) for Problems 1,2, and 3. Include breadth-first, depth-first, and at least one other uninformed non-heuristic search in your comparison; Your third choice of non-heuristic search may be skipped for Problem 3 if it takes longer than 10 minutes to run, but a note in this case should be included.
- Compare and contrast heuristic search result metrics using A* with the "ignore preconditions" and "level-sum" heuristics for Problems 1, 2, and 3.
- What was the best heuristic used in these problems?  Was it better than non-heuristic search planning methods for all problems?  Why or why not?
- Provide tables or other visual aids as needed for clarity in your discussion.

## Examples and Testing:
- The planning problem for the "Have Cake and Eat it Too" problem in the book has been
implemented in the `example_have_cake` module as an example.
- The `tests` directory includes `unittest` test cases to evaluate your implementations. All tests should pass before you submit your project for review. From the AIND-Planning directory command line:
    - `python -m unittest tests.test_my_air_cargo_problems`
    - `python -m unittest tests.test_my_planning_graph`
- The `run_search` script is provided for gathering metrics for various search methods on any or all of the problems and should be used for this purpose.

## Submission
Before submitting your solution to a reviewer, you are required to submit your project to Udacity's Project Assistant, which will provide some initial feedback.  

The setup is simple.  If you have not installed the client tool already, then you may do so with the command `pip install udacity-pa`.  

To submit your code to the project assistant, run `udacity submit` from within the top-level directory of this project.  You will be prompted for a username and password.  If you login using google or facebook, visit [this link](https://project-assistant.udacity.com/auth_tokens/jwt_login) for alternate login instructions.

This process will create a zipfile in your top-level directory named cargo_planning-<id>.zip.  This is the file that you should submit to the Udacity reviews system.

## Improving Execution Time

The exercises in this project can take a *long* time to run (from several seconds to a several hours) depending on the heuristics and search algorithms you choose, as well as the efficiency of your own code.  (You may want to stop and profile your code if runtimes stretch past a few minutes.) One option to improve execution time is to try installing and using [pypy3](http://pypy.org/download.html) -- a python JIT, which can accelerate execution time substantially.  Using pypy is *not* required (and thus not officially supported) -- an efficient solution to this project runs in very reasonable time on modest hardware -- but working with pypy may allow students to explore more sophisticated problems than the examples included in the project.

# Heuristic Analysis - Planning
__Frederick Chyan__

*Aug 17, 2017*


## Problem Description
Classical PDDL problems given below

**Air Cargo Action Schema:**

```
Action(Load(c, p, a),
	PRECOND: At(c, a) ∧ At(p, a) ∧ Cargo(c) ∧ Plane(p) ∧ Airport(a)
	EFFECT: ¬ At(c, a) ∧ In(c, p))
Action(Unload(c, p, a),
	PRECOND: In(c, p) ∧ At(p, a) ∧ Cargo(c) ∧ Plane(p) ∧ Airport(a)
	EFFECT: At(c, a) ∧ ¬ In(c, p))
Action(Fly(p, from, to),
	PRECOND: At(p, from) ∧ Plane(p) ∧ Airport(from) ∧ Airport(to)
	EFFECT: ¬ At(p, from) ∧ At(p, to))
```

**Problem 1:**

```
Init(At(C1, SFO) ∧ At(C2, JFK) 
	∧ At(P1, SFO) ∧ At(P2, JFK) 
	∧ Cargo(C1) ∧ Cargo(C2) 
	∧ Plane(P1) ∧ Plane(P2)
	∧ Airport(JFK) ∧ Airport(SFO))
Goal(At(C1, JFK) ∧ At(C2, SFO))
```

**Problem 2:**

```
Init(At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) 
	∧ At(P1, SFO) ∧ At(P2, JFK) ∧ At(P3, ATL) 
	∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3)
	∧ Plane(P1) ∧ Plane(P2) ∧ Plane(P3)
	∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL))
Goal(At(C1, JFK) ∧ At(C2, SFO) ∧ At(C3, SFO))
```

**Problem 3:**

```
Init(At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) ∧ At(C4, ORD) 
	∧ At(P1, SFO) ∧ At(P2, JFK) 
	∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3) ∧ Cargo(C4)
	∧ Plane(P1) ∧ Plane(P2)
	∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL) ∧ Airport(ORD))
Goal(At(C1, JFK) ∧ At(C3, JFK) ∧ At(C2, SFO) ∧ At(C4, SFO))
```

## Result
**Problem 1**

| Algorithm                                         | Expansions | Goal Tests | New Nodes | Elapsed Time (s) | Path Length | Optimal |
|---------------------------------------------------|------------|------------|-----------|------------------|-------------|---------|
| Breadth First Search                              | 43         | 56         | 180       | 0.030490892      | 6           | YES     |
| Breadth First Tree Search                         | 1458       | 1459       | 5960      | 0.947382754      | 6           | YES     |
| Depth First Graph Search                          | 21         | 22         | 84        | 0.012808286      | 20          | NO      |
| Greedy Best First Graph Search                    | 7          | 9          | 28        | 0.006106681      | 6           | YES     |
| A Star Search Heuristic: Ignore Preconditions     | 41         | 43         | 170       | 0.048111511      | 6           | YES     |
| A Star Search Heuristic: Planning Graph Level Sum | 11         | 13         | 50        | 0.796748964      | 6           | YES     |

**Problem 2**

| Algorithm                                        | Expansions | Goal Tests | New Nodes | Elapsed Time (s) | Path Length | Optimal |
|---------------------------------------------------|------------|------------|-----------|------------------|-------------|---------|
| Breadth First Search                              | 3343       | 4609       | 30509     | 13.43590876      | 9           | YES     |
| Breadth First Tree Search                         |            |            |           | Timeout          |             |         |
| Depth First Graph Search                          | 624        | 625        | 5602      | 3.450186569      | 619         | NO      |
| Greedy Best First Graph Search                    | 998        | 1000       | 8982      | 2.335751873      | 17          | NO      |
| A Star Search Heuristic: Ignore Preconditions     | 1450       | 1452       | 13303     | 4.173740015      | 9           | YES     |
| A Star Search Heuristic: Planning Graph Level Sum | 86         | 88         | 841       | 68.66236802      | 9           | YES     |

**Problem 3**

| Algorithm                                        | Expansions | Goal Tests | New Nodes | Elapsed Time (s) | Path Length | Optimal |
|---------------------------------------------------|------------|------------|-----------|------------------|-------------|---------|
| Breadth First Search                              | 14663      | 18098      | 129631    | 110.2354232      | 12          | YES     |
| Breadth First Tree Search                         |            |            |           | Timeout          |             |         |
| Depth First Graph Search                          | 408        | 409        | 3364      | 1.73957705       | 392         | NO      |
| Greedy Best First Graph Search                    | 5398       | 5400       | 47665     | 14.53414782      | 26          | NO      |
| A Star Search Heuristic: Ignore Preconditions     | 5038       | 5040       | 44926     | 14.85381065      | 12          | YES     |
| A Star Search Heuristic: Planning Graph Level Sum | 314        | 316        | 2894      | 329.5419026      | 12          | YES     |


## Optimal Plan
Below are the optimal plans for the given problems. They are obtained by breadth first search or A* search.

**Problem 1:**

```
Path length: 6
Load(C1, P1, SFO)
Load(C2, P2, JFK)
Fly(P2, JFK, SFO)
Unload(C2, P2, SFO)
Fly(P1, SFO, JFK)
Unload(C1, P1, JFK)
```

**Problem 2:**

```
Path length: 9
Load(C1, P1, SFO)
Load(C2, P2, JFK)
Load(C3, P3, ATL)
Fly(P2, JFK, SFO)
Unload(C2, P2, SFO)
Fly(P1, SFO, JFK)
Unload(C1, P1, JFK)
Fly(P3, ATL, SFO)
Unload(C3, P3, SFO)

```

**Problem 3:**

```
Path length: 12
Load(C1, P1, SFO)
Load(C2, P2, JFK)
Fly(P2, JFK, ORD)
Load(C4, P2, ORD)
Fly(P1, SFO, ATL)
Load(C3, P1, ATL)
Fly(P1, ATL, JFK)
Unload(C1, P1, JFK)
Unload(C3, P1, JFK)
Fly(P2, ORD, SFO)
Unload(C2, P2, SFO)
Unload(C4, P2, SFO)

```

## Non-heuristic Search
As demonstrated in the result, **breadth first search** always return optimal result. This is because BFS will check all nodes at each depth incrementally. Therefore, the first node that meets the goal is necessarily optimal. However, this is exhaustive and brute force. **Depth first search**, on the other hand, terminates much faster, but produces non-optimal result (e.g. path length of 392 vs. optimal path length of 12). This is understandable, as it simply keep trying various action until the goal is met. While fast, the resulting plan is almost useless. For completeness, **tree search** result is included here. Tree search is not effective in these problems as there might be loops in the search path so it might never terminate, also, duplicated states will be searched as visited nodes are not recorded. **Greedy best first graph search** looks to be a pretty good compromise, it achieves optimal result in problem 1 and problem 2 and achieves a reasonable result in problem 3 (path length: 26) which is slightly worse than optimal (12) but a lot better than DFS (path length: 392).


## Heuristic Search using A*
Both yield optimal result. Also, it is worth pointing out that `ignore preconditions` is faster than BFS, therefore it would be good search strategy when optimal result is necessary. Comparing `ignore preconditions` with `level-sum` showed some interesting facts. The `level-sum` heuristic expands a lot less nodes but takes much longer time. This is because every time the `level-sum` heuristic function is evaluated, it needs to create a new Planning Graph and compute the heuristic value. This means that, while the `level-sum` heuristic explores fewer nodes, there are overheads such as having to recreate objects every time at different states.

## Conclusion
The best overall search strategy should be used is **A* with the "ignore preconditions" heuristic**. It is guaranteed to provide optimal plan[^Russell2010], and terminates faster than BFS. This is more evident when the search space is larger like in problem 3, where the heuristic search is 10 times faster than non-heuristic search. Simple heuristic search expands less nodes than BFS, and there isn't too much computational overhead unlike planning graph level sum heuristic.


[^Russell2010]: Russell, S. J., & Norvig, P. (2010). Artificial intelligence: A modern approach. Pearson Education, Inc. p. 376

# Reserch Review - Planning
__Frederick Chyan__

*Aug 17, 2017*

## Intractability
Planning is an important and foundamental problem in AI, because it covers two essential study **logic** and **search**. To phrase it in another way, _planning is about controlling combinatorial explosion_. Therefore, before diving into the history of development of various planning algorithm and solvers, a section on computational complexity is provided for reference. To begin with, P problems contains problems that can be solved in polynomial time and verify in polynominal time. NP problem contains problems that can be verified in polynomial time but unknown if a polynomial solution exist. Therefore P is a subset of NP. Examples of NP-Complete problems include set cover, set packing, 3SAT etc. Co-NP problem is the complementary of NP, i.e. when the "no" instance (complement of NP's decision problem) can be verified in polynomial time. **Planning problems** are even harder (at least as hard as) than NP-complete problems because a solution might not be verified in polynomial time. This is due to the plan itself might be up to 2^n long. There are algorithms, however, can solve planning problems by using only polynomial space by throwing away intermediate data (recomputation necessary), so planning problem belong in PSPACE. It can be shown all other problems in PSPACE are polynomial reducible to planning problem, making planning problem PSPACE-Hard.

## Hierarchical Task Network
Hierarchical task network was one of the early development in planning. The algorithm works as follow.[^Nau1999]

```
1. Input a planning problem P.
2. If P contains only primitive tasks, then resolve the conflicts in P 
   and return the result. If the conflicts cannot be resolved,
   return failure.
3. Choose a non-primitive task t in P.
4. Choose an expansion for t.
5. Replace t with the expansion.
6. Use critics to find the interactions among tasks in P, 
   and suggest ways to handle them.
7. Apply one of the ways suggested in step 6. 
8. Go to step 2.
```
The intuition of the algorithm can be described in 4 steps. 1. Decompose tasks into subtasks 2. Handle constraints 3. Resolve interaction. 4. Backtrack and try other decompositions.
Due to HTN's expressive syntax, its complexity might be harder than PSPACE because some problem might be undecidable. The HTN is closely related to partial-order planning such as UCPOP[^Penberthy1992] which allows expressive syntax (ADL) and yet achieve sound and complete plan without restrictive syntax (STRIP) or linear planning.

## Planning Graph

Planning Graph was introduced as part of GRAPHPLAN algorithm in 1997 [^Blum1997]. Planning graph works by creating alternating literal and action states recursively. When new possible literals are added, it makes new actions available (or not available) and vice versa. This repeats until the planning graph has leveled off (no plan), that is, when no new information are generated. GraphPlan always returns optimal plan. Planning Graph opens a wide variety of possibilities in the development in planners in the area of **Reachability Heuristics**.[^bryce2007] The simplest reachability analysis is whether the goal can be reached from the initial state. Without using planning graph, the search tree will need to be explored using forward chaining, but this takes exponential time as it basically brute-forced the solution. Planning graph can be used to derive several heuristics to improve the search such as level based and cost based heuristics. There are also heuristics to be used in regression as well. Mutexes used in constructing the planning graph are relaxed or ignored in some heuristics, this is useful for stochastic and temporal planning too.

## Ordered Binary Decision Diagram
Binary decision diagram are used in the field of computer aided design, FPGA and logic synthesis rely heavily on optimization/equivalence checking provided by BDDs. It enables formal verification via **model checking**. It works by describing the domain of interest by a semantic model. Then describe the desired property by a logic formula. And finally check to see if the domain satisfy the desired property by checking whether the formula is true in the model. It not hard to draw similarity of formal verification of dynamic system to planning problems. Planning can be solved via symbolic model checking. In planning, the main problem is large state spaces. The idea is to utilize work on symbolic model checking based on OBDD's. It provides two main benefits: 1. canonical form for propositional formulas 2. efficient boolean operations in polynomial time.[^Bryant1986] However, OBDD does require human heuristic specifying ordering on problem input. This cannot be done automatically because choosing the correct ordering to minimize the graph size is itself a co-NP problem. 

## Comparison
Partial-order planning dominated the research space for a long time until planning graph was introduced. However, with the heuristics borrowed from planning graphs, partial order planning can achieve better performance as its more parallelizable. In terms of tackling intractibility, partial-order planning or planning graph are both constraint-based approaches. It is perhaps no surprising, because the only way to achieve optimal solution in PSPACE-Complete problems in the worse case is to either 1. constraint on the input or 2. achieve non-optimal plan. If the state-space is large, then a search based algorithm with feasible result will be better.
OBDD's is a different planning system with its roots from hardware design. Its usage are in dynamic control systems where model checker has additional information based on the specific problem domain. 

[^Nau1999]: D. S. Nau, Y. Cao, A. Lotem, and H. Muoz-Avila. SHOP: Simple hierarchical ordered planner. Proceedings of the International Joint Conference on Artificial Intelligence (IJCAI), pp. 968-973. Morgan Kaufmann Publishers, Jul 31-August 6 1999

[^Penberthy1992]: J.S. Penberthy and D. Weld "UCPOP: A Sound, Complete, Partial-Order Planner for ADL," Proceedings of KR-92, 103-114, Cambridge, MA, October 1992

[^bryce2007]: Daniel Bryce and Subbarao Kambhampati. (2007) Tutorial on Planning Graph–Based Reachability Heuristics. AI Magazine Spring 2007

[^Blum1997]: A. Blum and M. Furst, "Fast Planning Through Planning Graph Analysis", Artificial Intelligence, 90:281--300 (1997)

[^Bryant1986]: Randal E. Bryant. Graph-Based Algorithms for Boolean Function Manipulation. IEEE Transactions on Computers, Vol. C-35, No. 8, August, 1986, pp. 677-691.

