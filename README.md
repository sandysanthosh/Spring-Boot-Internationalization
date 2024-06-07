Internationalization (i18n) in Spring Boot allows you to create applications that support multiple languages and regions. Below are the steps and examples to implement i18n in a Spring Boot application.

### Step 1: Add Necessary Dependencies

Ensure that you have the Spring Boot Starter Web dependency in your `pom.xml`:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

### Step 2: Create Message Properties Files

Create property files for different languages in the `src/main/resources` directory.

#### `messages.properties` (default)
```properties
greeting=Hello, World!
```

#### `messages_fr.properties` (French)
```properties
greeting=Bonjour le monde!
```

#### `messages_es.properties` (Spanish)
```properties
greeting=¡Hola, Mundo!
```

### Step 3: Configure the Message Source

Configure a `ResourceBundleMessageSource` bean in your Spring Boot application to specify the basename and encoding of your message properties files.

#### `InternationalizationConfig.java`
```java
import org.springframework.context.MessageSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ResourceBundleMessageSource;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver;

import java.util.Locale;

@Configuration
public class InternationalizationConfig {

    @Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasename("messages");
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }

    @Bean
    public LocaleResolver localeResolver() {
        AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
        localeResolver.setDefaultLocale(Locale.ENGLISH);
        return localeResolver;
    }
}
```

### Step 4: Create a Controller to Use the Messages

Use the `MessageSource` bean to get the messages in different languages.

#### `GreetingController.java`
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RestController;

import java.util.Locale;

@RestController
public class GreetingController {

    @Autowired
    private MessageSource messageSource;

    @GetMapping("/greeting")
    public String greeting(@RequestHeader(name = "Accept-Language", required = false) Locale locale) {
        return messageSource.getMessage("greeting", null, locale);
    }
}
```

### Step 5: Test the Application

Start your Spring Boot application and test the endpoint with different `Accept-Language` headers.

#### Request with `Accept-Language: en`
```http
GET /greeting HTTP/1.1
Host: localhost:8080
Accept-Language: en
```
Response:
```
Hello, World!
```

#### Request with `Accept-Language: fr`
```http
GET /greeting HTTP/1.1
Host: localhost:8080
Accept-Language: fr
```
Response:
```
Bonjour le monde!
```

#### Request with `Accept-Language: es`
```http
GET /greeting HTTP/1.1
Host: localhost:8080
Accept-Language: es
```
Response:
```
¡Hola, Mundo!
```

### Additional Tips

- **Fallback Locale**: If a message is not found in the requested locale, the default message file (`messages.properties`) will be used.
- **Parameterization**: You can also parameterize messages. For example, `greeting=Hello, {0}!` and then use `messageSource.getMessage("greeting", new Object[]{"John"}, locale);`.
- **Locale Change Interceptor**: If you want to switch locales using query parameters, you can use a `LocaleChangeInterceptor`.

This basic setup allows you to internationalize your Spring Boot application, supporting multiple languages and enhancing the user experience globally.
