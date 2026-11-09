A. Diagnose the Bottleneck 

Original problem:

In Q1, a railway e-ticket was generated using passenger details such as PNR, passenger name, coach, berth, fare and journey information.
The original implementation used the String class with repeated concatenation (+) to build the ticket.

Identified limitations:

•	The String class is immutable. Once a string is created, its contents cannot be changed.

•	Every concatenation creates a new string containing the previous content and the new text.

•	When many fields are added repeatedly, unnecessary intermediate string objects are created.

•	The old string contents must be copied again during repeated concatenation.

•	As the number of fields and tickets increases, execution time and temporary memory usage also increase.

Where most time and memory are spent:

•	Repeatedly creating new string objects.

•	Copying previously generated ticket content.

•	Allocating temporary memory for intermediate strings.

Growth of operations:

•	For a ticket containing a small number of fields, the difference may be insignificant.

•	For thousands of tickets containing many fields, repeated string creation becomes expensive.

•	With repeated concatenation in a loop, the total copying cost can grow approximately quadratically with the number of fields, assuming similar field lengths.

Main cause: The immutability of the String class causes repeated object creation during ticket construction.

B. Define the Design Objective 

The redesigned solution should improve ticket generation when the number of tickets and fields increases.

Objectives:

1.	Reduce unnecessary intermediate string object creation.

2.	Avoid repeatedly copying the entire previously generated ticket.

3.	Improve ticket-generation execution time.

4.	Reduce temporary memory allocation.

5.	Produce exactly the same ticket output as the original implementation.

Measurable objectives:

•	Generate 10,000 tickets using both implementations.

•	Use the same 50 fields for each ticket.

•	Compare total execution time in milliseconds.

•	Compare the output of both implementations to verify correctness.

•	Aim for lower execution time and fewer temporary objects with the redesigned implementation.

C. Design the Revised Solution 

Proposed redesign: StringBuilder with preallocated capacity

Instead of repeatedly creating new strings, the redesigned solution uses a mutable StringBuilder.

Changes made:

•	Replace repeated String concatenation with StringBuilder.

•	Use the append() method to add ticket fields.

•	Allocate an estimated initial capacity to reduce repeated internal buffer expansion.

•	Convert the final result into a String only once using toString().

Properties used:

•	StringBuilder is mutable.

•	Its contents can be modified without creating a new string for every append operation.

•	It uses an internal expandable character buffer.

•	It is not synchronized, making it suitable for building an individual ticket within a single thread.

Operations avoided or reduced:

•	Repeated creation of intermediate strings.

•	Repeated copying of previously generated ticket content.

•	Unnecessary temporary memory allocation.

Additional memory and trade-offs:

•	StringBuilder requires an internal buffer.

•	An estimated capacity can reduce buffer resizing.

•	The final toString() operation still creates the final immutable string.

•	If the estimated capacity is too large, some memory may be wasted.

•	If multiple threads share the same builder, synchronization is required.

Design representation

             Ticket Generation
                    |
                    v
          Receive Ticket Details
                    |
                    v
             Create StringBuilder
             with Initial Capacity
                    |
                    v
             Append Ticket Fields
                    |
                    v
          Generate Final String
                    |
                    v
             Completed E-Ticket

Pseudocode:

START

Receive passenger details

Create StringBuilder with estimated capacity

Append PNR

Append Passenger Name

Append Coach

Append Berth

Append Fare

Append Journey Details

Convert StringBuilder into String

Display Completed Ticket

STOP

Revised Java implementation

public class TicketBuilder {

    static String buildTicket(String[] fields) {

        StringBuilder sb = new StringBuilder(512);

        for (String field : fields) {
            sb.append(field);
            sb.append(" | ");
        }

        return sb.toString();
    }

    public static void main(String[] args) {

        String[] fields = {
            "PNR: 123456",
            "Passenger: Ravi",
            "Coach: A1",
            "Berth: 25",
            "Fare: 750",
            "Journey: Chennai to Bangalore"
        };

        String ticket = buildTicket(fields);

        System.out.println(ticket);
    }
}

Expected output:

PNR: 123456 | Passenger: Ravi | Coach: A1 |

Berth: 25 | Fare: 750 | Journey: Chennai to Bangalore |

D. Predict Before Implementing 

Before conducting the experiment, the following improvements are predicted:

•	StringBuilder should generate tickets faster when many fields are added repeatedly.

•	Temporary object creation should be reduced.

•	Memory allocation for intermediate strings should decrease.

•	Both implementations should produce identical ticket content.

•	The performance difference should become more noticeable with larger workloads.

Prediction: StringBuilder with preallocated capacity will perform better than repeated String concatenation for large ticket-generation workloads.

E. Implement and Validate the Redesign 

To validate the redesign, both implementations are tested using the same input fields and number of tickets.

Test sizes:

•	100 tickets

•	1,000 tickets

•	10,000 tickets

Each ticket contains 50 fields.

Experimental comparison

Run the program and record the actual output for each test size.

•	100 tickets: Compare the execution time of both implementations.

•	1,000 tickets: Observe whether the performance difference increases.

•	10,000 tickets: Check whether StringBuilder provides a greater improvement under the larger workload.

Measures used:

•	Total execution time in milliseconds.

•	Correctness of generated ticket content.

•	Number of temporary string objects, analysed from the implementation.

Expected validation:

•	Both implementations should produce identical ticket content.

•	StringBuilder is expected to require fewer intermediate objects.

•	StringBuilder is expected to perform better for larger workloads.

Note: The program measures execution time, not exact memory consumption or object counts. Actual timings must be obtained by running the program on your computer. 
For precise memory and allocation measurements, a Java profiling tool or JMH benchmark can be used.

F. Defend the Final Design

Evaluation of the redesigned solution:

•	The redesign addresses the main limitation of repeated String concatenation.

•	StringBuilder modifies an existing buffer instead of creating a new string for every appended field.

•	The final output remains identical to the original implementation.

•	The redesign is expected to reduce execution time and temporary object creation, especially for large workloads.

New costs and trade-offs:

•	StringBuilder requires additional buffer memory.

•	The final conversion to String creates one additional immutable string.

•	Capacity estimation must be reasonable to avoid unnecessary memory allocation.

•	StringBuilder is not thread-safe when shared between multiple threads.

When the original String approach may still be preferable:

•	When generating a small number of short strings.

•	When only a few concatenations are required.

•	When readability and simplicity are more important than performance.

•	When building a single simple string expression rather than repeatedly appending inside a loop.


Final engineering decision:

StringBuilder with preallocated capacity is the better design for generating large numbers of railway e-tickets containing multiple fields. It reduces repeated object creation and copying while maintaining the correctness of the output. Therefore, the redesigned solution is more suitable for large-scale ticket generation.






