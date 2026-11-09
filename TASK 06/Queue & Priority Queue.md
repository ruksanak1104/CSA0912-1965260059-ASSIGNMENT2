1) Problem Analysis

The hospital emergency department must treat patients according to two rules:

•	Priority rule: The patient with the lowest severity number must be treated first. Severity 1 is critical, while severity 5 is minor.

•	Arrival rule: If two or more patients have the same severity, the patient who arrived first must be treated first.

A normal FIFO queue is not sufficient because it serves patients only in arrival order, regardless of their severity.
Suitable data structure: Java PriorityQueue with a custom Comparator.

2) Why a Plain Queue Is Not Sufficient

•	A normal Queue follows First In, First Out (FIFO).

•	It serves the earliest arriving patient even if another patient with a more critical condition arrives later.

•	This does not satisfy the hospital's emergency priority rule.

•	Therefore, a PriorityQueue is more suitable because it selects the patient with the highest treatment priority.

3) PriorityQueue with a Suitable Comparator

The PriorityQueue must compare patients using two conditions:

1.	Compare severity in ascending order. Lower severity numbers receive higher priority.

2.	If severity is equal, compare arrival counters in ascending order. The smaller counter represents the earlier arrival.

The comparator is:

Comparator<Patient> patientOrder =

    Comparator.comparingInt((Patient p) -> p.severity)
    
              .thenComparingLong(p -> p.arrival);

This ensures that:

•	Severity 1 is treated before severity 2.

•	Patients with equal severity are treated in their arrival order.

4) How Arrival Order Is Preserved

•	A variable called counter is maintained in the Triage class.

•	Every time a patient arrives, the current counter value is assigned to that patient.

•	The counter is then increased by 1 using counter++.

•	Patients with equal severity are compared using their arrival counter.

•	The patient with the smaller counter value is treated first.

Example:

•	Meena: Severity 1, Arrival 0

•	Kumar: Severity 1, Arrival 1

Meena is treated before Kumar because both have the same severity, but Meena arrived earlier.

5) Correctness Check Using Six Patients

The supplied program contains six patients. Their treatment order can be verified manually:

1.	Meena (S1) arrives second, but has the highest severity priority.

2.	Kumar (S1) has the same severity as Meena but arrives later.

3.	Lata (S2) is treated after both severity-1 patients.

4.	Ravi (S3) arrived before John, so Ravi is treated first.

5.	John (S3) is treated after Ravi.

6.	Asha (S5) has the lowest treatment priority and is treated last.

Therefore, the program output matches the manually calculated order.

Displaying the waiting list:

•	queue.poll() removes and returns the highest-priority patient.

•	queue.peek() displays the next patient without removing it.

•	Printing the PriorityQueue directly does not guarantee treatment order. Its internal structure is a heap, not a fully sorted list.

•	To display patients in treatment order without changing the original queue, create a copy and repeatedly call poll() on the copy.

6) Growth Investigation and Time Complexity

Let n represent the number of patients currently waiting in the priority queue.

Predicted time complexity:

•	Adding a patient (queue.add()): O(log n)

o	The new patient is inserted into the heap.

o	The heap rearranges its elements to maintain priority order.

•	Removing the next patient (queue.poll()): O(log n)

o	The root patient is removed.

o	The last element is moved to the root and the heap is rearranged.

•	Viewing the next patient (queue.peek()): O(1)

o	It returns the highest-priority patient without removing them.

•	Displaying all patients in treatment order: O(n log n) using a copied priority queue and repeated poll() operations.

•	Space complexity: O(n), because the queue stores all waiting patients.

Three meaningful queue sizes for experimentation:

•	100 patients

•	1,000 patients

•	10,000 patients

Prediction before running the experiment:

•	At 100 patients, adding and removing patients should be very fast.

•	At 1,000 patients, the operations should take slightly more time.

•	At 10,000 patients, the operations should still scale efficiently because their complexity is O(log n), not O(n).

Java benchmark code:

Add this method inside the Triage class and call it from a separate test class or from main().

static void testPerformance(int n) {

    PriorityQueue<Patient> q = new PriorityQueue<>(
        Comparator.comparingInt((Patient p) -> p.severity)
                  .thenComparingLong(p -> p.arrival)
    );

    long start = System.nanoTime();

    // Add patients

    for (int i = 0; i < n; i++) {
        q.add(new Patient(
            "Patient" + i,
            (i % 5) + 1,
            i
        ));
    }

    long addTime = System.nanoTime() - start;

    start = System.nanoTime();

    // Remove all patients
    
    while (!q.isEmpty()) {
        q.poll();
    }

    long removeTime = System.nanoTime() - start;

    System.out.println("Patients: " + n);
    
    System.out.println("Adding time: " +
    
        addTime / 1_000_000.0 + " ms");
    
    System.out.println("Removing time: " +
    
        removeTime / 1_000_000.0 + " ms");
}

public static void main(String[] args) {
    testPerformance(100);
    testPerformance(1000);
    testPerformance(10000);
}


Expected observation:

•	Both adding and removing operations should become slower as the queue grows.

