1.Problem Analysis

A music-streaming application allows users to create and manage playlists. Users can add songs, insert songs at specific positions, remove songs and access the k-th song directly.

The application currently uses ArrayList<Song> to store playlist information.

To determine whether ArrayList is the best choice, its performance must be compared with LinkedList and ArrayDeque under different operations.

2) Design of Experiments

Three different playlist sizes are selected to compare the performance of ArrayList and LinkedList.

•	Experiment 1: 100 songs (Small playlist).

•	Experiment 2: 10,000 songs (Medium playlist).

•	Experiment 3: 100,000 songs (Large playlist).

The following operations are tested:

1. Adding at the End

•	ArrayList: Adds songs at the end efficiently, with amortized O(1) time.

•	LinkedList: Adds songs at the end in O(1) time.

2. Inserting at the Front

•	ArrayList: Requires shifting existing elements, taking O(n) time.

•	LinkedList: Adds a new node at the beginning in O(1) time.

3. Inserting in the Middle

•	ArrayList: Requires shifting elements after the insertion position, taking O(n) time.

•	LinkedList: Requires traversal to find the position, taking O(n) time.

4. Removing from the Front

•	ArrayList: Requires shifting remaining elements, taking O(n) time.

•	LinkedList: Removes the first node in O(1) time.

5. Accessing the k-th Song

•	ArrayList: Provides direct index-based access in O(1) time.

•	LinkedList: Requires traversal from the beginning, taking O(n) time.

6. Iterating Through the Entire Playlist

•	ArrayList: Iterates efficiently using sequential array access.

•	LinkedList: Iterates by moving from one node to the next.

3. Expected Experimental Results

The following are the expected observations based on data structure characteristics.
- 100 songs:

•	ArrayList and LinkedList both perform efficiently for a small playlist.

•	The performance difference is generally small.
- 10,000 songs:

•	ArrayList performs better for random access.

•	LinkedList requires additional traversal for accessing elements.
- 100,000 songs:

•	ArrayList remains efficient for accessing songs by index.

•	LinkedList becomes slower for random access because of sequential traversal.

Overall observations:

•	ArrayList performs better for accessing songs by index.

•	LinkedList performs better for frequent insertion and removal at the front.

•	ArrayList is generally more memory-efficient because it does not require separate node objects and pointers.

•	LinkedList requires additional memory for storing node references.

Note: These are expected results. Actual execution times must be recorded by running the experiments.

4. When Is ArrayList an Appropriate Choice?

ArrayList is a reasonable engineering choice when:

1.	Songs are frequently accessed using their index.

2.	Most songs are added at the end of the playlist.

3.	The playlist requires frequent iteration.

4.	Insertions and deletions at the beginning or middle are relatively rare.

5.	Better memory efficiency and cache performance are required.

When to stop recommending ArrayList:

If the application frequently inserts and removes songs at the beginning or performs operations at both ends, ArrayList becomes less efficient because of repeated element shifting.

5. Changed Condition: Introduction of Up Next Queue

The application introduces an Up Next queue where songs are frequently added at both ends and removed from the front.

In this situation, ArrayDeque is a more appropriate choice.

Reasons:

1.	ArrayDeque supports insertion and removal at both ends in amortized O(1) time.

2.	It does not require shifting all existing elements.

3.	It is generally more efficient than LinkedList for queue operations.

4.	It uses a resizable circular array internally.

Recommendation:

•	Main playlist: ArrayList.

•	Up Next queue: ArrayDeque.

This combination provides efficient playlist access and queue management.

6. Alternative Concept and Complexity Analysis

A plain array can also be used to store playlist information. However, its size is fixed, and insertion or deletion requires manually shifting elements.


A. Time Complexity Comparison

1. Add at the End

•	ArrayList: O(1) amortized.

•	LinkedList: O(1).

•	ArrayDeque: O(1) amortized.

2. Insert at the Front

•	ArrayList: O(n), because existing elements must be shifted.

•	LinkedList: O(1), because a new node is added at the beginning.

•	ArrayDeque: O(1) amortized.

3. Insert in the Middle

•	ArrayList: O(n), because elements must be shifted.

•	LinkedList: O(n), because traversal is required to find the position.

•	ArrayDeque: O(n), because elements must be rearranged. It does not support direct indexed insertion.

4. Get the k-th Element

•	ArrayList: O(1), because it supports direct index access.

•	LinkedList: O(n), because traversal is required.

•	ArrayDeque: O(n), because it does not support direct index access.

5. Iterate Through the Entire List

•	ArrayList: O(n), because all elements are visited sequentially.

•	LinkedList: O(n), because each node is visited one by one.

•	ArrayDeque: O(n), because all elements are visited sequentially.

6. Remove from the Front

•	ArrayList: O(n), because remaining elements must be shifted.

•	LinkedList: O(1), because the first node is removed.

•	ArrayDeque: O(1) amortized, because the front element is removed without shifting all elements.


B. Space Complexity Comparison

1. ArrayList

•	Stores elements in a resizable array.

•	Requires one array reference per allocated slot.

•	Uses O(n) space.

•	May have unused capacity.

2. LinkedList

•	Stores elements as separate nodes.

•	Each node contains data and previous/next references.

•	Requires additional memory for node references.

•	Uses O(n) space.

3. ArrayDeque

•	Stores elements in a resizable circular array.

•	Requires array slots for storing element references.

•	Uses O(n) space.

•	May have unused capacity.

C. Plain Array with Manual Shifting

If a plain array is used:

•	Adding at the end takes O(1) time if an empty position is available.

•	Inserting at the front requires shifting all existing elements, taking O(n) time.

•	Inserting in the middle requires shifting elements after the insertion position, taking O(n) time.

•	Accessing the k-th element takes O(1) time.

•	Removing from the front requires shifting remaining elements, taking O(n) time.

•	Iterating through the array takes O(n) time.

If the array becomes full, a larger array must be created and the existing elements copied into it, taking O(n) time.

Therefore, ArrayList is more convenient than a plain array because it automatically manages resizing.



7. Conclusion

ArrayList is the most suitable choice for the main music playlist because it provides fast random access, efficient iteration and amortized constant-time insertion at the end.

LinkedList is useful when frequent insertions and deletions occur at the beginning or through an already-known node position.

When the Up Next queue is introduced, ArrayDeque becomes the better choice because it supports efficient insertion and removal at both ends.

Final Recommendation:

•	ArrayList: Main music playlist.

•	LinkedList: Frequent insertions and deletions at known node positions.

•	ArrayDeque: Up Next queue.

•	Plain Array: Suitable for fixed-size collections with minimal modifications.




