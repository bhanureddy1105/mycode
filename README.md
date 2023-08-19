# mycode
get values from JSON code based on key

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.util.ArrayList;
import java.util.List;

public class JsonValueExtractor {

    public static List<String> extractValues(JsonNode jsonNode, String key) {
        List<String> values = new ArrayList<>();
        if (jsonNode.isObject()) {
            JsonNode valueNode = jsonNode.get(key);
            if (valueNode != null) {
                if (valueNode.isArray()) {
                    for (JsonNode elementNode : valueNode) {
                        values.add(elementNode.asText());
                    }
                } else {
                    values.add(valueNode.asText());
                }
            }
            jsonNode.fields().forEachRemaining(entry -> {
                values.addAll(extractValues(entry.getValue(), key));
            });
        } else if (jsonNode.isArray()) {
            for (JsonNode elementNode : jsonNode) {
                values.addAll(extractValues(elementNode, key));
            }
        }
        return values;
    }

    public static void main(String[] args) {
        String json = "{\"key1\":\"value1\",\"key2\":{\"key1\":\"value2\"},\"key3\":[{\"key1\":\"value3\"},{\"key1\":\"value4\"}]}";
        String key = "key1";

        ObjectMapper objectMapper = new ObjectMapper();
        try {
            JsonNode jsonNode = objectMapper.readTree(json);
            List<String> extractedValues = extractValues(jsonNode, key);
            extractedValues.forEach(System.out::println);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
