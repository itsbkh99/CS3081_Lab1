# CS3081 – Artificial Intelligence | Lab 1: Maze Solver
**Effat University · Computer Science Department · Spring 2026**

---

## 📁 Project Structure

```
Lab1/
├── maze.py           # Main solver — DFS (StackFrontier)
├── maze_bfs.py       # Modified solver — BFS (QueueFrontier) [Exercise 2]
├── maze1.txt         # Small maze  (6×7)
├── maze2.txt         # Large maze (16×29)
├── maze3.txt         # Medium maze
├── maze4.txt         # Custom maze [Exercise 3]
├── requirements.txt  # Python dependencies
├── images/           # All output PNG visualizations
└── README.md         # This file
```

---

## ▶️ How to Run

```bash
# Install dependencies
pip install -r requirements.txt

# Run with DFS (original)
python maze.py maze1.txt
python maze.py maze2.txt
python maze.py maze3.txt
python maze.py maze4.txt

# Run with BFS (Exercise 2 modification)
python maze_bfs.py maze1.txt
python maze_bfs.py maze2.txt
python maze_bfs.py maze3.txt
python maze_bfs.py maze4.txt
```

**Output:** The program prints the maze before and after solving (solution marked with `*`), reports the number of states explored, and generates `maze.png`.

---

## 🗺️ Maze File Format

```
Symbol   Meaning
──────────────────────────────
  #      Wall (impassable)
 (sp)    Open path
  A      Start position (exactly one)
  B      Goal position (exactly one)
  *      Solution path (printed output only)
```

---

## 🔁 Exercise 1 — Run and Observe (DFS Results)

| Maze    | Dimensions | States Explored | Path Length |
|---------|------------|-----------------|-------------|
| maze1   | 6 × 7      | 11              | 10 steps    |
| maze2   | 16 × 29    | 194             | 22 steps    |
| maze3   | 6 × 7      | 17              | 14 steps    |

**maze1 — DFS Solution:**
```
█████B█
█████*█
████**█
████*██
*****██
A██████
```

**maze2 — DFS Solution (partial view):**
```
...
█   ██********        █ █ █ █
█ █ ██*███ ██*█████████ █ █ █
█ █****█   ██B█         █ █ █
...
A******██████████████████████
```

**maze3 — DFS Solution:**
```
██****█
██*██*█
█B*█**█
█ ██*██
*****██
A██████
```

---

## 🔄 Exercise 2 — Switch to BFS (maze_bfs.py)

**Change made:** Line 127 in `maze.py` → `frontier = QueueFrontier()` instead of `StackFrontier()`.

| Maze  | DFS States | BFS States | DFS Path Length | BFS Path Length | Same Path? |
|-------|------------|------------|-----------------|-----------------|------------|
| maze1 | 11         | 11         | 10 steps        | 10 steps        | ✅ Yes     |
| maze2 | 194        | 77         | 22 steps        | 22 steps        | ✅ Yes     |
| maze3 | 17         | 6          | 14 steps        | 6 steps         | ❌ No      |

### Key Observations

**States Explored:**
- For maze1, both algorithms happened to explore the same number of states because the maze has a narrow corridor with little branching.
- For maze2, BFS explored significantly fewer states (77 vs 194) because it fans out level-by-level and reaches the goal sooner without exploring deep dead ends.
- For maze3, BFS was dramatically more efficient (6 vs 17) and found a shorter path.

**Path Shape:**
- DFS dives deep — it follows one branch to the end before backtracking. This can lead to long, winding paths.
- BFS expands like ripples in water — it explores all neighbours at distance 1, then distance 2, etc., guaranteeing the shortest path.

**Is the path always the same?**
No. As seen in maze3, DFS found a 14-step path while BFS found a 6-step path. BFS always finds the **shortest** path; DFS finds *a* path, not necessarily the shortest.

---

## 🧩 Exercise 3 — Custom Maze (maze4.txt)

