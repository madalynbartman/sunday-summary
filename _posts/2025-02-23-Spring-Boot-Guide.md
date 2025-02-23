# How to Create 

### Intro
I recently became interested in learning AI development in C++ and Java. I thought translating my first post, a guide on writing an API using FastAPI, into Java would be a great place to start. The goal is to notice patterns and trends in API development across languages. The ideal outcome would be to be able to think of development in a language agnostic sense. Let's get into it! 

Let's start by covering some important definitions.

API stands for Application Programming Interface. It allows us to define how we want clients to interact with our application.

In this tutorial, we'll be creating a web API. That's an API that uses Hypertext Transfer Protocol (HTTP) to transport data.

- **HTTP** is a communication protocol used to exchange different media types over a network. This article will provide examples for the four most common HTTP methods:
  - **GET**: Returns info about the requested resource
  - **POST**: Creates a new resource
  - **PUT**: Performs a full update by replacing a resource
  - **DELETE**: Removes a resource

APIs are useful for allowing microservices to communicate with each other. We can specify rules for interacting with each microservice in the API documentation.

The API documentation is essentially the description of the API and it's created following a standard interface description language such as OpenAPI for REST APIs.

Why Spring Boot? Spring Boot has three main benefits:
1. **Auto configuration**: Spring Boot automatically configures your project based on the dependencies you have added.
2. **Embedded server**: Spring Boot comes with embedded servers like Tomcat, making it easy to run your applications.
3. **Production-ready features**: Spring Boot includes features like metrics, health checks, and externalized configuration.

Alright, now we're ready to create our own!

Start by creating a Spring Boot project:
1. Go to [Spring Initializr](https://start.spring.io/)
2. Select the following dependencies:
   - Spring Web
   - Spring Data JPA (optional, if you want to use a database)
   - Spring Boot DevTools
   - Lombok (optional, for reducing boilerplate code)
   - Spring Boot Starter Validation
   - Springdoc OpenAPI (for Swagger documentation)
3. Generate the project and download the zip file.
4. Extract the zip file into your project directory.

Now, let's go through the files one by one:

### `DemoApplication.java`

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

@SpringBootApplication: This annotation marks the main class of a Spring Boot application. It combines @Configuration, @EnableAutoConfiguration, and @ComponentScan annotations.

main(): This is the entry point of the application. The SpringApplication.run() method launches the Spring Boot application.

### `pom.xml`

Ensure your pom.xml includes the necessary dependencies:

```xml
<dependencies>
    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Boot Starter Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- Springdoc OpenAPI UI -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-ui</artifactId>
        <version>1.5.9</version>
    </dependency>

    <!-- Lombok (optional) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### `OpenApiConfig.java`

package com.example.demo.config;

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springdoc.core.GroupedOpenApi;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("Spring Boot API")
                        .version("1.0.0"));
    }

    @Bean
    public GroupedOpenApi publicApi() {
        return GroupedOpenApi.builder()
                .group("spring-public")
                .pathsToMatch("/api/**")
                .build();
    }
}
```

This file configures the OpenAPI and Swagger documentation for your Spring Boot API.

### `Item.java`

```java
package com.example.demo.model;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Positive;
import lombok.Data;

@Data
public class Item {
    @NotNull
    private String name;

    @NotNull
    @Positive
    private Double price;

    private String description;
}
```

This model class defines the properties and validation rules for the Item entity in your application.

### `ItemController.java`

```java
package com.example.demo.controller;

import com.example.demo.model.Item;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

@RestController
@Validated
@RequestMapping("/api")
public class ItemController {
    private Map<Integer, Item> inventory = new HashMap<>();

    @GetMapping("/get-item/{itemId}")
    public ResponseEntity<Item> getItem(@PathVariable int itemId, @RequestParam Optional<String> name) {
        Item item = inventory.get(itemId);
        if (item == null || (name.isPresent() && !item.getName().equals(name.get()))) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(item, HttpStatus.OK);
    }

    @PostMapping("/create-item/{itemId}")
    public ResponseEntity<Item> createItem(@PathVariable int itemId, @RequestBody @Valid Item item) {
        if (inventory.containsKey(itemId)) {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
        inventory.put(itemId, item);
        return new ResponseEntity<>(item, HttpStatus.CREATED);
    }

    @PutMapping("/update-item/{itemId}")
    public ResponseEntity<Item> updateItem(@PathVariable int itemId, @RequestBody @Valid Item item) {
        if (!inventory.containsKey(itemId)) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        Item existingItem = inventory.get(itemId);
        if (item.getName() != null) {
            existingItem.setName(item.getName());
        }
        if (item.getPrice() != null) {
            existingItem.setPrice(item.getPrice());
        }
        if (item.getDescription() != null) {
            existingItem.setDescription(item.getDescription());
        }
        inventory.put(itemId, existingItem);
        return new ResponseEntity<>(existingItem, HttpStatus.OK);
    }

    @DeleteMapping("/delete-item/{itemId}")
    public ResponseEntity<String> deleteItem(@PathVariable int itemId) {
        if (!inventory.containsKey(itemId)) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        inventory.remove(itemId);
        return new ResponseEntity<>("Success: Item deleted!", HttpStatus.OK);
    }
}
```

Running the Application
Run the application using your IDE or by executing mvn spring-boot:run from the command line.

Access the Swagger UI at http://localhost:8080/swagger-ui.html to view and test your API documentation.

That's it for the GET method! Let's use the POST to create an item for our inventory.

``` Java
@PostMapping("/create-item/{itemId}")
public ResponseEntity<Item> createItem(@PathVariable int itemId, @RequestBody @Valid Item item) {
    if (inventory.containsKey(itemId)) {
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
    inventory.put(itemId, item);
    return new ResponseEntity<>(item, HttpStatus.CREATED);
}
```
Once again, we will create a decorator for our app. This time, however, we are calling the POST method after the dot. After that, we'll give it a descriptive path name and pass itemId as a path parameter just like we did before.

```Java
@PostMapping("/create-item/{itemId}")
```

Afterwards, we create a function to create an item that takes in an int for an item id and an item of the Item class that we defined earlier.

```Java
public ResponseEntity<Item> createItem(@PathVariable int itemId, @RequestBody @Valid Item item) {
```

Then we check if the item id is in the inventory and raise an exception if it is.

```Java
    if (inventory.containsKey(itemId)) {
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
```

If not, we update the empty inventory dictionary with the new item.

```Java
    inventory.put(itemId, item);
```

Finally, we return the inventory.

```Java
    return new ResponseEntity<>(item, HttpStatus.CREATED);
}
```

We can use the PUT method to update the inventory.

```Java
@PutMapping("/update-item/{itemId}")
public ResponseEntity<Item> updateItem(@PathVariable int itemId, @RequestBody @Valid Item item
```

