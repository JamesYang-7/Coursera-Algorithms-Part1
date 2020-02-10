```java
import edu.princeton.cs.algs4.Point2D;
import edu.princeton.cs.algs4.RectHV;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.TreeSet;

public class PointSET {
    private final TreeSet<Point2D> pointTree;
    // construct an empty set of points
    public PointSET() {
        pointTree = new TreeSet<>();
    }
    // is the set empty?              
    public boolean isEmpty() {
        return pointTree.isEmpty();
    }
    // number of points in the set                
    public int size() {
        return pointTree.size();
    }
    // add the point to the set (if it is not already in the set)
    public void insert(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        pointTree.add(p);
    }
    // does the set contain point p?
    public boolean contains(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        return pointTree.contains(p);
    }
    // draw all points to standard draw
    public void draw() {
        for (Point2D p : pointTree) {
            p.draw();
        }
    }
    // all points that are inside the rectangle (or on the boundary)
    public Iterable<Point2D> range(RectHV rect) {
        if (rect == null) {
            throw new IllegalArgumentException();
        }
        List<Point2D> pointList = new ArrayList<>();
        for (Point2D p : pointTree) {
            if (rect.contains(p)) {
                pointList.add(p);
            }
        }
        return Collections.unmodifiableCollection(pointList);
    }
    // a nearest neighbor in the set to point p; null if the set is empty
    public Point2D nearest(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        double nearestDistance = Double.POSITIVE_INFINITY;
        double d = 0;
        Point2D nearestPoint = null;
        for (Point2D pointx : pointTree) {
            d = p.distanceSquaredTo(pointx);
            if (d < nearestDistance) {
                nearestDistance = d;
                nearestPoint = pointx;
            }
        }
        return nearestPoint;
    }
    // unit testing of the methods (optional)
    public static void main(String[] args) {
        Point2D[] points = new Point2D[3];
        PointSET st = new PointSET();
        points[0] = new Point2D(0.3, 0.4);
        points[1] = new Point2D(0.8, 0.6);
        points[2] = new Point2D(0.4, 0.9);
        System.out.println("tree1 is empty? : " + st.isEmpty());
        st.insert(points[0]);
        System.out.println("// tree1.insert(points[0]);");
        System.out.println("tree1 is empty? : " + st.isEmpty());
        System.out.println("tree1.size() : " + st.size());
        st.insert(points[1]);
        System.out.println("// tree1.insert(points[1]);");
        System.out.println("tree1 contains points[2]? : " + st.contains(points[2]));
        st.insert(points[2]);
        System.out.println("// tree1.insert(points[2]);");
        System.out.println("tree1.size() : " + st.size());
        System.out.println("tree1 contains points[2]? : " + st.contains(points[2]));
        for (Point2D p : st.range(new RectHV(0, 0, 1, 1))) {
            System.out.println("rect contains " + p);
        }
        System.out.println("nearest to points[1] : " + st.nearest(points[1]));
    }
}
```
