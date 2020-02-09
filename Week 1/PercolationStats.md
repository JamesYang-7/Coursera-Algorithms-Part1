```java
import edu.princeton.cs.algs4.StdRandom;
import edu.princeton.cs.algs4.StdStats;
import edu.princeton.cs.algs4.StdOut;

public class PercolationStats {
    private static final double CONFIDENCE_95 = 1.96;
    private final int size;
    private final int t;
    private final double[] thresholds;

    // perform independent trials on an n-by-n grid
    public PercolationStats(int n, int trials) {
        if (n < 0 || trials < 0) {
            throw new IllegalArgumentException();
        }
        size = n;
        t = trials;
        thresholds = new double[t];
        for (int i = 0; i < t; ++i) {
            thresholds[i] = computeThreshold();
        }
    }

    private double computeThreshold() {
        int index;
        int row;
        int col;
        Percolation grids = new Percolation(size);
        while (!grids.percolates()) {
            index = StdRandom.uniform(size*size);
            row = index / size + 1;
            col = index % size + 1;
            grids.open(row, col);
        }
        return (double) grids.numberOfOpenSites() / (size * size);
    }

    // sample mean of percolation threshold
    public double mean() {
        return StdStats.mean(thresholds);
    }

    // sample standard deviation of percolation threshold
    public double stddev() {
        return StdStats.stddev(thresholds);
    }

    // low endpoint of 95% confidence interval
    public double confidenceLo() {
        return mean()-CONFIDENCE_95*stddev()/Math.sqrt(t);
    }

    // high endpoint of 95% confidence interval
    public double confidenceHi() {
        return mean()+CONFIDENCE_95*stddev()/Math.sqrt(t);
    }

    // test client (see below)
    public static void main(String[] args) {
        PercolationStats grids = new PercolationStats(Integer.parseInt(args[0]), Integer.parseInt(args[1]));
        StdOut.println("mean                    = " + grids.mean());
        StdOut.println("stddev                  = " + grids.stddev());
        StdOut.println("95% confidence interval = [" + grids.confidenceLo() + ", " + grids.confidenceHi() + "]");
    }

}
```
