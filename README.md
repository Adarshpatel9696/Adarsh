/*import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.io.IOException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.Random;\*

public class DestinationHashGenerator {

    // Method to generate a random 8-character alphanumeric string
    public static String generateRandomString(int length) {
        String characters = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        Random rand = new Random();
        StringBuilder randomString = new StringBuilder();
        for (int i = 0; i < length; i++) {
            int index = rand.nextInt(characters.length());
            randomString.append(characters.charAt(index));
        }
        return randomString.toString();
    }

    // Method to generate MD5 hash
    public static String generateMD5(String input) throws NoSuchAlgorithmException {
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] hashBytes = md.digest(input.getBytes());
        StringBuilder hexString = new StringBuilder();
        for (byte b : hashBytes) {
            hexString.append(String.format("%02x", b));
        }
        return hexString.toString();
    }

    // Method to traverse JSON and get the value for the first "destination" key
    public static String findDestination(JsonNode node) {
        if (node.isObject()) {
            for (Iterator<String> fieldNames = node.fieldNames(); fieldNames.hasNext(); ) {
                String fieldName = fieldNames.next();
                JsonNode fieldValue = node.get(fieldName);
                if (fieldName.equals("destination")) {
                    return fieldValue.asText();
                }
                String result = findDestination(fieldValue);
                if (result != null) {
                    return result;
                }
            }
        } else if (node.isArray()) {
            for (JsonNode element : node) {
                String result = findDestination(element);
                if (result != null) {
                    return result;
                }
            }
        }
        return null;
    }

    public static void main(String[] args) throws IOException, NoSuchAlgorithmException {
        if (args.length != 2) {
            System.out.println("Usage: java -jar DestinationHashGenerator.jar <Roll Number> <JSON File Path>");
            return;
        }

        // 1. Parse command-line arguments
        String rollNumber = args[0].toLowerCase(); // Roll number in lowercase
        String jsonFilePath = args[1];

        // 2. Read and parse JSON file
        File jsonFile = new File(jsonFilePath);
        ObjectMapper objectMapper = new ObjectMapper();
        JsonNode rootNode = objectMapper.readTree(jsonFile);

        // 3. Traverse JSON and find the first "destination" key
        String destinationValue = findDestination(rootNode);
        if (destinationValue == null) {
            System.out.println("Destination key not found in the JSON file.");
            return;
        }

        // 4. Generate a random 8-character alphanumeric string
        String randomString = generateRandomString(8);

        // 5. Concatenate Roll Number, Destination Value, and Random String
        String concatenated = rollNumber + destinationValue + randomString;

        // 6. Generate MD5 hash of the concatenated string
        String md5Hash = generateMD5(concatenated);

        // 7. Output the result: <hashed value>;<random string>
        System.out.println(md5Hash + ";" + randomString);
    }
}
