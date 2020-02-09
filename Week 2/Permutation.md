```java
import edu.princeton.cs.algs4.StdIn;
import edu.princeton.cs.algs4.StdOut;
public class Permutation {
    public static void main(String[] args) {
        int k = Integer.parseInt(args[0]);
        String currentString = "";
        if (k == 0) {
            return;
        }
        RandomizedQueue<String> stringList1 = new RandomizedQueue<>();
        while (!StdIn.isEmpty()) {
            currentString = StdIn.readString();
            stringList1.enqueue(currentString);
        }
        for (int i = 0; i < k; ++i) {
            StdOut.println(stringList1.dequeue());
        }
    }
 }
```
