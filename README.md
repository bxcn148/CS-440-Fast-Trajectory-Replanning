Download link :https://programming.engineering/product/cs-440-fast-trajectory-replanning/

# CS-440-Fast-Trajectory-Replanning
CS 440 Fast Trajectory Replanning
Consider the following problem: an agent in a gridworld has to move from its current cell to the given cell of a non-moving target, where the gridworld is not fully known. They are discretizations of terrain into square cells that are either blocked or unblocked.

Similar search challenges arise frequently in real-time computer games, such as Starcraft shown in Figure 1, and robotics. To control characters in such games, the player can click on known or unknown terrain, and the game characters then move autonomously to the location that the player clicked on. The characters observe the terrain within their limited field of view and then remember it for future use but do not know the terrain initially (due to “fog of war”). The same situation arises in robotics, where a mobile platform equipped with sensors builds a map of the world as it traverses an unknown environment.

Figure 1: Fog of War in Starcraft

Assume that the initial cell of the agent is unblocked. The agent can move from its current cell in the four main compass directions (east, south, west and north) to any adjacent cell, as long as that cell is unblocked and still part of the gridworld. All moves take one time step for the agent and thus have cost one. The agent always knows which (unblocked) cell it is in and which (unblocked) cell the target is in. The agent knows that blocked cells remain blocked and unblocked cells remain unblocked but does not know initially which cells are blocked. However, it can always observe the blockage status of its four adjacent cells, which corresponds to its field of view, and remember this information for future use. The objective of the agent is to reach the target as effectively as possible.

A common-sense and tractable movement strategy for the agent is the following: The agent assumes that cells are unblocked unless it has already observed them to be blocked and uses search with the “freespace assumption”. In other words, it moves along a path that satisfies the following three properties:

It is a path from the current cell of the agent to the target.

It is a path that the agent does not know to be blocked and thus assumes to be unblocked, i.e., a presumed unblocked path.

It is a shortest such path.

Whenever the agent observes additional blocked cells while it follows its current path, it remembers this information for future use. If such cells block its current path, then its current path might no longer be a “shortest presumed-unblocked path” from the current cell of the agent to the target. Then, the agent stops moving along its current path, searches for another “shortest presumed-unblocked path” from its current cell to the target, taking into account the blocked cells that it knows about, and then moves along this path. The cycle stops when the agent:

either reaches the target or

determines that it cannot reach the target because there is no presumed-unblocked path from its current cell to the target and it is thus separated from the target by blocked cells.

In the former case, the agent reports that it reached the target. In the latter case, it reports that it cannot reach the target.

This movement strategy has two desirable properties:

The agent is guaranteed to reach the target if it is not separated from it by blocked cells.

The trajectory is provably short (but not necessarily optimal).

The trajectory is believable since the movement of the agent is directed toward the target and takes the blockage status of all observed cells into account but not the blockage status of any unobserved cell.

cell of the agent toward the target (= forward), resulting in Repeated Forward A*, or from the target toward the current cell of the agent (= backward), resulting in Repeated Backward A*.

Repeated Forward A*

procedure ComputePath()

while g(sgoal ) > mins02OPEN(g(s0) + h(s0))

remove a state s with the smallest f-value g(s) + h(s) from OPEN;

