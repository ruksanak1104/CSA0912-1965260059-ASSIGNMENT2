1) Problem Analysis

A college fest uses a student attendance system to record students entering through different gates. The same student may be scanned multiple times.
The system uses HashSet<Student> to store unique student records and prevent duplicate attendance entries.

Each student has two attributes:

•	Registration number (regNo)

•	Student name (name)

The equals() method compares students using their registration numbers. However, the developer has not overridden hashCode().
This creates a problem because HashSet uses both hashCode() and equals() to identify duplicate objects.



2) Design of Three Tests

Three different tests are designed to investigate the behaviour of HashSet.

Test 1: Scanning the Same Student Twice

Input:

•	Student 1: 192311001, Arun

•	Student 2: 192311002, Divya

•	Student 3: 192311001, Arun

Prediction:

•	Expected set size: 2.

•	The first and third students have the same registration number.

•	According to equals(), both students are equal.

•	However, without overriding hashCode(), they may be placed in different hash buckets.

Reason: The result depends on their generated hash codes. HashSet may fail to identify the duplicate.

Test 2: Multiple Duplicate Scans

Input:

•	Student 1: 192311001, Arun

•	Student 2: 192311001, Arun

•	Student 3: 192311001, Arun

•	Student 4: 192311002, Divya

•	Student 5: 192311002, Divya

Prediction:

•	Expected set size: 2.

•	All students with the same registration number should be considered equal.

•	Without a proper hashCode(), duplicate objects may be stored in different buckets.

Reason: Different objects may receive different default hash codes even when their registration numbers are identical.


Test 3: Changing Registration Number After Insertion

Input:

1.	Create a student with registration number 192311001.

2.	Add the student to the HashSet.

3.	Change the registration number to 192311002.

4.	Check whether the student can be found in the set.

Prediction:

•	The HashSet may fail to locate the student after modification if the changed field affects its hash code.

•	This happens because the object may remain in the bucket selected using its original hash code.

Reason: Changing a field used in equals() or hashCode() after insertion can violate the expected behaviour of hash-based collections.


4. Comparison of Prediction and Experimental Results

Test 1: Same Student Scanned Twice

•	Prediction: 2 unique students.

•	Without hashCode(): Commonly 3 entries.

•	With hashCode(): 2 entries.

•	Reason: The corrected implementation identifies duplicate students properly.

Test 2: Multiple Duplicate Scans

•	Prediction: 2 unique students.

•	Without hashCode(): Duplicate entries may appear.

•	With hashCode(): 2 entries.

•	Reason: Equal registration numbers produce equal hash codes, allowing HashSet to detect duplicates.

Test 3: Changing Registration Number

•	Prediction: The student may not be found after changing the registration number.

•	Result: HashSet lookup can fail because the object's hash code has changed.

•	Reason: HashSet stores the object in the bucket determined by its original hash code.

5. Why Does HashSet Use Both hashCode() and equals()?

HashSet uses two methods to identify duplicate objects.

1. hashCode()

•	Calculates an integer hash value for an object.

•	Helps HashSet determine the bucket where the object should be stored.

•	Objects that are equal must produce the same hash code.

2. equals()

•	Compares two objects to check whether they are logically equal.

•	HashSet uses it to identify duplicates among objects with matching hash codes.

HashSet Working Process:

1.	A new student object is added.

2.	HashSet calculates its hash code.

3.	It identifies the appropriate bucket.

4.	It checks existing objects in that bucket using equals().

5.	If an equal object exists, the new object is rejected.

6.	Otherwise, the new object is stored.

Important Contract:

•	If two objects are equal according to equals(), they must have the same hashCode().

•	Two objects with the same hash code do not necessarily have to be equal.

•	Both methods must be implemented consistently.


6. What Happens When a Field Used in hashCode() Changes?

When an object is inserted into HashSet, its hash code determines its storage bucket.

If a field used in hashCode() changes after insertion:

1.	The object's hash code changes.

2.	HashSet may still store the object in its original bucket.

3.	Searching using the modified object may calculate a different bucket.

4.	The object may not be found even though it is present in the set.

Solution:

•	Make fields used in equals() and hashCode() immutable.

•	Use final for the registration number.

•	If modification is necessary, remove the object before changing the field and then insert it again.

In the corrected program, regNo is declared final, so it cannot be changed after object creation.

7. Alternative Concept and Complexity Analysis

Instead of HashSet<Student>, two alternative approaches can be used.

A. Using TreeSet with Comparator

TreeSet stores unique students in sorted order based on their registration numbers.

Java Code:

import java.util.*;

class Student {
    
    String regNo;
    
    String name;

    Student(String regNo, String name) {
    
        this.regNo = regNo;
        
        this.name = name;
    }
}

public class Attendance {
   
    public static void main(String[] args) {

    
        Set<Student> present = new TreeSet<>(
        
            (s1, s2) -> s1.regNo.compareTo(s2.regNo)
        );

        present.add(new Student("192311001", "Arun"));
       
        present.add(new Student("192311002", "Divya"));
        
        present.add(new Student("192311001", "Arun"));

        System.out.println("Unique students: " + present.size());
    }
}

Output:

Unique students: 2

Explanation:

•	TreeSet compares students using their registration numbers.

•	If the comparator returns 0, the new student is considered a duplicate.

•	It automatically maintains students in sorted order.

•	Each insertion takes O(log n) time.

B. Using HashMap with Registration Number as Key

HashMap stores registration numbers as keys and Student objects as values.

Java Code:

import java.util.*;

public class Attendance {
    
    public static void main(String[] args) {

        HashMap<String, String> students = new HashMap<>();

        students.put("192311001", "Arun");
       
        students.put("192311002", "Divya");
        
        students.put("192311001", "Arun");

        System.out.println("Unique students: " + students.size());
    }
}

Output:

Unique students: 2

Explanation:

•	Registration numbers are used as unique keys.

•	HashMap does not allow duplicate keys.

•	If the same registration number is inserted again, its existing value is replaced.

•	Average insertion and searching time is O(1).

C. Time Complexity Analysis

1. HashSet

•	Insertion: O(1) average.

•	Searching: O(1) average.

•	Space: O(n) for n unique students.

2. TreeSet

•	Insertion: O(log n).

•	Searching: O(log n).

•	Space: O(n), with additional tree-node references.

3. HashMap

•	Insertion: O(1) average.

•	Searching: O(1) average.

•	Space: O(n) for n unique students.

D. Space Complexity

•	HashSet: Stores n unique Student objects with hash-table bucket references.

•	TreeSet: Stores n unique Student objects in tree nodes containing additional references.

•	HashMap: Stores n registration-number keys and corresponding Student values.

All three structures require O(n) overall space.

8. Conclusion

The hashCode–equals contract is important when using hash-based collections such as HashSet and HashMap.

If two objects are equal according to equals(), they must produce the same hash code. Otherwise, duplicate objects may be stored.

Overriding hashCode() using the registration number solves the duplicate attendance problem.

Final Recommendation:

•	HashSet: Best for storing unique student objects.

•	TreeSet: Suitable when unique students must also be maintained in sorted order.

•	HashMap: Suitable when registration numbers are used as unique keys to retrieve student details.

Therefore, overriding both equals() and hashCode() correctly ensures reliable attendance de-duplication and efficient data management.