•	Their growth should broadly follow O(log n) per operation.

•	Actual timings must be recorded by executing the program. They may vary depending on the computer, Java version and system load.

•	For reliable measurements, run multiple trials and compare average times. A benchmark framework such as JMH is preferable for formal performance testing.

7) Starvation Problem and Fairness Rule

What is starvation?

•	Starvation occurs when a patient with a low treatment priority waits for a very long time because higher-priority patients continuously arrive.

•	For example, a severity-5 patient may keep waiting if new severity-1, severity-2 or severity-3 patients repeatedly arrive.

•	This can cause unfair treatment and may allow a patient's condition to worsen.

Proposed fair rule: Priority Aging

•	Introduce a waiting-time limit, such as 30 minutes.

•	If a patient waits longer than 30 minutes, increase their treatment priority.

•	For example, a severity-5 patient's effective priority can improve to severity 4 after the waiting limit.

•	Further waiting can gradually improve their priority, subject to clinical safety rules.

•	The hospital should regularly reassess patients because their medical condition may change.

Implementation idea:

•	Store the patient's arrival time.

•	Calculate the waiting duration using the current time minus arrival time.

•	Adjust the patient's effective priority when the waiting-time threshold is reached.

•	Rebuild or update the priority queue whenever priorities change, because Java's PriorityQueue does not automatically reorder an element when a field used by its 
comparator changes.

The aging rule should be approved by medical staff. A waiting-time rule must never delay a genuinely critical patient.

8) Alternative Queue Implementations

A. LinkedList maintained in sorted order

•	Patients are inserted at the correct position based on severity and arrival order.

•	The first patient in the list is always the next patient to be treated.

•	Adding a patient requires finding the correct position and inserting the patient.

•	Removing the first patient is O(1).

•	Adding a patient is O(n) because the list may need to be traversed.

•	Space complexity is O(n) because each patient is stored in a linked-list node.

B. Array of Five FIFO Queues

•	Create five separate FIFO queues, one for each severity level.

•	Queue 1 stores severity-1 patients, queue 2 stores severity-2 patients, and so on.

•	When a patient arrives, insert them into the queue matching their severity.

•	To treat the next patient, check the queues in order from severity 1 to severity 5.

•	Remove the first patient from the first non-empty queue.

•	Adding a patient is O(1).

•	Removing the next patient is O(1), because there are only five queues to check.

•	Space complexity is O(n), excluding the fixed overhead of the five queues.

•	FIFO ordering automatically preserves arrival order among patients with equal severity.

C. Complexity comparison

•	PriorityQueue

o	Add: O(log n)

o	Remove next: O(log n)

o	Space: O(n)

•	Sorted LinkedList

o	Add: O(n)

o	Remove next: O(1)

o	Space: O(n)

•	Five FIFO Queues

o	Add: O(1)

o	Remove next: O(1), because only five queues are checked

o	Space: O(n)

The five-queue approach is particularly efficient when there are exactly five fixed severity levels. The PriorityQueue approach is more flexible when the priority rules may become more complex.

9) Deployment Decision for a City Hospital

Suitable with proper safeguards

The proposed PriorityQueue design can handle a hospital receiving 5,000 patients per day.

Reasons:

•	A PriorityQueue supports insertion and removal in O(log n) time.

•	Even if thousands of patients are waiting, adding and serving patients remain efficient.

•	The queue requires O(n) space, which is manageable for 5,000 patient records.

•	The arrival counter preserves fairness among patients with equal severity.

•	Priority aging can reduce the risk of lower-severity patients waiting indefinitely.

Additional deployment considerations:

•	Use a thread-safe design or suitable synchronization if multiple hospital staff members admit and remove patients simultaneously.

•	Store patient details and arrival times reliably.

•	Reassess patient severity regularly.

•	Apply priority-aging rules approved by medical professionals.

•	Maintain a separate patient database for medical records and treatment history.

Final decision: A PriorityQueue is suitable for a city hospital because it provides efficient priority-based admission and treatment. However, real hospital deployment also requires concurrency control, patient reassessment and starvation prevention.

10) Conclusion

•	A normal FIFO queue cannot handle emergency severity priorities correctly.

•	A PriorityQueue with a custom comparator serves the lowest severity number first.

•	The arrival counter ensures that patients with equal severity are treated in arrival order.

•	Adding and removing patients take O(log n) time.

•	A sorted LinkedList and five separate FIFO queues are alternative solutions.

•	Priority aging improves fairness by considering how long a patient has waited.

Final recommendation: Use a PriorityQueue with a severity-and-arrival comparator, along with a clinically approved aging policy, for a flexible and efficient 
emergency triage system.

Small note for the correctness check: The supplied program has six patients and produces the exact expected output. To test eight patients, add:

er.admit("Nila", 2);

er.admit("Arun", 4);

The expanded expected output becomes:

Meena(S1) Kumar(S1) Lata(S2) Nila(S2) Ravi(S3) John(S3) Arun(S4) Asha(S5)

This also verifies that arrival order is preserved when two patients have the same severity.










