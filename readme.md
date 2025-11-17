
# CI2025_lab1

## MULTIPLE DIMENTION, MULTIPLE CONSTRAINT, KNAPSACK PROBLEM

This is the problem presentation.

Given a list of objects, their value and their weight along moultiple dimentions, and given some Knapsack with constraints over all the dimentions, it is needed to find the best composition of object among these knapsack that respects all the given constraints, over all dimentions. An item could also not be taken.

## REPOSITORY STRUCTURE

This is the readme, the entry point that explains what is done.

The file *"knapsack.ipynb"* is a notebook that contains the code to solve the problem, for all instances. For each problem instance it loads the data and solves the problem.

## SOLVING ALGORITHM

### RANDOM INITIALIZATION

The algorithm starts by generating a valid random initial solution for the multi-dimensional knapsack problem. This is a critical phase that ensures the optimization begins with a feasible assignment of items to knapsacks.

**Solution Representation**: Items are represented as a 1D vector where each element indicates the knapsack assignment:
- `-1` = unassigned item  
- `0, 1, 2, ...` = knapsack index (0-based)

**Constraint-Aware Generation**: The random initialization process includes built-in constraint checking to ensure all knapsack capacity limits are respected across all dimensions. If a randomly generated solution violates constraints, it is immediately rejected.

**Progressive Relaxation Strategy**: For harder problem instances where finding a valid random solution becomes time-consuming, the algorithm implements an adaptive timeout mechanism:
- Every `DELAY` seconds, a subset of items (determined by `n_items // STEPS`) are forced to remain unassigned
- This progressive relaxation ensures termination while maintaining solution feasibility
- The forced unassigned positions are tracked in a set to avoid duplicates and ensure efficient constraint satisfaction

**Time Complexity Considerations**: While generating a constraint-satisfying random solution can be computationally expensive for large-scale problems, this investment in a valid starting point significantly improves the subsequent optimization convergence and reduces the risk of getting trapped in infeasible regions of the search space.


### LOCAL SEARCH ALGORITHM

The optimization phase employs a **hybrid hill climbing and simulated annealing** approach to improve the initial random solution. This combination provides both exploitation (hill climbing) and exploration (simulated annealing) capabilities.

**Solution Representation**: The algorithm operates on a 1D vector representation where each element indicates the knapsack assignment for the corresponding item. This compact representation allows for efficient constraint checking and solution manipulation.

**Neighborhood Search (Tweak Function)**: At each iteration, the algorithm generates a neighboring solution by:
- Randomly selecting one item from the current solution
- Randomly reassigning it to a different knapsack (or to unassigned status)
- Ensuring the new solution satisfies all multi-dimensional constraints before acceptance

**Acceptance Criteria**: The algorithm uses a dual acceptance mechanism:
1. **Greedy Acceptance**: Solutions with better or equal fitness are immediately accepted
2. **Simulated Annealing Acceptance**: Worse solutions are accepted with probability `exp(-Δ/T)` where Δ is the fitness deterioration and T is the current temperature

**Temperature Cooling Schedule**: The simulated annealing component implements an **exponential cooling schedule**:
- Initial temperature: `INITIAL_TEMP = 10,000` (high exploration)
- Final temperature: `FINAL_TEMP = 1` (pure hill climbing)
- Cooling rate: `COOLING_RATE = 0.995` per iteration
- Temperature update: `T = max(FINAL_TEMP, T × COOLING_RATE)`

**Convergence and Termination**: The algorithm runs for a fixed number of iterations (`MAX_STEPS = 10,000`) regardless of convergence status. This ensures consistent execution time across problem instances while allowing sufficient exploration of the solution space.

**Constraint Preservation**: All generated solutions are guaranteed to be feasible through the constraint-aware tweak function, eliminating the need for penalty functions or constraint repair mechanisms.

## COMPARISON BETWEEN DIFFERENT RANDOM SOLUTION INITIALIZATION

I want to understand if the compute to obtain randoms solution will let get an advantage comparing the execution with whole empty starting solution.
I have tried different parameter setting, that take about the same execution time (around 5 minutes), on my pc, with same starting condition. 

| MAX_STEPS | STEPS | DELAY | Execution Time | Problem 3 Final Fitness |
|-----------|-------|-------|----------------|-------------------------|
| 1000      | 100   | 1     | 4'49''         | 983,653                 |
| 2000      | 1     | 1     | 4'49''         | 1,048,128               |
| 1685      | 10    | 0.085 | 4'48''         | 1,097,684               |

I have found a setting that let me take advantage of the random starting solution.


## FINAL RESULTS

In the end I chose this parameter settings (MAX_STEP=1685 STEPS=10 DELAY=0.085) that according to me was the most efficient setting with time limit of 5 minutes. 
If the avaiable time is different the parameter could be proportionally modified. 
With this setting it took 4'48'' to run and get these final fitnesses for each problem: problem 1 fitness=1,065  , problem 2 fitness=43'237  , problem 3 fitness= 1,097,684 .

## FINAL NOTES

Notebook and report (README) were written by Matteo Zito, with the support of OpenAI's GPT-5 model, used only as a programming assistant.
    
