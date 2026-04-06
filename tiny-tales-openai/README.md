# Nodify Tiny Tales Template with OpenAI

A simple **news template** for [Nodify Headless CMS](https://github.com/AZIRARM/nodify).

## 🚀 How to Use

1. **Download** the template JSON file:  
   👉 [Nodify-Tiny-Tales-OpenAI.json](Nodify-Tiny-Tales-OpenAI.json)
   
   At the root node, change the `BASE_URL` value to point to your Nodify API address.
   The template contains 2 stories. You need to run an OpenAI-compatible AI client or add stories from the Nodify studio.

2. **Import into Nodify**:
   - Open your **Nodify dashboard**.
   - Go to **Projects → Environments**.
   - Choose your environment.
   - Click on the **import button** (⬇️ inside a circle).
   - Upload the JSON file.

3. **Preview the Template**:
   - Go to **Content** of the **parent node**.
   - Click 👁️ on the first content.

## 📦 Features
- AI-generated children's stories

---

## Maven Dependency

Add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>io.github.azirarm</groupId>
    <artifactId>nodify-java-client</artifactId>
    <version>1.1.0</version>
</dependency>
```

---

## Java Code Examples

### Author Model

```java
package com.tinytales.example.models;

/**
 * Author record for story authors
 */
public record Author(
        String name,
        String email,
        String bio,
        String avatarUrl
) {
    @Override
    public String toString() {
        return name;
    }
}
```

### Story Model

```java
package com.tinytales.example.models;

import io.github.azirarm.content.lib.enums.ContentTypeEnum;
import java.time.Instant;
import java.util.List;

/**
 * Story record representing a children's story
 */
public record Story(
        String id,
        String title,
        String content,
        String excerpt,
        Author author,
        List<String> tags,
        String slug,
        String language,
        ContentTypeEnum type,
        Instant publishedDate,
        Instant lastModified,
        boolean published
) {
    public Story withId(String id) {
        return new Story(
                id, title, content, excerpt, author, tags,
                slug, language, type, publishedDate, lastModified, published
        );
    }

    public Story withPublished(boolean published) {
        return new Story(
                id, title, content, excerpt, author, tags,
                slug, language, type, publishedDate, lastModified, published
        );
    }

    @Override
    public String toString() {
        return String.format("Story{title='%s', author=%s, language=%s}",
                title, author.name(), language);
    }
}
```

### OpenAI Client

```java
package com.tinytales.example.ai;

import com.fasterxml.jackson.databind.ObjectMapper;
import io.github.azirarm.content.client.ReactiveNodifyClient;
import io.github.azirarm.content.lib.enums.ContentTypeEnum;
import io.github.azirarm.content.lib.enums.StatusEnum;
import io.github.azirarm.content.lib.models.ContentNode;
import io.github.azirarm.content.lib.models.Translation;
import io.github.azirarm.content.lib.models.Value;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLParameters;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.security.cert.X509Certificate;
import java.time.Duration;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * Client that generates children's stories using OpenAI-compatible LLM and pushes them to Nodify
 */
public class TinyTalesOpenAIClient {

    private final ReactiveNodifyClient nodifyClient;
    private final String storiesNodeCode;
    private final String openAiUrl;
    private final String model;
    private final ObjectMapper mapper = new ObjectMapper();
    private final HttpClient httpClient;

    public TinyTalesOpenAIClient(String nodifyBaseUrl, String username, String password,
                                 String storiesNodeCode, String openAiUrl, String model) {
        this.storiesNodeCode = storiesNodeCode;
        this.openAiUrl = openAiUrl;
        this.model = model;

        this.httpClient = createUnsafeHttpClient();

        this.nodifyClient = ReactiveNodifyClient.create(
                ReactiveNodifyClient.builder()
                        .withBaseUrl(nodifyBaseUrl)
                        .withTimeout(60000)
                        .build()
        );

        try {
            nodifyClient.login(username, password).block();
            System.out.println("✅ Authenticated to Nodify");
        } catch (Exception e) {
            System.err.println("❌ Failed to authenticate to Nodify: " + e.getMessage());
            throw new RuntimeException(e);
        }
    }

    private HttpClient createUnsafeHttpClient() {
        try {
            TrustManager[] trustAllCerts = new TrustManager[]{
                    new X509TrustManager() {
                        public X509Certificate[] getAcceptedIssuers() {
                            return new X509Certificate[0];
                        }

                        public void checkClientTrusted(X509Certificate[] certs, String authType) {
                        }

                        public void checkServerTrusted(X509Certificate[] certs, String authType) {
                        }
                    }
            };

            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, trustAllCerts, new java.security.SecureRandom());

            SSLParameters sslParams = new SSLParameters();
            sslParams.setEndpointIdentificationAlgorithm("");

            return HttpClient.newBuilder()
                    .connectTimeout(Duration.ofSeconds(30))
                    .sslContext(sslContext)
                    .sslParameters(sslParams)
                    .build();
        } catch (Exception e) {
            System.err.println("Warning: Could not create SSL context, using default: " + e.getMessage());
            return HttpClient.newBuilder()
                    .connectTimeout(Duration.ofSeconds(30))
                    .build();
        }
    }

    /**
     * Generate a story with title using OpenAI-compatible LLM
     * Returns a map with "title" and "content"
     */
    private Mono<Map<String, String>> generateStoryWithTitle(String prompt) {
        return Mono.fromCallable(() -> {
            String fullPrompt = prompt + "\n\nPlease provide a short title for this story. Format: TITLE: [title] then the story content.";

            Map<String, Object> requestBody = Map.of(
                    "model", model,
                    "messages", List.of(Map.of("role", "user", "content", fullPrompt)),
                    "temperature", 0.7,
                    "max_tokens", 600
            );

            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(openAiUrl + "/v1/chat/completions"))
                    .header("Content-Type", "application/json")
                    .timeout(Duration.ofSeconds(120))
                    .POST(HttpRequest.BodyPublishers.ofString(mapper.writeValueAsString(requestBody)))
                    .build();

            System.out.println("  📡 Calling OpenAI-compatible API at: " + openAiUrl + "/v1/chat/completions");

            HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());

            if (response.statusCode() != 200) {
                throw new RuntimeException("API error: " + response.statusCode() + " - " + response.body());
            }

            Map<String, Object> result = mapper.readValue(response.body(), Map.class);
            List<Map<String, Object>> choices = (List<Map<String, Object>>) result.get("choices");
            Map<String, Object> message = (Map<String, Object>) choices.get(0).get("message");
            String generatedText = (String) message.get("content");

            if (generatedText == null || generatedText.trim().isEmpty()) {
                throw new RuntimeException("API returned empty response");
            }

            generatedText = generatedText.replaceAll("[\\p{Cntrl}&&[^\n\r]]", "");

            String title = extractTitle(generatedText);
            String content = extractContent(generatedText, title);

            return Map.of("title", title, "content", content);
        });
    }

    /**
     * Extract title from generated text
     */
    private String extractTitle(String text) {
        Pattern titlePattern = Pattern.compile("TITLE:\\s*(.+?)(?=\\n|$)", Pattern.CASE_INSENSITIVE);
        Matcher matcher = titlePattern.matcher(text);
        if (matcher.find()) {
            return matcher.group(1).trim();
        }

        String firstLine = text.split("\n")[0].trim();
        if (firstLine.length() > 50) {
            return firstLine.substring(0, 47) + "...";
        }
        return firstLine;
    }

    /**
     * Extract content (remove title from text)
     */
    private String extractContent(String text, String title) {
        String content = text.replaceAll("(?i)TITLE:\\s*" + Pattern.quote(title) + "\\s*\\n?", "");
        return content.trim();
    }

    private Mono<String> createStoryInNodify(String title, String content, String language, String author) {
        ContentNode node = new ContentNode();
        node.setParentCode(storiesNodeCode);
        node.setType(ContentTypeEnum.JSON);
        node.setTitle(title);
        node.setCode(generateStoryCode(title));
        node.setSlug(title.toLowerCase().replaceAll("[^a-z0-9]+", "-"));
        node.setLanguage(language);
        node.setStatus(StatusEnum.SNAPSHOT);
        node.setEnvironmentCode("production");

        String jsonContent = """
                {
                  "title": "$translate(TITLE)",
                  "content": "$translate(CONTENT)"
                }
                """;

        node.setContent(jsonContent);

        Value authorValue = new Value();
        authorValue.setKey("AUTHOR_NAME");
        authorValue.setValue(author);
        node.setValues(List.of(authorValue));

        return nodifyClient.saveContentNode(node)
                .map(saved -> saved.getCode())
                .doOnSuccess(code -> System.out.println("  ✅ Story created: " + title + " (Code: " + code + ")"))
                .doOnError(e -> System.err.println("  ❌ Error creating story: " + e.getMessage()));
    }

    private Mono<Void> addTranslations(String storyCode, String titleEn, String contentEn,
                                       String titleFr, String contentFr) {
        return nodifyClient.findContentNodeByCodeAndStatus(storyCode, "SNAPSHOT")
                .flatMap(obj -> {
                    ContentNode node = (ContentNode) obj;

                    List<Translation> translations = new ArrayList<>();

                    Translation enTitle = new Translation();
                    enTitle.setKey("TITLE");
                    enTitle.setLanguage("EN");
                    enTitle.setValue(titleEn);
                    translations.add(enTitle);

                    Translation enContent = new Translation();
                    enContent.setKey("CONTENT");
                    enContent.setLanguage("EN");
                    enContent.setValue(contentEn);
                    translations.add(enContent);

                    Translation frTitle = new Translation();
                    frTitle.setKey("TITLE");
                    frTitle.setLanguage("FR");
                    frTitle.setValue(titleFr);
                    translations.add(frTitle);

                    Translation frContent = new Translation();
                    frContent.setKey("CONTENT");
                    frContent.setLanguage("FR");
                    frContent.setValue(contentFr);
                    translations.add(frContent);

                    node.setTranslations(translations);
                    return nodifyClient.saveContentNode(node);
                })
                .doOnSuccess(saved -> System.out.println("  ✅ Translations added for: " + storyCode))
                .then();
    }

    private Mono<Void> publishStory(String storyCode) {
        return nodifyClient.publishContentNode(storyCode, true)
                .doOnSuccess(published -> System.out.println("  ✅ Story published: " + storyCode))
                .then();
    }

    /**
     * Generate and publish a single story with translations
     */
    public Mono<Void> generateAndPublishStory(String promptEn, String promptFr,
                                              String author, String language) {
        System.out.println("\n📖 Generating story with OpenAI-compatible API...");
        System.out.println("   Model: " + model);
        System.out.println("   API URL: " + openAiUrl);

        return generateStoryWithTitle(promptEn)
                .flatMap(enResult -> {
                    String titleEn = enResult.get("title");
                    String contentEn = enResult.get("content");
                    System.out.println("  📝 English story generated:");
                    System.out.println("     Title: " + titleEn);
                    System.out.println("     Content length: " + contentEn.length() + " chars");

                    return createStoryInNodify(titleEn, contentEn, "EN", author)
                            .zipWith(Mono.just(titleEn), (code, title) -> Map.of(
                                    "code", code,
                                    "titleEn", title,
                                    "contentEn", contentEn
                            ));
                })
                .flatMap(storyInfo -> {
                    String storyCode = (String) storyInfo.get("code");
                    String titleEn = (String) storyInfo.get("titleEn");
                    String contentEn = (String) storyInfo.get("contentEn");

                    return generateStoryWithTitle(promptFr)
                            .flatMap(frResult -> {
                                String titleFr = frResult.get("title");
                                String contentFr = frResult.get("content");
                                System.out.println("  📝 French story generated:");
                                System.out.println("     Title: " + titleFr);
                                System.out.println("     Content length: " + contentFr.length() + " chars");

                                return addTranslations(storyCode,
                                        titleEn, contentEn,
                                        titleFr, contentFr)
                                        .thenReturn(storyCode);
                            });
                })
                .flatMap(storyCode -> {
                    System.out.println("  📤 Publishing story...");
                    return publishStory(storyCode);
                })
                .doOnSuccess(code -> System.out.println("  ✅ Story fully created, translated and published!"));
    }

    /**
     * Generate and publish multiple stories with translations
     *
     * @param count Number of stories to generate
     * @param author Author name
     * @param language Default language
     * @return Mono<Void> that completes when all stories are generated
     */
    public Mono<Void> generateAndPublishStories(int count, String author, String language) {
        System.out.println("\n📖 Generating " + count + " stories with OpenAI-compatible API...");
        System.out.println("   Model: " + model);
        System.out.println("   API URL: " + openAiUrl);

        return Flux.range(0, count)
                .concatMap(i -> {
                    String promptEn = "Write a very short children's story about a unique and original theme. " +
                            "The story should be magical, heartwarming, and suitable for children. " +
                            "Max 80 words. Each story must be completely different from the previous ones.";

                    String promptFr = "Écris une très courte histoire pour enfants sur un thème unique et original. " +
                            "L'histoire doit être magique, réconfortante et adaptée aux enfants. " +
                            "Maximum 80 mots. Chaque histoire doit être complètement différente des précédentes.";

                    System.out.println("\n📚 Generating story " + (i + 1) + "/" + count);

                    return Mono.delay(Duration.ofSeconds(3))
                            .then(generateAndPublishStory(promptEn, promptFr, author, language));
                })
                .then()
                .doOnSuccess(v -> System.out.println("\n🎉 All " + count + " stories generated and published!"));
    }

    private String generateStoryCode(String title) {
        String base = title.toUpperCase()
                .replaceAll("[^a-zA-Z0-9]", "-")
                .replaceAll("-+", "-");
        if (base.length() > 20) base = base.substring(0, 20);
        if (base.endsWith("-")) base = base.substring(0, base.length() - 1);
        return "STORY-" + base + "-" + UUID.randomUUID().toString().substring(0, 8).toUpperCase();
    }

    // ==================== MAIN ====================

    public static void main(String[] args) {
        String nodifyUrl = "https://nodify-core.azirar.ovh";
        String username = "admin";
        String password = "Admin13579++";
        String storiesNodeCode = "STORIES-NODE-815E2BA3";
        String openAiUrl = "https://api.openai.com/v1";
        String model = "gpt-3.5-turbo";

        System.out.println("🚀 Starting TinyTales OpenAI Client");
        System.out.println("   Nodify URL: " + nodifyUrl);
        System.out.println("   OpenAI URL: " + openAiUrl);
        System.out.println("   Model: " + model);
        System.out.println("   Stories Node Code: " + storiesNodeCode);

        TinyTalesOpenAIClient client = new TinyTalesOpenAIClient(
                nodifyUrl, username, password, storiesNodeCode, openAiUrl, model
        );

        int numberOfStories = 100;

        client.generateAndPublishStories(numberOfStories, "AI Storyteller", "EN")
                .doOnSuccess(v -> System.out.println("\n🎉 All " + numberOfStories + " stories generated!"))
                .doOnError(e -> {
                    System.err.println("\n❌ Error: " + e.getMessage());
                    e.printStackTrace();
                })
                .block();
    }
}
```

## 📝 Notes

- The client uses the official `nodify-java-client` dependency (version 1.1.0)
- The OpenAI API endpoint expects a chat completion endpoint (`/v1/chat/completions`)
- Stories are generated in both English and French with automatic translations
- All stories are automatically published to Nodify after creation
- The client uses reactive programming with Project Reactor for non-blocking operations
