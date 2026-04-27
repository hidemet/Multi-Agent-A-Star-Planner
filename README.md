# Multi-Agent A* Path Planner

Python application implementing a solver for the **Multi-Agent Pathfinding (MAPF)** problem. The software computes optimal, collision-free paths for an agent navigating a 2D grid with obstacles and moving agents, using a **space-time A*** approach.

This project demonstrates a structured engineering workflow for MAPF modeling, reproducible benchmarking, and visual analysis of generated solutions.

## Table of Contents

- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Run the Application](#run-the-application)
  - [gen](#gen)
  - [run](#run)
  - [man](#man)
- [File Formats](#file-formats)

---

## Key Features

- **Space-Time A* Algorithm:** A* implementation that searches over `(position, time)` states to find optimal paths that dynamically avoid other agents.
- **Advanced Collision Prevention:** Robust handling of both **vertex collisions** (same cell, same time) and **edge collisions** (swap conflicts).
- **Multiple Heuristics Support:** Flexible setup to run and compare different heuristic functions (Diagonal, Chebyshev, Manhattan, Euclidean, Relaxed Path).
- **Instance Generation and Benchmarking:** Command-line tooling to generate parameterized scenarios and measure performance consistently.
- **Result Visualization:** Automatic generation of reports (`.md`) including images (`.png`) and animations (`.mp4`) for computed trajectories.

## Tech Stack

- **Language:** Python 3.10+
- **Core Libraries:** `numpy`, `matplotlib`, `Pillow`, `typer`, `pandas`
- **Architecture:** Modular codebase with clear separation of concerns (CLI, core logic, data handling, visualization).

---

## Project Structure

The repository is organized as follows. Detailed implementation notes and performance experiments are available in `test.ipynb`.

- `benchmarks`: files used to generate, test, and solve search problems
- `pf4ea`: source code of the solver

```text
[benchmarks]
   ├── [generators]        # CSV files with problem specifications
   ├── [output_csv]        # CSV outputs used for benchmarking
   ├── [problems]          # Serialized problem instances
   └── [report]            # Generated reports
       └── [media]
[pf4ea]
   ├── __main__.py         # Application entry point
   ├── agents.py           # Agent path generation
   ├── cli.py              # Command-line interface
   ├── constants.py        # Project constants
   ├── gridGraph.py        # Grid generation
   ├── heuristic.py        # Problem heuristics
   ├── input_handler.py    # Manual mode input handler
   ├── plotGraph.py        # Grid plotting utilities
   ├── problem.py          # PF4EA problem definition
   ├── repository.py       # File operations
   ├── result.py           # Search result handling
   ├── search.py           # ReachGoal and variant implementations
   ├── state.py            # State instance generation
   ├── utils.py            # Helper utilities
   └── visualize.py        # Graphical visualization

README.md                  # This file
requirements.txt           # Python dependencies
test.ipynb                 # Performance testing notebook
```

## Requirements

To run the project, use Python 3.10.11 or newer.

Install dependencies from `requirements.txt`:

```bash
pip install -r requirements.txt
```

## Run the Application

After installing dependencies, run the application with one of the following commands:

- `gen`: generate problems from file and solve them
- `run`: load a problem from file and solve it
- `man`: create and solve a problem interactively via terminal input

Use `-h` (or `--help`) at any time to list available options.

Example:

```bash
python pf4ea gen -h
```

### gen

The `gen` command reads problem specifications from file, creates corresponding instances, and solves them.

Options:

- `-f`, `--file`: input file containing problem specifications
- `-h`, `--heuristic`: heuristic to use for search (`h1`, `h2`, `h3`, `h4`, `h5`)
- `-v`, `--variant`: selects the ReachGoal variant
- `-r`, `--report`: saves execution output to a markdown report
- `--show`: renders the solution graphically using matplotlib
- `-s`, `--save`: stores the generated problem instance as a pickle file
- `--csv_output`: writes output to CSV (used for performance testing)

Example:

```bash
python pf4ea gen -f exp_0.csv --heuristic h1 -r
```

This generates and solves the problems listed in `exp_0.csv`, uses heuristic `h1`, and exports a markdown report.

Heuristic mapping:

- `h1`: Diagonal Distance
- `h2`: Chebyshev Distance
- `h3`: Manhattan Distance
- `h4`: Euclidean Distance
- `h5`: Relaxed Path Heuristic

### run

The `run` command loads a previously generated problem instance and solves it with ReachGoal or one of its variants.

Options:

- `-i`, `--file`: input file containing the serialized problem instance
- `-o`, `--output`: output file for algorithm results
- `-h`, `--heuristic`: heuristic to use (`h1`, `h2`, `h3`, `h4`, `h5`)
- `-v`, `--variant`: selects the ReachGoal variant
- `-r`, `--report`: saves execution output to a markdown report
- `--show`: renders the solution graphically

Example:

```bash
python pf4ea run -i 50x50_08_01_20_60_2190_2460.pkl --heuristic h1 -r
```

This loads the problem from pickle, solves it with heuristic `h1`, and writes a markdown report.

### man

The `man` command allows full terminal-based interaction, where problem parameters are entered manually. The resulting solution can still be exported in markdown report format.

## File Formats

### Input Files

#### CSV file

The input CSV containing problem specifications should follow this structure:

```csv
# exp_0.csv
rows,cols,traversability_ratio,obstacle_agglomeration_ratio,num_agents,maximum_time
10,10,0.1,1,0,30
100,10,1,1,30,30
106,10,1,1,30,30
10,105,1,1,30,30
105,107,1,1,30,30
108,104,1,1,30,30
10564,102,1,1,30,30
```

Default path:

`benchmarks/generators/exp_0.csv`

#### Pickle file

Pickle files store serialized Python problem instances.

Default path:

`benchmarks/problems/50x50_08_01_20_60_2190_2460.pkl`

### Output Files

#### Markdown report

Generated markdown reports include:

- problem data
- search results
- performance metrics
- rendered solution image
- animated solution video

Default path:

`benchmarks/report/50x50_08_01_20_60_247_73_DiagonalDistance.md`

#### CSV output

Performance test output can be exported to CSV.

Example:

```csv
rows,cols,traversability_ratio,obstacle_agglomeration_ratio,num_agents,maximum_time,init,goal,h_type,path_length,path_cost,tot_states,percentage_visited_nodes,unique_node_visited,wait,problem_time,heuristic_time,search_time,mem_grid,mem_heuristic,mem_open,mem_closed,mem_path
50,50,0.8,0.1,20,60,645,2285,DiagonalDistance,34,37.14213562373095,947,12.15,243,0,0.0,0.0,0.109375,0.046875,0.046875,0.046875,8.2109375,0.3203125
```

Default path:

`benchmarks/output_csv/output_exp_0.csv`
