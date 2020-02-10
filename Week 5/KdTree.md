```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import edu.princeton.cs.algs4.Point2D;
import edu.princeton.cs.algs4.RectHV;
import edu.princeton.cs.algs4.StdDraw;

public class KdTree {
    private class Node {
        private Point2D point;
        private Node left;
        private Node right;
        private final boolean isVertical;
        private final RectHV rectangle;

        public Node(Point2D xpoint, boolean xisVertical, RectHV xrect) {
            point = xpoint;
            isVertical = xisVertical;
            rectangle = xrect;
        }

        public boolean letGoLeft(Point2D p) {
            if (isVertical) {
                return p.x() < point.x();
            }
            else {
                return p.y() < point.y();
            }
        }
    }
    private Node root;
    private int size;

    // construct an empty set of points
    public KdTree() { 
        size = 0;
    }

    private RectHV splitRect(Node n, Point2D p) {
        if (n.isVertical) {
            if (n.letGoLeft(p)) return new RectHV(n.rectangle.xmin(), n.rectangle.ymin(), n.point.x(), n.rectangle.ymax());
            else return new RectHV(n.point.x(), n.rectangle.ymin(), n.rectangle.xmax(), n.rectangle.ymax());
        }
        else {
            if (n.letGoLeft(p)) return new RectHV(n.rectangle.xmin(), n.rectangle.ymin(), n.rectangle.xmax(), n.point.y());
            else return new RectHV(n.rectangle.xmin(), n.point.y(), n.rectangle.xmax(), n.rectangle.ymax());
        }
    }

    // is the set empty?      
    public boolean isEmpty() {
        return root == null;
    }
    // number of points in the set                 
    public int size() {
        return size;
    }

    // add the point to the set (if it is not already in the set)
    public void insert(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        if (root == null) { 
            root = new Node(p, true, new RectHV(0, 0, 1, 1));
            ++size;
            return;
        }
        else {
            myInsert(p, root);
        }
    }
    private void myInsert(Point2D p, Node currentNode) {
        if (currentNode.letGoLeft(p)) {
            if (currentNode.left == null) {
                currentNode.left = new Node(p, !currentNode.isVertical, splitRect(currentNode, p));
                ++size;
                return;
            }
            else { myInsert(p, currentNode.left); }
        }
        else {
            if (currentNode.right == null) {
                currentNode.right = new Node(p, !currentNode.isVertical, splitRect(currentNode, p));
                ++size;
                return;
            }
            else { myInsert(p, currentNode.right); }
        }
    }

    // does the set contain point p?
    public boolean contains(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        return myContains(p, root);
    }
    private boolean myContains(Point2D p, Node currentNode) {
        if (currentNode == null) return false;
        if (p.equals(currentNode.point)) return true;
        if (currentNode.letGoLeft(p)) {
            return myContains(p, currentNode.left);
        }
        else {
            return myContains(p, currentNode.right);
        }
    }

    // draw all points to standard draw
    public void draw() {
        draw(root);
    }
    private void draw(Node n) {
        if (n == null) return;
        StdDraw.setPenRadius(0.01);
        n.point.draw();
        StdDraw.setPenRadius();
        drawLine(n);
        draw(n.left);
        draw(n.right);
    }
    private void drawLine(Node n) {
        if (n.isVertical) {
            StdDraw.setPenColor(StdDraw.RED);
            StdDraw.line(n.point.x(), n.rectangle.ymin(), n.point.x(), n.rectangle.ymax());
        }
        else {
            StdDraw.setPenColor(StdDraw.BLUE);
            StdDraw.line(n.rectangle.xmin(), n.point.y(), n.rectangle.xmax(), n.point.y());
        }
        StdDraw.setPenColor();
    }

    // all points that are inside the rectangle (or on the boundary)
    public Iterable<Point2D> range(RectHV rect) {
        if (rect == null) {
            throw new IllegalArgumentException();
        }
        List<Point2D> pointList = new ArrayList<>();
        myRange(root, rect, pointList);
        return Collections.unmodifiableCollection(pointList);
    }
    private void myRange(Node n, RectHV rect, List<Point2D> list) {
        if (n == null) return;
        if (rect.contains(n.point)) {
            list.add(n.point);
        }
        if (n.left != null && rect.intersects(n.left.rectangle)) {
            myRange(n.left, rect, list);
        }
        if (n.right != null && rect.intersects(n.right.rectangle)) {
            myRange(n.right, rect, list);
        }
    }

    // a nearest neighbor in the set to point p; null if the set is empty
    public Point2D nearest(Point2D p) {
        if (p == null) {
            throw new IllegalArgumentException();
        }
        Node result = new Node(null, false, null);
        if (root != null) result.point = root.point;
        myNearest(p, root, result);
        return result.point;
    }
    private void myNearest(Point2D p, Node currentNode, Node result) {
        if (currentNode == null) return;
        if (p.distanceSquaredTo(currentNode.point) < p.distanceSquaredTo(result.point)) {
            result.point = currentNode.point;
        }
        if (currentNode.letGoLeft(p)) {
            myNearest(p, currentNode.left, result);
            if (currentNode.right != null && currentNode.right.rectangle.distanceSquaredTo(p) < p.distanceSquaredTo(result.point)) {
                myNearest(p, currentNode.right, result);
            }
        }
        else {
            myNearest(p, currentNode.right, result);
            if (currentNode.left != null && currentNode.left.rectangle.distanceSquaredTo(p) < p.distanceSquaredTo(result.point)) {
                myNearest(p, currentNode.left, result);
            }
        }
    }
    // unit testing of the methods (optional)
    public static void main(String[] args) {
        Point2D[] points = new Point2D[3];
        KdTree st = new KdTree();
        points[0] = new Point2D(0.0, 1.0);
        points[1] = new Point2D(1.0, 0.0);
        points[2] = new Point2D(1.0, 1.0);
        for (Point2D p : points) {
            st.insert(p);
        }
        System.out.println(st.nearest(points[2]));
        System.out.println(st.size());
    }              
 }
 ```
