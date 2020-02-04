```java
import java.util.Collections;
import java.util.LinkedList;

import edu.princeton.cs.algs4.In;
import edu.princeton.cs.algs4.MinPQ;
import edu.princeton.cs.algs4.StdOut;

public class Solver {
    private class Node implements Comparable<Node> {
        private final Board board;
        private final Node preNode;
        private final int steps;
        private final int priority;

        public Node(Board xboard, Node xpreNode, int xstep) {
            board = xboard;
            preNode = xpreNode;
            steps = xstep;
            priority = board.manhattan() + steps;
        }

        @Override
        public int compareTo(Node that) {
            if (priority < that.priority) return -1;
            else if (priority == that.priority) return 0;
            else return 1;
        }
    }
    private final boolean isSolvable;
    private final LinkedList<Board> solution;

    // find a solution to the initial board (using the A* algorithm)
    public Solver(Board initial) {
        if (initial == null) {
            throw new IllegalArgumentException();
        }
        solution = new LinkedList<>();

        Node presentNode1 = new Node(initial, null, 0);
        MinPQ<Node> gameTree1 = new MinPQ<>();
        gameTree1.insert(presentNode1);

        Node presentNode2 = new Node(initial.twin(), null, 0);
        MinPQ<Node> gameTree2 = new MinPQ<>();
        gameTree2.insert(presentNode2);

        do {
            presentNode1 = gameTree1.delMin();
            presentNode2 = gameTree2.delMin();
            if (presentNode1.board.isGoal() || presentNode2.board.isGoal()) break;
            for (Board neighbors : presentNode1.board.neighbors()) {
                if (presentNode1.preNode != null && presentNode1.preNode.board.equals(neighbors)) continue;
                gameTree1.insert(new Node(neighbors, presentNode1, presentNode1.steps + 1));
            }
            for (Board neighbors : presentNode2.board.neighbors()) {
                if (presentNode2.preNode != null && presentNode2.preNode.board.equals(neighbors)) continue;
                gameTree2.insert(new Node(neighbors, presentNode2, presentNode2.steps + 1));
            }
        } while (!gameTree1.isEmpty());
        if (presentNode1.board.isGoal()) {
            isSolvable = true;
            while (presentNode1 != null) {
                solution.addFirst(presentNode1.board);
                presentNode1 = presentNode1.preNode;
            }
        }
        else {
            isSolvable = false;
        }
    }

    // is the initial board solvable? (see below)
    public boolean isSolvable() {
        return isSolvable;
    }

    // min number of moves to solve initial board
    public int moves() {
        return solution.size() - 1;
    }

    // sequence of boards in a shortest solution
    public Iterable<Board> solution() {
        if (!isSolvable) return null;
        return Collections.unmodifiableList(solution);
    }

    // test client (see below) 
    public static void main(String[] args) {
        // create initial board from file
        In in = new In(args[0]);
        int n = in.readInt();
        int[][] tiles = new int[n][n];
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                tiles[i][j] = in.readInt();
         Board initial = new Board(tiles);

        // solve the puzzle
        Solver solver = new Solver(initial);

        // print solution to standard output
        if (!solver.isSolvable())
            StdOut.println("No solution possible");
        else {
            StdOut.println("Minimum number of moves = " + solver.moves());
            for (Board board : solver.solution())
                StdOut.println(board);
        }
    }
}
```
