```java
import edu.princeton.cs.algs4.WeightedQuickUnionUF;
public class Percolation {
    private final int size;
    private int numberOfOpenSite;
    private boolean[] openSites;
    private WeightedQuickUnionUF wqu1;
    
    // creates n-by-n grid, with all sites initially blocked
    public Percolation(int n) {
        if (n <= 0) {
            throw new IllegalArgumentException();
        }
        int n2 = n*n+2;
        this.size = n;
        openSites = new boolean[n2];
        wqu1 = new WeightedQuickUnionUF(n2);
        for (int i = 0; i < n2; ++i) {
            openSites[i] = false;
        }
    }

    private int getIndex(int row, int col) {
        if (row < 1 || row > size || col < 1 || col > size) {
            throw new IllegalArgumentException();
        }
        else {
            return ((row-1)*size+col);
        }
    }

    // opens the site (row, col) if it is not open already
    public void open(int row, int col) {
        if (row <= 0 || row > size || col <= 0 || col > size) {
            throw new IllegalArgumentException();
        }
        int idx = getIndex(row, col);
        if (!openSites[idx]) {
            openSites[idx] = true;
//          connect with virtual sites
            if (row == 1) {
                wqu1.union(0, idx);
            }
            else if (row == size) {
                wqu1.union(size * size + 1, idx);
            }
//          connect with legal adjacent open sites
            if (row > 1 && openSites[getIndex(row-1, col)]) {
                wqu1.union(idx, getIndex(row-1, col));
            }
            if (row < size && openSites[getIndex(row+1, col)]) {
                wqu1.union(idx, getIndex(row+1, col));
            }
            if (col > 1 && openSites[getIndex(row, col-1)]) {
                wqu1.union(idx, getIndex(row, col-1));
            }
            if (col < size && openSites[getIndex(row, col+1)]) {
                wqu1.union(idx, getIndex(row, col+1));
            }
            ++numberOfOpenSite;
        }
    }

    // is the site (row, col) open?
    public boolean isOpen(int row, int col) {
        if (row < 1 || row > size || col < 1 || col > size) {
            throw new IllegalArgumentException();
        }
        return openSites[getIndex(row, col)];
    }

    // is the site (row, col) full?
    public boolean isFull(int row, int col) {
        if (row < 1 || row > size || col < 1 || col > size) {
            throw new IllegalArgumentException();
        }
        return (wqu1.find(0) == wqu1.find(getIndex(row, col)));
    }

    // returns the number of open sites
    public int numberOfOpenSites() {
        return numberOfOpenSite;
    }

    // does the system percolate?
    public boolean percolates() {
        return wqu1.find(0) == wqu1.find(size*size+1);
    }

    // test client (optional)
//    public static void main(String[] args) {
//    }
}
```
