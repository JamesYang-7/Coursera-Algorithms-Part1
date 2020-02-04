```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Board {
    private final int[][] tiles;
    private final int n;
    private final int hammingDistance;
    private final int manhattanDistance;

    public Board(int[][] tiles) {
        n = tiles.length;
        this.tiles = new int[n][];
        for (int i = 0; i < n; i++)
            this.tiles[i] = tiles[i].clone();
        hammingDistance = preHamming();
        manhattanDistance = preManhatten();
    }
                                           
    // string representation of this board
    public String toString() {
        StringBuilder s = new StringBuilder();
        s.append(n).append('\n');
        for (int[] row : tiles) {
            for (int k : row) {
                s.append(" ").append(k).append(" ");
            }
            s.append('\n');
        }
        return s.toString();
    }

    // board dimension n
    public int dimension() {
        return n;
    }

    // number of tiles out of place
    public int hamming() {
        return hammingDistance;
    }
    private int preHamming() {
        int number = 1;
        int distance = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (tiles[i][j] != number) ++distance;
                ++number;
            }
        }
        --distance;
        return distance;
    }

    // sum of Manhattan distances between tiles and goal
    public int manhattan() {
        return manhattanDistance;
    }
    private int preManhatten() {
        int number = 1;
        int row = 0;
        int col = 0;
        int distance = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (tiles[i][j] != number && tiles[i][j] != 0) {
                    row = (tiles[i][j] - 1)/n;
                    col = (tiles[i][j] - 1) % n;
                    distance += Math.abs(row - i) + Math.abs(col - j);
                }
                ++number;
            }
        }
        return distance;
    }

    // is this board the goal board?
    public boolean isGoal() {
        return hamming() == 0;
    }

    // does this board equal y?
    public boolean equals(Object y) {
        if (y == null) return false;
        if (this.getClass() != y.getClass()) return false;
        Board that = (Board) y;
        if (n != that.n) return false;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (tiles[i][j] != that.tiles[i][j]) return false;
            }
        }
        return true;
    }

    // all neighboring boards
    public Iterable<Board> neighbors() {
        int row = 0;
        int col = 0;
        int neighboringRow = 0;
        int neighboringCol = 0;
        boolean f = false;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (tiles[i][j] == 0) {
                    row = i;
                    col = j;
                    f = true;
                    break;
                }
            }
            if (f) break;
        }
        List<Board> neighbors = new ArrayList<>();
        int[][] dirs = {{0, -1}, {-1, 0}, {0, 1}, {1, 0}};
        for (int i = 0; i < dirs.length; ++i) {
            neighboringRow = row + dirs[i][0];
            neighboringCol = col + dirs[i][1];
            if (neighboringRow >= 0 && neighboringCol >= 0 && neighboringRow < n && neighboringCol < n) {
                exchange(row, col, neighboringRow, neighboringCol);
                neighbors.add(new Board(tiles));
                exchange(row, col, neighboringRow, neighboringCol);
            }
        }
        return Collections.unmodifiableList(neighbors);
    }
    
    private void exchange(int x1, int y1, int x2, int y2) {
        int t = tiles[x1][y1];
        tiles[x1][y1] = tiles[x2][y2];
        tiles[x2][y2] = t;
    }

    // a board that is obtained by exchanging any pair of tiles
    // exchange the first two elements in a row
    public Board twin() {
        int row = 0;
        if (tiles[0][0] == 0 || tiles[0][1] == 0) row = 1;
        exchange(row, 0, row, 1);
        Board twinBoard = new Board(tiles);
        exchange(row, 0, row, 1);
        return twinBoard;
    }

    // unit testing (not graded)
    public static void main(String[] args) {
        int[][] t = {{2, 1, 3}, {4, 5, 6}, {7, 8, 0}};
        // int [][] t = {{1, 2}, {0, 3}};
        Board board1 = new Board(t);
        Board board1Twin = board1.twin();
        System.out.println("board1:\n" + board1);
        System.out.println("board1 hamming distance:" + board1.hamming());
        System.out.println("board1 manhattan distance:" + board1.manhattan());
        System.out.println("board1 is goal? " + board1.isGoal());
        for (Board neighbor : board1.neighbors()) {
            System.out.println("neighbour :");
            System.out.println(neighbor);
        }
        System.out.println("board1Twin:\n" + board1Twin);
        System.out.println("board1Twin hamming distance:" + board1Twin.hamming());
        System.out.println("board1Twin manhattan distance:" + board1Twin.manhattan());
        System.out.println("board1Twin is goal? " + board1Twin.isGoal());
        System.out.println(board1Twin.equals(board1));
        System.out.println(board1.equals(board1Twin.twin()));
    }
}
```
