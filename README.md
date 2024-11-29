import java.util.*;

public class LogicGateSimulation {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // Number of gates
        int numGates = Integer.parseInt(sc.nextLine().trim());
        Map<String, String> gates = new LinkedHashMap<>();

        // Read gate definitions
        for (int i = 0; i < numGates; i++) {
            String gateDefinition = sc.nextLine().trim();
            String[] parts = gateDefinition.split("=");
            gates.put(parts[0].trim(), parts[1].trim());
        }

        // Number of cycles
        int numCycles = Integer.parseInt(sc.nextLine().trim());

        // Read inputs
        Map<String, int[]> inputs = new HashMap<>();
        while (sc.hasNextLine()) {
            String line = sc.nextLine().trim();
            if (!line.contains(" ")) {
                break; // The last line is the required gate
            }
            String[] parts = line.split(" ");
            String inputName = parts[0];
            int[] values = new int[numCycles];
            for (int i = 1; i <= numCycles; i++) {
                values[i - 1] = Integer.parseInt(parts[i]);
            }
            inputs.put(inputName, values);
        }

        // Required gate
        String requiredGate = sc.nextLine().trim();

        // Store outputs of gates
        Map<String, int[]> gateOutputs = new HashMap<>();
        for (String gate : gates.keySet()) {
            gateOutputs.put(gate, new int[numCycles]);
        }

        // Process gates cycle by cycle
        for (int cycle = 1; cycle < numCycles; cycle++) {
            for (Map.Entry<String, String> entry : gates.entrySet()) {
                String outputGate = entry.getKey();
                String operation = entry.getValue();

                String gateType = operation.substring(0, operation.indexOf('(')).trim();
                String[] gateInputs = operation.substring(operation.indexOf('(') + 1, operation.indexOf(')'))
                        .split(",");

                String input1 = gateInputs[0].trim();
                String input2 = gateInputs[1].trim();

                int val1 = inputs.containsKey(input1) ? inputs.get(input1)[cycle - 1] : gateOutputs.get(input1)[cycle - 1];
                int val2 = inputs.containsKey(input2) ? inputs.get(input2)[cycle - 1] : gateOutputs.get(input2)[cycle - 1];

                gateOutputs.get(outputGate)[cycle] = evaluateGate(gateType, val1, val2);
            }
        }

        // Print the output of the required gate
        int[] output = gateOutputs.get(requiredGate);
        for (int i = 0; i < numCycles; i++) {
            System.out.print(output[i]);
            if (i < numCycles - 1) {
                System.out.print(" ");
            }
        }
    }

    // Evaluate the gate operation
    private static int evaluateGate(String gateType, int val1, int val2) {
        switch (gateType) {
            case "AND":
                return val1 & val2;
            case "OR":
                return val1 | val2;
            case "NAND":
                return (val1 & val2) == 0 ? 1 : 0;
            case "NOR":
                return (val1 | val2) == 0 ? 1 : 0;
            case "XOR":
                return val1 ^ val2;
            default:
                throw new IllegalArgumentException("Unknown gate type: " + gateType);
        }
    }
}

