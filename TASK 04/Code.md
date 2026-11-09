import java.util.*;

public class PharmacyBenchmark {

    static void test(int n) {

        Map<Integer, String> hashMap = new HashMap<>();
        Map<Integer, String> treeMap = new TreeMap<>();
        Map<Integer, String> linkedMap = new LinkedHashMap<>();

        // Insert medicines
        
        for (int i = n; i >= 1; i--) {
            hashMap.put(i, "Medicine" + i);
            treeMap.put(i, "Medicine" + i);
            linkedMap.put(i, "Medicine" + i);
        }

        int lookups = 100000;

        // HashMap lookup
       
        long start = System.nanoTime();
        
        for (int i = 0; i < lookups; i++) {
            hashMap.get((i % n) + 1);
        }
        
        long hashTime = System.nanoTime() - start;

        // TreeMap lookup
       
        start = System.nanoTime();
        
        for (int i = 0; i < lookups; i++) {
            treeMap.get((i % n) + 1);
        }
        
        long treeTime = System.nanoTime() - start;

        
        // LinkedHashMap lookup
        
        start = System.nanoTime();
        
        for (int i = 0; i < lookups; i++) {
            linkedMap.get((i % n) + 1);
        }
        
        long linkedTime = System.nanoTime() - start;

        // Code-order report
        
        start = System.nanoTime();
        
        Map<Integer, String> codeReport =
        
                new TreeMap<>(hashMap);
        
        long codeTime = System.nanoTime() - start;

        // Insertion-order report
       
        start = System.nanoTime();
        
        List<String> insertionReport =
        
                new ArrayList<>(linkedMap.values());
        
        long insertionTime = System.nanoTime() - start;

        System.out.println("\nMedicines: " + n);

        System.out.println("HashMap Lookup: "
        
                + hashTime / 1_000_000.0 + " ms");

        System.out.println("TreeMap Lookup: "
                
                + treeTime / 1_000_000.0 + " ms");

        System.out.println("LinkedHashMap Lookup: "
                
                + linkedTime / 1_000_000.0 + " ms");

        System.out.println("Code Report: "
                
                + codeTime / 1_000_000.0 + " ms");

        System.out.println("Insertion Report: "
                
                + insertionTime / 1_000_000.0 + " ms");

        System.out.println("Code order correct: "
                
                + isSorted(codeReport));

        System.out.println("Insertion order correct: "
                
                + insertionReport.get(0).equals(
                      
                        "Medicine" + n));
    }

    static boolean isSorted(Map<Integer, String> map) {
        int previous = 0;

        for (int key : map.keySet()) {
            if (key < previous)
                return false;
            previous = key;
        }

 return true;
 
    } 
 
 public static void main(String[] args) {

        test(100);
        test(10000);
        test(100000);
    }
}

