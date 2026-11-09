public class TicketBenchmark {

    static volatile String result;

    // Approach 1: Using String
    static String usingString(String[] fields) {
        String ticket = "";

        for (String field : fields) {
            ticket = ticket + field + " | ";
        }

        return ticket;
    }

    // Approach 2: Using StringBuilder
    static String usingStringBuilder(String[] fields) {
        StringBuilder ticket = new StringBuilder();

        for (String field : fields) {
            ticket.append(field).append(" | ");
        }

        return ticket.toString();
    }

    // Approach 3: Using StringBuffer
    static String usingStringBuffer(String[] fields) {
        StringBuffer ticket = new StringBuffer();

        for (String field : fields) {
            ticket.append(field).append(" | ");
        }

        return ticket.toString();
    }

    // Measure execution time
    static void measure(String name, String[] fields, int tickets) {

        long start = System.nanoTime();

        for (int i = 0; i < tickets; i++) {
            if (name.equals("String"))
                result = usingString(fields);

            else if (name.equals("StringBuilder"))
                result = usingStringBuilder(fields);

            else
                result = usingStringBuffer(fields);
        }

        long end = System.nanoTime();

        System.out.println(name + " : "
                + (end - start) / 1_000_000.0 + " ms");
    }

    public static void main(String[] args) {

        int tickets = 10000;
        int fieldsCount = 50;

        String[] fields = new String[fieldsCount];

        for (int i = 0; i < fieldsCount; i++) {
            fields[i] = "Field" + i;
        }

        // Warm-up
        for (int i = 0; i < 5; i++) {
            measure("String", fields, 100);
            measure("StringBuilder", fields, 100);
            measure("StringBuffer", fields, 100);
        }

        // Actual comparison
        System.out.println("\nPerformance Comparison");

        measure("String", fields, tickets);
        measure("StringBuilder", fields, tickets);
        measure("StringBuffer", fields, tickets);

        // Approximate memory measurement
        Runtime runtime = Runtime.getRuntime();

        System.gc();

        long before = runtime.totalMemory()
                - runtime.freeMemory();

        result = usingStringBuilder(fields);

        long after = runtime.totalMemory()
                - runtime.freeMemory();

        System.out.println("Approximate memory difference: "
                + (after - before) + " bytes");
    }
}



