### Code Explanation

This C++ program implements a Breadth-First Search (BFS) to solve an 8-puzzle problem. The 8-puzzle is a sliding puzzle that consists of a 3x3 grid with tiles numbered 1 through 8 and one empty space.

Here's a breakdown of the code:

#### **Includes and Constants**
```cpp
#include <iostream>
#include <cstring>
#include <queue>
#include <stack>
using namespace std;

const int SIZE = 3;
```
- **Headers**: Includes necessary libraries.
- **SIZE**: Defines the size of the puzzle (3x3).

#### **Struct and Global Variables**
```cpp
struct MapNode {
    int state[SIZE][SIZE];
    int level;
    char action;
    MapNode* parent;
};

char ACTION_STRING[5][15] = {"", " move up,    ", " move down,  ", " move left,  ", " move right, "};
char VISITED[1000000000];
MapNode TREE[2000000];
int COUNT = 0, TotalNode = 0;
```
- **MapNode**: Represents a node in the search tree, including the puzzle state, level (depth), action taken, and a pointer to the parent node.
- **ACTION_STRING**: Stores descriptive strings for the moves.
- **VISITED**: Array to track visited states.
- **TREE**: Array to store the nodes of the search tree.
- **COUNT** and **TotalNode**: Counters for visited and generated nodes.

#### **Helper Functions**
```cpp
void printPuzzle(int puzzle[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            cout << puzzle[i][j] << " ";
        }
        cout << endl;
    }
}

bool isGoalState(int puzzle[SIZE][SIZE], int goal[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (puzzle[i][j] != goal[i][j]) {
                return false;
            }
        }
    }
    return true;
}

bool isValidMove(int x, int y) {
    return (x >= 0 && x < SIZE && y >= 0 && y < SIZE);
}
```
- **printPuzzle**: Prints the current state of the puzzle.
- **isGoalState**: Checks if the current state matches the goal state.
- **isValidMove**: Checks if a move is within the puzzle boundaries.

#### **BFS Function**
```cpp
bool bfs(MapNode initial, int goal[SIZE][SIZE]) {
    queue<MapNode> q;
    q.push(initial);
    
    while (!q.empty()) {
        MapNode node = q.front();
        q.pop();
        
        int sum = 0;
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                sum *= 10;
                sum += node.state[i][j];
            }
        }
        
        if (!VISITED[sum]) {
            VISITED[sum] = true;
        } else {
            continue;
        }
        
        TREE[COUNT] = node;
        
        if (isGoalState(node.state, goal)) {
            return true;
        }
        
        int x, y;
        for (x = 0; x < SIZE; x++) {
            for (y = 0; y < SIZE; y++) {
                if (node.state[x][y] == 0) {
                    break;
                }
            }
            if (y < SIZE && node.state[x][y] == 0) {
                break;
            }
        }
        
        int moveX[] = {1, -1, 0, 0};
        int moveY[] = {0, 0, 1, -1};
        
        for (int i = 0; i < 4; i++) {
            int newX = x + moveX[i];
            int newY = y + moveY[i];
            if (isValidMove(newX, newY)) {
                MapNode child = node;
                child.parent = &TREE[COUNT];
                child.action = i + 1;
                child.level++;
                swap(child.state[x][y], child.state[newX][newY]);
                q.push(child);
                TotalNode++;
            }
        }
        COUNT++;
    }
    return false;
}
```
- **bfs**: Implements BFS to find the solution to the puzzle.
  - Uses a queue to explore nodes level by level.
  - Tracks visited states to avoid revisits.
  - Checks for goal state.
  - Generates valid moves and adds new states to the queue.

#### **Solution Printing**
```cpp
void printSolution(stack<MapNode> s) {
    while (!s.empty()) {
        MapNode node = s.top();
        s.pop();
        cout << ACTION_STRING[node.action];
        printPuzzle(node.state);
    }
}
```
- **printSolution**: Prints the sequence of moves to reach the goal state.

#### **Main Function**
```cpp
int main() {
    memset(VISITED, 0, sizeof(char) * 1000000000);
    int initial[SIZE][SIZE];
    int goal[SIZE][SIZE];

    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            cin >> initial[i][j];
        }
    }
    
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            cin >> goal[i][j];
        }
    }
    
    MapNode initialNode;
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            initialNode.state[i][j] = initial[i][j];
        }
    }
    initialNode.level = 0;
    initialNode.parent = nullptr;
    initialNode.action = 0;

    if (bfs(initialNode, goal)) {
        stack<MapNode> solution;
        while (initialNode.parent != nullptr) {
            solution.push(initialNode);
            initialNode = *initialNode.parent;
        }
        solution.push(initialNode);
        cout << "find depth = " << solution.top().level << endl;
        cout << "total nodes(visited) = " << COUNT << endl;
        cout << "total nodes(generated) = " << TotalNode << endl;
        printSolution(solution);
    } else {
        cout << "No solution found." << endl;
    }
    
    return 0;
}
```
- **main**: Initializes the puzzle, reads input for initial and goal states, and starts the BFS.
- **Initialization**: Clears the VISITED array.
- **Reads**: Initial and goal states from standard input.
- **Starts BFS**: With the initial state.
- **Prints**: The solution if found, or indicates no solution exists.