```
#######
#A    #
#     #
## ## #
#   # #
# # # #
# # B #
# #   #
#     #
#######
```

This maze was designed so that **DFS and BFS find completely different paths**:

- **DFS** (tries "up" before "down") takes the long right-side path, wrapping around the top.
- **BFS** finds the short direct path down the left side.

| Maze  | Algorithm | States Explored | Path Taken             |
|-------|-----------|-----------------|------------------------|
| maze4 | DFS       | 11              | Long path (right side) |
| maze4 | BFS       | 23              | Short path (left side) |

**DFS path (goes right and down the right corridor):**
```
███████
█A****█
█    *█
██ ██*█
█   █*█
█ █ █*█
█ █ B*█
...
```

**BFS path (goes down and directly to B):**
```
███████
█A    █
█**   █
██*██ █
█ **█ █
█ █*█ █
█ █*B █
...
```

---

## ❓ Discussion Questions — Answers

### Q1. Why does the current implementation use `StackFrontier` instead of `QueueFrontier`?

The lab uses `StackFrontier` to implement **Depth-First Search (DFS)**. A stack is Last-In-First-Out (LIFO): the most recently added node is always explored next. This makes the algorithm "go deep" — it follows one path all the way to its end (or a dead end) before backtracking and trying another branch. This mirrors how you'd explore a physical maze by always going forward until you hit a wall, then backtracking.

### Q2. What would happen if you changed line 127 to use `QueueFrontier()`?

Using a `QueueFrontier` converts the algorithm to **Breadth-First Search (BFS)**. BFS uses a queue (First-In-First-Out), so it always explores the *oldest* node next.

- **Exploration pattern change:** Instead of diving deep, the algorithm explores all cells at distance 1 from start, then all at distance 2, and so on — like ripples spreading in a pond.
- **Path found:** BFS guarantees the **shortest path** in terms of number of steps. DFS has no such guarantee — it just finds *some* path.
- **States explored:** BFS may explore fewer states if the goal is close to the start, or more states if it must fan out widely. In our experiments, BFS explored fewer states overall (77 vs 194 for maze2).

### Q3. Is DFS guaranteed to find the shortest path? Why or why not?

**No.** DFS is not guaranteed to find the shortest path. Because it commits to going deep along one branch first, it may find a path that reaches the goal but takes many more steps than necessary. BFS is guaranteed to find the shortest path (in terms of number of moves) because it always expands the shallowest (closest) unexplored node first.

**Example from our lab:** In maze3, DFS found a 14-step path while BFS found a 6-step path — the same goal, but DFS took more than twice as many steps.

### Q4. How does the number of explored states differ between maze1.txt and maze2.txt?

| Maze  | DFS States | BFS States |
|-------|------------|------------|
| maze1 | 11         | 11         |
| maze2 | 194        | 77         |

maze2 has far more explored states because it is much larger (16×29 vs 6×7) with many more branching paths and dead ends. DFS's tendency to go deep means it explores many dead-end branches of maze2 before finding the solution. The exponentially larger search space of maze2 amplifies the difference between the two algorithms.

### Q5. What would happen if we removed the explored set check?

Without the explored set, the algorithm could revisit cells it has already been to. In a maze with any cycles or open areas (which most mazes have), this would create **infinite loops** — the algorithm keeps bouncing between the same cells forever, never terminating. The explored set is what ensures each cell is processed at most once, keeping the algorithm both correct and efficient.

---

## 🖼️ Image Color Legend

| Color       | Meaning          |
|-------------|------------------|
| 🔴 Red      | Start position A |
| 🟢 Green    | Goal position B  |
| 🟡 Yellow   | Solution path    |
| 🟠 Salmon   | Explored cells   |
| ⬛ Dark      | Walls            |
| ⬜ Light     | Open path        |

---

*CS3081 Artificial Intelligence — Lab 1 submission*