4CLOSED := CLOSED [ fsg;

5for all actions a 2 A(s)

if search(succ(s; a)) < counter

g(succ(s; a)) := 1;

search(succ(s; a)) := counter;

if g(succ(s; a)) > g(s) + c(s; a)

g(succ(s; a)) := g(s) + c(s; a);

tree(succ(s; a)) := s;

if succ(s; a) is in OPEN then remove it from OPEN;

insert succ(s; a) into OPEN with f-value g(succ(s; a)) + h(succ(s; a));

procedure Main()

counter := 0;

for all states s 2 S

search(s) := 0;

while sstart 6= sgoal

counter := counter + 1;

g(sstart ) := 0;

search(sstart ) := counter;

g(sgoal ) := 1;

search(sgoal ) := counter;

OPEN := CLOSED := ;;

insert sstart into OPEN with f-value g(sstart ) + h(sstart );

ComputePath();

if OPEN = ;

print “I cannot reach the target.”;

stop;

follow the tree-pointers from sgoal to sstart and then move the agent along the resulting path from sstart to sgoal until it reaches sgoal or one or more action costs on the path increase;

set sstart to the current state of the agent (if it moved);

update the increased action costs (if any);

print “I reached the target.”;

stop;

Figure 4: Pseudocode of Repeated Forward A*

The pseudocode of Repeated Forward A* is shown in Figure 4. It performs the A* searches in ComputePath(). A* is described in your textbook and therefore only briefly discussed in the following, using the following notation that can be used to describe general search problems rather than only search problems in gridworlds: S denotes the finite set of states. sstart 2 S denotes the start state of the A* search (which is the current state of the agent), and sgoal 2 S denotes the goal state of the A* search (which is the state of the target). A(s) denotes the finite set of actions that can be executed in state s 2 S. c(s; a) > 0 denotes the action cost of executing action a 2 A(s) in state s 2 S, and succ(s; a) 2 S denotes the resulting successor state. A* maintains five values for all states s that it encounters:

a g-value g(s) (which is infinity initially), which is the length of the shortest path from the start state to state s found by the A* search and thus an upper bound on the distance from the start state to state s;

an h-value (= heuristic) h(s) (which is user-supplied and does not change), which estimates the goal distance of state s (= the distance from state s to the goal state);

an f-value f(s) := g(s) + h(s), which estimates the distance from the start state via state s to the goal state;

a tree-pointer tree(s) (which is undefined initially), which is necessary to identify a shortest path after the A* search;

and a search-value search(s), which is described below.

A* maintains an open list (a priority queue which contains only the start state initially). A* identifies a state s with the smallest f-value in the open list [Line 2]. If the f-value of state s is no smaller than the g-value of the goal state, then the A* search is over. Otherwise, A* removes state s from the open list [Line 3] and expands it. We say that it expands state s when it inserts state s into the closed list (a set which is empty initially) [Line 4] and then performs the following operations for all actions that can be executed in state s and result in a successor state whose g-value is larger than the

g-value of state s plus the action cost [Lines 5-13]: First, it sets the g-value of the successor state to the g-value of state s plus the action cost [Line 10]. Second, it sets the tree-pointer of the successor state to (point to) state s [Line 11]. Finally, it inserts the successor state into the open list or, if it was there already, changes its priority [Line 12-13]. (We say that it generates a state when it inserts the state for the first time into the open list.) It then repeats the procedure.

Remember that h-values h(s) are consistent (= monotone) iff they satisfy the triangle inequalities h(sgoal ) = 0 and h(s) c(s; a) + h(succ(s; a)) for all states s with s 6= sgoal and all actions a that can be executed in state s. Consistent h-values are admissible (= do not overestimate the goal distances). A* search with consistent h-values has the following properties. Let g(s) and f(s) denote the g-values and f-values, respectively, after the A* search: First, the A* search expands all states at most once each. Second, the g-values of all expanded states and the goal state after the A* search are equal to the distances from start state to these states. Following the tree-pointers from these states to the start state identifies shortest paths from the start state to these states in reverse. Third, the f-values of the series of expanded states over time are monotonically nondecreasing. Thus, it holds that f(s) f(sgoal ) = g(sgoal ) for all states s that were expanded by the A* search (that is, all states in the closed list) and g(sgoal ) = f(sgoal ) f(s) for all states s that were generated by the A* search but remained unexpanded (that is, all states in the open list). Fourth, an A* search with consistent h-values h1(s) expands no more states than an otherwise identical A* search with consistent h-values h2(s) for the same search problem (except possibly for some states whose f-values are identical to the f-value of the goal state) if h1(s) h2(s) for all states s.

Repeated Forward A* itself executes ComputePath() to perform an A* search. Afterwards, it follows the tree-pointers from the goal state to the start state to identify a shortest path from the start state to the goal state in reverse. Repeated Forward A* then makes the agent move along this path until it reaches the target or action costs on the path increase [Line 30]. In the first case, the agent has reached the target. In the second case, the current path might no longer be a shortest path from the current state of the agent to the state of the target. Repeated Forward A* then updates the current state of the agent and repeats the procedure.

Repeated Forward A* does not initialize all g-values up front but uses the variables counter and search(s) to decide when to initialize them. The value of counter is x during the xth A* search, that is, the xth execution of ComputePath(). The value of search(s) is x if state s was generated last by the xth A* search (or is the goal state). The g-value of the goal state is initialized at the beginning of an A* search [Line 22] since it is needed to test whether the A* search should terminate [Line 2]. The g-values of all other states are initialized directly before they might be inserted into the open list [Lines 7 and 20] provided that state s has not yet been generated by the current A* search (search(s) < counter). The only initialization that Repeated Forward A* performs up front is to initialize search(s) to zero for all states s, which is typically automatically done when the memory is allocated [Lines 16-17].

Implementation Details

Your version of Repeated A* should use a binary heap to implement the open list. The reason for using a binary heap is that it is often provided as part of standard libraries and, if not, that it is easy to implement. At the same time, it is also reasonably efficient in terms of processor cycles and memory usage. You will get extra credit if you implement the binary heap from scratch, that is, if your implementation does not use existing libraries to implement the binary heap or parts of it. This will allow you to make connections to other classes and experience first hand how helpful the algorithms and data structure class that you once took can be. You can read up on binary heaps, for example, in Cormen, Leiserson and Rivest, Introduction to Algorithms, MIT Press, 2001.

Your version of Repeated A* should use the Manhattan distances as h-values. The Manhattan distance of a cell is the sum of the absolute difference of the x coordinates and the absolute difference of the y coordinates of the cell and the cell of the target. The reason for using the Manhattan distances is that they are consistent in gridworlds in which the agent can move only in the four main compass directions.

Your implementation of Repeated A* needs to be efficient in terms of processor cycles and memory usage since games place limitations on the resources that trajectory planning has available. Thus, it is important that you think carefully about your implementation rather than use the pseudocode from Figure 4 blindly since it is not optimized. (For example, the closed list in the pseudocode is shown only to allow us to refer to it later when explaining Adaptive A*.) Make sure that you never iterate over all cells except to initialize them once before the first A* search since your program might be used

A*. It first finds the shortest path from the current start state to the goal state according to the current action costs. It then updates the h-values of the states that were expanded by this search to make them larger and thus future A* searches more focused. Adaptive A* searches from the current state of the agent to the target since the h-values estimate the goal distances with respect to a given goal state. Thus, the goal state needs to remain unchanged, and the state of the target remains unchanged while the current state of the agent changes. Adaptive A* can handle action costs that increase over time.

To understand the principle behind Adaptive A*, assume that the action costs remain unchanged to make the description simple. Assume that the h-values are consistent. Let g(s) and f(s) denote the g-values and f-values, respectively, after an A* search from the current state of the agent to the target. Let s denote any state expanded by the A* search. Then, g(s) is the distance from the start state to state s since state s was expanded by the A* search. Similarly, g(sgoal) is the distance from the start state to the goal state. Thus, it holds that g(sgoal ) = gd(sstart ), where gd(s) is the goal distance of state s. Distances satisfy the triangle inequality:

gd(sstart )

g(s) + gd(s)

gd(sstart )

g(s)

gd(s)

g(sgoal )

g(s)

gd(s):

Thus, g(sgoal ) g(s) is an admissible estimate of the goal distance of state s that can be calculated quickly. It can thus be used as a new admissible h-value of state s. Adaptive A* therefore updates the h-values by assigning:

h(s) := g(sgoal ) g(s)

for all states s expanded by the A* search. Let hnew(s) denote the h-values after the updates.

The h-values hnew (s) have several advantages. They are not only admissible but also consistent. The next A* search with the h-values hnew (s) thus continues to find shortest paths without expanding states that have already been expanded by the current A* search. Furthermore, it holds that:

f(s) gd(sstart )

g(s) + h(s) g(sgoal )

h(s) g(sgoal ) g(s)

h(s) hnew (s)

since state s was expanded by the current A* search. Thus, the h-values hnew (s) of all expanded states s are no smaller than the immediately preceeding h-values h(s) and thus, by induction, also all previous h-values, including the user-supplied h-values. An A* search with consistent h-values h1(s) expands no more states than an otherwise identical A* search with consistent h-values h2(s) for the same search problem (except possibly for some states whose f-values are identical to the f-value of the goal state, a fact that we will ignore in the following) if h1(s) h2(s) for all states s. Consequently, the next A* search with the h-values hnew (s) cannot expand more states than with any of the previous h-values, including the user-supplied h-values. It therefore cannot be slower (except possibly for the small amount of runtime needed by the bookkeeping and h-value update operations), but will often expand fewer states and thus be faster. You can read up on Adaptive A* in Koenig and Likhachev, Adaptive A* [Poster Abstract], Proceedings of the International Joint Conference on Autonomous Agents and Multiagent Systems (AAMAS), 1311-1312, 2005.

Figure 7 shows the first two searches of Adaptive A* for the example search problem from Figure 2 (left). The number of cell expansions is smaller for Adaptive A* (20) than for Repeated Forward A* (23), demonstrating the advantage of Adaptive A* over Repeated Forward A*. Figure 7 (left) shows the first A* search of Adaptive A*. All cells have their h-values in the lower left corner. The goal distance of the current cell of the agent is eight. The lower right corners show the updated h-values after the h-values of all grey cells have been updated to eight minus their g-values, which makes it easy to compare them to the h-values before the h-value update in the lower left corners. Cells D2, E1, E2 and E3 have larger h-values than their Manhattan distances to the target, that is, the user-supplied h-values. Figure 7 (right) shows the second A* search of Adaptive A*, where Adaptive A* expands three cells (namely, cells E1, E2 and E3) fewer than Repeated Forward A*, as shown in Figure 5 (right).

Questions

This project argues that the agent is guaranteed to reach the target if it is not separated from it by blocked cells. Give a convincing argument that the agent in finite gridworlds indeed either reaches the target or discovers that this is impossible in finite time. Prove that the number of moves of the agent until it reaches the target or discovers that this is impossible is bounded from above by the number of unblocked cells squared.

Part 2 – The Effects of Ties [15 points]: Repeated Forward A* needs to break ties to decide which cell to expand next if several cells have the same smallest f-value. It can either break ties in favor of cells with smaller g-values or in favor of cells with larger g-values. Implement and compare both versions of Repeated Forward A* with respect to their runtime or, equivalently, number of expanded cells. Explain your observations in detail, that is, explain what you observed and give a reason for the observation.

[Hint: For the implementation part, priorities can be integers rather than pairs of integers. For example, you can use c f(s) g(s) as priorities to break ties in favor of cells with larger g-values, where c is a constant larger than the largest g-value of any generated cell. For the explanation part, consider which cells both versions of Repeated Forward A* expand for the example search problem from Figure 9.]

1 2 3 4 5

A

6A

6

10

5

4

5

6

B

6

10

5

8

4

8

3

8

5

4

3

4

5

C

6

10

5

8

4

6

3

6

2

6

4

3

2

3

4

D

7

10

6

8

3

3

2

4

1

4

3

2

3

3

2

3

E

8

10

7

8

8

8

1

1

0

2

2

1

0

1

1

2 T

Figure 9: Third Example Search Problem

Part 3 – Forward vs. Backward [20 points]: Implement and compare Repeated Forward A* and Repeated Backward A* with respect to their runtime or, equivalently, number of expanded cells. Explain your observations in detail, that is, explain what you observed and give a reason for the observation. Both versions of Repeated A* should break ties among cells with the same f-value in favor of cells with larger g-values and remaining ties in an identical way, for example randomly.

Part 4 – Heuristics in the Adaptive A* [20 points]: The project argues that “the Manhattan distances are consistent in gridworlds in which the agent can move only in the four main compass directions.” Prove that this is indeed the case.

Furthermore, it is argued that “The h-values hnew (s) … are not only admissible but also consistent.” Prove that Adaptive A* leaves initially consistent h-values consistent even if action costs can increase.

Part 5 – Heuristics in the Adaptive A* [15 points]: Implement and compare Repeated Forward A* and Adaptive A* with respect to their runtime. Explain your observations in detail, that is, explain what you observed and give a reason for the observation. Both search algorithms should break ties among cells with the same f-value in favor of cells with larger g-values and remaining ties in an identical way, for example randomly.

Part 6 – Statistical Significance [10 points]: Performance differences between two search algorithms can be systematic in nature or only due to sampling noise (= bias exhibited by the selected test cases since the number of test cases is always limited). One can use statistical hypothesis tests to determine whether they are systematic in nature. Read up on statistical hypothesis tests (for example, in Cohen, Empirical Methods for Artificial Intelligence, MIT Press, 1995) and then describe for one of the experimental questions above exactly how a statistical hypothesis test could be performed. You do not need to implement anything for this question, you should only precisely describe how such a test could be performed.
