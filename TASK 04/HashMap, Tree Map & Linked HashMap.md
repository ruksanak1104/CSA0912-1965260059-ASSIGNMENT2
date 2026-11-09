1) Problem Analysis

A hospital pharmacy maintains medicine stock details using a unique medicine code.
Pharmacists frequently search for medicines using their codes. At the end of each day, the system generates two reports:

1.	Code-order report: Medicines arranged in ascending order of medicine codes.

2.	Insertion-order report: Medicines displayed in the order in which they were added.

Three Java Map implementations are considered:

•	HashMap

•	TreeMap

•	LinkedHashMap

Each map has different performance characteristics and ordering behaviour.

2) Expected Performance Before Implementation

1. HashMap

•	put(): O(1) average time.

•	get(): O(1) average time.

•	Iteration: O(n) in terms of entries, with additional bucket-capacity overhead.

•	Does not maintain insertion order or sorted order.

•	Requires sorting when a code-order report is generated.

2. TreeMap

•	put(): O(log n).

•	get(): O(log n).

•	Iteration: O(n).

•	Automatically maintains medicines in ascending key order.

•	Naturally produces the code-order report.

•	Requires extra sorting to produce insertion order, which cannot be recovered from the map alone.

3. LinkedHashMap
•	put(): O(1) average time.

•	get(): O(1) average time.

•	Iteration: O(n).

•	Maintains insertion order.

•	Naturally produces the insertion-order report.

•	Requires sorting when a code-order report is generated.

3) Which Map Produces Each Report?

A. Code-Order Report

•	TreeMap produces the report directly because it maintains keys in sorted order.

•	HashMap requires copying the entries and sorting them by medicine code.

•	LinkedHashMap also requires sorting the entries by medicine code.

B. Insertion-Order Report

•	LinkedHashMap produces the report directly because it maintains insertion order.

•	HashMap does not guarantee insertion order.

•	TreeMap sorts medicines by code and does not preserve insertion order.

Extra Work When the Wrong Map Is Chosen:

•	HashMap: Sorting is required for both reports, and insertion order cannot be reconstructed reliably.

•	TreeMap: Insertion order must be stored separately if required.

•	LinkedHashMap: Sorting is required for the code-order report.

4) Design of Experiments

Three different inventory sizes are selected to compare the performance.

•	Experiment 1: 100 medicines.

•	Experiment 2: 10,000 medicines.

•	Experiment 3: 100,000 medicines.

The following operations are measured:

1.	Inserting medicines using put().

2.	Performing 100,000 lookups using get().

3.	Producing the code-order report.

4.	Producing the insertion-order report.

Expected Observations:

•	HashMap is expected to provide fast lookups.

•	TreeMap is expected to take more time for insertion and lookup because of tree traversal.

•	LinkedHashMap is expected to provide fast lookups while maintaining insertion order.

•	TreeMap should produce the code-order report without additional sorting.

•	LinkedHashMap should produce the insertion-order report without additional sorting.

5) Scalability Inquiry

An acceptable response time for one medicine lookup is defined as less than 1 millisecond under normal operating conditions.

Expected deployment behaviour:

•	HashMap provides fast average lookup performance.

•	LinkedHashMap provides similarly efficient lookup while maintaining insertion order.

•	TreeMap provides predictable O(log n) lookup performance.

•	The remaining cost comes from hashing, key comparisons, memory access, resizing and report generation.



Why Hashtable Is Not Preferred:

1.	Hashtable is a legacy Java collection.

2.	Its methods are synchronized, which can introduce unnecessary overhead.

3.	It does not allow null keys or null values.

4.	Modern alternatives such as HashMap, TreeMap and LinkedHashMap provide more flexible collection management.

Recommendation: LinkedHashMap is a suitable choice when the pharmacy requires both fast stock lookups and insertion-order reports. A sorted copy can be created 
when the code-order report is required.

If code-order reporting is the most important requirement, TreeMap is another suitable choice.

6) Alternative Concept and Complexity Analysis

A sorted array of medicines can also be used instead of a Map.

Using Arrays.binarySearch():

•	Medicines are stored in an array sorted by medicine code.

•	Binary search is used to find a medicine using its code.

•	New medicines are inserted at their correct sorted positions.

•	The report can be generated directly by traversing the sorted array.

Time Complexity:

1.	Lookup: O(log n), because binary search repeatedly divides the search range.

2.	Insertion: O(n), because elements must be shifted to maintain sorted order.

3.	Code-order report: O(n), because medicines are already sorted.

4.	Space: O(n), because an array stores n medicine references.

Comparison:

•	HashMap: O(1) average lookup, O(n log n) for generating a sorted report, O(n) space.

•	TreeMap: O(log n) lookup, O(n) code-order iteration, O(n) space.

•	LinkedHashMap: O(1) average lookup, O(n) insertion-order iteration, O(n) space.

•	Sorted Array: O(log n) lookup, O(n) insertion and O(n) code-order reporting, O(n) space.

7) Conclusion

HashMap, TreeMap and LinkedHashMap provide different advantages for pharmacy inventory management.

•	HashMap is suitable for fast medicine stock lookup.

•	TreeMap is suitable for maintaining medicines in sorted code order.

•	LinkedHashMap is suitable for maintaining insertion order with efficient lookup.

For the hospital pharmacy, LinkedHashMap is a suitable overall choice because it supports fast lookups and preserves the order in which medicines were added. A sorted copy can be generated for the daily code-order report.




















