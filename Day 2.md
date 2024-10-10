# Why String is a good key for Hashmap? How to choose a key for HM? 
# Why should they be immutable?


### Why is `String` a good key for `HashMap`?


- The `String`  class in Java overrides the `hashCode()`  method from `Object`  class. This method generates an integer value based on the contents of the string, which is then used by the HashMap to determine the bucket in which the key-value pair will be placed.
- The `hashCode()`  method of `String`  has been optimized for performance, meaning it's fast enough even for long strings, providing an even distribution of hash codes which reduces the chance of collisions.
- A key’s `hashCode()`  is calculated based on its state. If the state of the key changes after it’s been inserted into the HashMap, the hash code will also change, making it impossible to locate the key.
- Since strings are immutable, their contents can't be modified once they are created. This ensures that the hash code remains constant, preventing issues when looking up keys in the HashMap.


A good `hashCode()` method:

- Should be consistent: If the object’s state does not change, multiple invocations of `hashCode()`  should return the same value.
- Should distribute hash codes uniformly to minimize collisions in hash-based collections.


```
public class Person {
    private String name;
    private int age;
    private String address;

    // Constructor, getters, and setters

    @Override
    public boolean equals(Object obj) {
        // Step 1: Check if the same object
        if (this == obj) {
            return true;
        }

        // Step 2: Check if obj is null or of a different class
        if (obj == null || getClass() != obj.getClass()) {
            return false;
        }

        // Step 3: Cast the object
        Person person = (Person) obj;

        // Step 4: Compare fields (note: use Objects.equals() to handle nulls)
        return Objects.equals(name, person.name) &&
               age == person.age &&
               Objects.equals(address, person.address);
    }

    @Override
    public int hashCode() {
        // Use Objects.hash() for consistency
        return Objects.hash(name, age, address);
    }
}
```
### How to Choose a Key for a HashMap?
When choosing a key for a HashMap, there are several factors to consider:

1. **Immutability**:
    - **Why it’s important**: The key should be immutable. This ensures that the hash code and equality of the key remain consistent throughout its lifetime in the map. If the key’s state changes after insertion, it will be difficult to retrieve the value since the `hashCode()`  will no longer match.
    - **Examples of good keys**:
        - `String`  (immutable by design)
        - Wrapper classes like `Integer` , `Double` , `Boolean` , etc., (also immutable)
    - **Examples of bad keys**:
        - Mutable objects like arrays, `List` , or custom classes where fields can change after being added to the HashMap.
2. **Efficient **`**hashCode()**` ** and **`**equals()**` :
    - The class of the key should provide a well-implemented `hashCode()`  and `equals()`  method.
    - A good hash code should spread the keys evenly across the HashMap’s buckets, which reduces collisions (when two keys have the same hash code and end up in the same bucket).
    - The `equals()`  method must properly compare the key’s content for equality.
3. **Consistency**:
    - The `hashCode()`  method should return the same hash code every time it is called on the same object (especially important for the HashMap’s correct functionality).
    - The `equals()`  method should also be reflexive, symmetric, transitive, and consistent with the hash code.
    - 
### Why Should Keys Be Immutable?
1. **Ensures Consistent Behavior**:
    - When you insert a key-value pair into a HashMap, the key’s `hashCode()`  and `equals()`  methods are used to determine where to store the pair and how to retrieve it. If the key is mutable and its state changes after being inserted into the map, the hash code might also change.
    - If the hash code changes, the map won’t be able to find the key in its original bucket, and you’ll lose the ability to retrieve the value associated with that key.
2. **Avoids Data Integrity Issues**:
    - If the key can change after insertion, it might lead to multiple problems like duplicate entries, broken lookup, or even corrupted data.
    - For example, if an object used as a key is modified such that its hash code changes, it might still be stored in the wrong bucket based on the old hash code, but searching for it with the new hash code will fail.
3. **Concurrency Benefits**:
    - If you are using a `HashMap`  (or a `ConcurrentHashMap`  for that matter) in a multi-threaded environment, having mutable keys could cause synchronization problems. Immutable keys are thread-safe, meaning they cannot be changed by multiple threads, which reduces the chances of race conditions.
### Conclusion
- **String** is a good key for a HashMap because it is immutable, has well-optimized `hashCode()`  and `equals()`  methods, and ensures consistent behavior in the map.
- When choosing a key for a HashMap, it's crucial to prioritize immutability, efficient hashing and equality methods, and performance.
- Immutable keys are essential for preventing unpredictable behavior in hash-based collections like HashMap, where consistent hash codes and equality checks are critical to data retrieval.


# When does an OutOfMemory Error happen and how to fix that?




```
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
at memory.JavaHeapSpace.main(JavaHeapSpace.java:5)
```
Basically, the **java.lang.OutOfMemoryError Java heap space** tells that the heap of your application is not large enough or you are doing something wrong, or you have an old, good [﻿Java memory leak](https://sematext.com/blog/java-memory-leaks/).

Here’s an example that illustrates the Java heap space problem:



```
public class JavaHeapSpace {
  public static void main(String[] args) throws Exception {
    String[] array = new String[100000 * 100000];
  }
}
```


And the result is simple – there is just not enough memory on the heap to assign the array, and thus the JVM throws an error informing us about that.







# JVM architecture 


![image.png](https://eraser.imgix.net/workspaces/0WwpA7zV20amyP1rhvCR/wWRfOan6zVfYt3tup4I0EWF0rar1/CJI4vJ9b4AJKOZVxyJ4Z4.png?ixlib=js-3.7.0 "image.png")



1. **JVM Architecture Overview**:
    - The JVM has three main components:
        1. **ClassLoader**: Loads Java classes.
        2. **Runtime Data Area**: The memory area where classes and variables are stored.
        3. **Execution Engine**: Converts bytecode to machine code for execution.
2. **ClassLoader**:
    - **ClassLoader Phases**:
        1. **Loading**: Loads `.class`  files into memory.
        2. **Linking**: Involves verification, preparing memory for static variables, and resolving references.
        3. **Initialization**: Initializes static variables and executes static blocks.
    - There are three types of class loaders:
        1. **Bootstrap ClassLoader**: Loads core Java libraries (like `System` ).
        2. **Extension ClassLoader**: Loads additional libraries (like JDBC).
        3. **Application ClassLoader**: Loads application-specific classes.
3. **Runtime Data Area**:
    - The memory is divided into five areas:
        1. **Method Area**: Stores class-level data, including static variables.
        2. **Heap**: Stores objects and instance variables.
        3. **Stack**: Stores local variables, operands, and execution frames.
        4. **PC Registers**: Tracks the current executing instruction.
        5. **Native Method Stack**: Handles native methods.
4. **Execution Engine**:
    - **Interpreter**: Reads and executes bytecode one line at a time. However, it's slower for repeated methods.
    - **JIT Compiler**: Compiles frequently used bytecode into native machine code for faster execution.
    - **Garbage Collector**: Removes objects no longer in use.
    - **JNI (Java Native Interface)**: Allows JVM to interact with native libraries.
In short, the JVM architecture consists of the ClassLoader, Runtime Data Area, and Execution Engine, each playing a crucial role in running Java programs.





1. **Can you explain the role of the ClassLoader in JVM architecture?**
    - Follow-up: What are the three types of class loaders in the JVM, and how do they differ?
2. **What are the different runtime data areas in JVM, and what is stored in each of them?**
    - Follow-up: How do the heap and stack memory differ in terms of storage and purpose?
3. **What is the purpose of the JIT (Just-In-Time) compiler, and how does it improve JVM performance?**
    - Follow-up: How does JIT differ from the interpreter in JVM?
4. **What is the Garbage Collector in JVM, and how does it work?**
    - Follow-up: Can you explain different garbage collection algorithms or techniques in Java?
5. **What are the steps involved in the linking phase of ClassLoader in JVM?**
    - Follow-up: Why is bytecode verification important during the linking process?




# What is difference between composition, aggregation and association?


**Association**: Use when the two entities can exist independently. For example, a `User`  and `Profile`  can have a loose association, where deleting one does not necessarily delete the other.

**Aggregation**: Use when an entity logically contains other entities, but they can exist independently. For instance, a `Company`  that contains `Employees` , but the `Employee`  can exist even if the `Company`  is deleted.

**Composition**: Use when the child entity cannot exist without the parent. For example, a `School`  and `Classroom`  may have a composition relationship, as classrooms are meaningless without the school.



Association:

-  A `Person`  and `Address`  have an association. A person can live at an address, but the address can exist without the person.
#### 
```
@Entity
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToOne
    @JoinColumn(name = "address_id")
    private Address address;

    // getters and setters
}

@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String street;
    private String city;

    // getters and setters
}
```


### **Aggregation in Spring Boot and Hibernate**
In **aggregation**, one entity contains references to another entity, but they can exist independently. This is usually represented by a `@OneToMany` or `@ManyToOne` relationship where the lifecycle of the contained objects (child objects) is independent of the parent entity.

- **Example**: A `Library`  can contain multiple `Book`  entities, but the `Book`  entities can still exist without the `Library` .
```
@Entity
public class Library {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(cascade = CascadeType.ALL)
    @JoinColumn(name = "library_id")
    private List<Book> books;

    // getters and setters
}

@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    // getters and setters
}
```


**Composition** in Hibernate is when one entity is strongly dependent on another, meaning if the parent entity is deleted, the child entity should also be deleted. This is represented using a `@OneToMany` or `@OneToOne` relationship with a cascading delete.

- **Example**: A `House`  entity may have multiple `Room`  entities. If the `House`  is deleted, all associated `Room`  entities should be deleted as well.
```
@Entity
public class House {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Room> rooms;

    // getters and setters
}

@Entity
public class Room {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    // getters and setters
}
```




**Association**:

- Loose coupling between entities.
- Both entities can exist independently.
- Example: `@OneToOne`  between `Person`  and `Address` .
**Aggregation**:

- One entity contains other entities, but they can exist independently.
- Example: `@OneToMany`  relationship between `Library`  and `Book` .
**Composition**:

- Strong coupling; child entities' lifecycle depends on the parent.
- Example: `@OneToMany`  with `CascadeType.ALL`  between `House`  and `Room` .


# When to use LinkedList over ArrayList in Java?


### **Use LinkedList over ArrayList in Java when**:
1. **Frequent Insertions or Deletions**:
    - **LinkedList** is more efficient when you frequently need to **add or remove elements** from the **beginning or middle** of the list. Inserting or deleting an element in a LinkedList has a time complexity of **O(1)**, as it simply involves updating the node pointers.
    - In contrast, **ArrayList** requires **shifting elements** after an insertion or deletion, making it slower (**O(n)**) for such operations.
2. When you need a **Queue** or **Deque**:
    - **LinkedList** implements both the **Queue** and **Deque** interfaces, so it’s a natural fit for implementing **FIFO (First-In-First-Out)** queues or **double-ended queues**.
    - **ArrayList** does not directly implement these interface.
3. Use **LinkedList** when you frequently need to **insert** or **remove** elements from the **middle or ends** of the list.Use **ArrayList** when you need **fast random access** and are working with **large lists** where only occasional inserts or deletions are required.
# 


****



# **What is difference between API Gateway and Load Balancer?**


### **API Gateway:**
- **Primary Purpose**: Manages and controls the routing of API requests.
- **Functionality**:
    - Handles **authentication**, **authorization**, and **rate limiting**.
    - **Routes** and **aggregates** API calls from clients to different microservices.
    - **Transforms** requests and responses, allowing modifications such as adding headers, changing the body, or managing versions.
    - Can serve as a single entry point for multiple **microservices** in an architecture.
    - Provides features like **caching**, **monitoring**, and **throttling**.
- **Use Case**:
    - Best used in **microservice** architectures where many internal services need a **single interface** for clients to interact with, ensuring security, routing, and policy enforcement.
- **Example**: AWS API Gateway, Kong, Zuul.
### **Load Balancer**:
- **Primary Purpose**: Distributes network traffic across multiple servers or instances to improve scalability and reliability.
- **Functionality**:
    - Primarily focuses on **distributing traffic** to ensure no single server gets overloaded.
    - Can operate on different layers like **Layer 4 (Transport)** or **Layer 7 (Application)**.
    - Supports **health checks** to monitor if a server is up or down.
    - Balances requests based on algorithms like **round-robin**, **least connections**, or **IP hash**.
    - Helps in **high availability** by distributing requests across multiple instances.
- **Use Case**:
    - Ideal for **scaling** applications by distributing traffic among several servers, ensuring system stability under high traffic.
- **Example**: AWS Elastic Load Balancer (ELB), NGINX, HAProxy.


# How to create an immutable map in Java and why do you think that is necessary?


- ImmutableMap, as suggested by the name, is a type of Map which is immutable. It means that the content of the map are fixed or constant after declaration, that is, they are **read-only**.
- If any attempt made to add, delete and update elements in the Map, **UnsupportedOperationException** is thrown.
- An ImmutableMap does not allow null element either.
- If any attempt made to create an ImmutableMap with null element, **NullPointerException** is thrown. If any attempt is made to add null element in Map, **UnsupportedOperationException** is thrown.
**Advantages of ImmutableMap**

### **Thread Safety**
-  Since they cannot be changed after creation, you don't need to synchronize access to them. Immutable maps are inherently thread-safe because their state can’t be altered by multiple threads at the same time.
- **Example**: If multiple threads are accessing the same map without needing to lock or coordinate access, it ensures safe reads without risking data corruption.


---

### **Examples of Use Cases**:
- **Configuration Data**: In a Spring Boot application, immutable maps can be used for configuration properties that should not change at runtime.
- **Static Lookups**: When you need a map for static lookups (e.g., mapping error codes to error messages), an immutable map ensures that the mapping doesn’t accidentally change during runtime.
- **Returning Values**: When returning a map from a method, using an immutable map guarantees that the caller cannot modify the returned data, making the code more predictable and safe.
---





**Use **`**Collections.unmodifiableMap**`** when:**

- **You are using Java 8 or older**: `Collections.unmodifiableMap`  is available in earlier versions of Java, providing a way to make an existing map immutable.
- **You have an existing mutable map**: If you already have a mutable map and need to provide an immutable view of it, `Collections.unmodifiableMap`  is suitable.
- **You need to ensure immutability for a dynamically created map**: If your map is created at runtime or has dynamic content, you can wrap it in `Collections.unmodifiableMap`  to prevent modifications.
**Advantages:**

- **Flexibility**: It allows you to create an immutable view of a mutable map, which can be useful in scenarios where the map content is generated or modified dynamically.
- **Compatibility**: It works with older versions of Java (Java 8 and earlier).






### . `**Map.of**` 
**Use **`**Map.of**`** when:**

- **You are using Java 9 or newer**: `Map.of`  is available starting from Java 9 and is a straightforward way to create immutable maps.
- **You have a fixed set of entries**: If you know the map will have a small, fixed number of key-value pairs, `Map.of`  is a clean and concise way to create the map.
- **You want to initialize the map at the point of declaration**: `Map.of`  is convenient for initializing immutable maps with known values directly.
**Advantages:**

- **Concise Syntax**: It provides a compact and readable syntax for creating maps with a small number of entries.
- **Direct Immutability**: The map is inherently immutable; you cannot add or modify entries once it’s created.


****









# **What is the difference between Dependency Injection and Dependency Inversion Principle?**


The **Dependency Inversion Principle (DIP)** is one of the SOLID principles of object-oriented design. 





It states that high-level modules (business logic) should not depend on low-level modules (detailed implementations), but both should rely on abstractions (like interfaces). 

In simpler terms, this keeps different layers of an application loosely connected, making them easier to modify independently.



In microservices, each service can be thought of as a separate unit. By applying DIP:

- You define **interfaces** for services and their interactions, avoiding direct dependencies.
- **Low-level modules** (e.g., database access, external APIs) implement the interfaces.
- **High-level modules** (e.g., business logic) use the interfaces without knowing the details of the low-level modules.




### 
**This separation promotes loose coupling and flexibility when scaling or maintaining microservices.**

****

**Example**: Consider a service that sends notifications. The high-level module is the notification service, and the low-level module is the specific implementation of sending an email.



```
// Abstraction
public interface MessageSender {
    void sendMessage(String message);
}

// Low-level module
public class EmailSender implements MessageSender {
    public void sendMessage(String message) {
        // Code to send email
    }
}

// High-level module
public class NotificationService {
    private MessageSender messageSender;

    // Dependency Injection via constructor
    public NotificationService(MessageSender messageSender) {
        this.messageSender = messageSender;
    }

    public void sendNotification(String message) {
        messageSender.sendMessage(message);
    }
}
```


Dependency Injection is a design pattern used to implement Inversion of Control (IoC) where the control of creating and managing dependencies is transferred to a container or framework.

**Types of Dependency Injection**:

1. **Constructor Injection**: Dependencies are provided through the class constructor.
2. **Setter Injection**: Dependencies are provided through setter methods.
3. **Field Injection**: Dependencies are injected directly into fields of the class.
**Dependency Injection (DI)** is a design pattern where the framework (like Spring Boot) automatically provides the necessary dependencies (e.g., services, repositories) to a class. Instead of creating dependencies directly within a class, DI supplies them via constructor injection, setter injection, or field injection.





- **DIP** is a **design principle**. It guides how to structure your code by making high-level modules independent of low-level modules through abstraction. DIP focuses on reducing direct dependencies between modules.
- **DI** is a **design pattern** or technique to achieve DIP. It involves providing dependencies to an object from the outside (through the framework) rather than having the object create its own dependencies.


- **DIP** aims to create loosely coupled code by ensuring both high-level and low-level components depend on abstractions.
- **DI** simplifies code by delegating the responsibility of creating and managing dependencies to a framework like Spring.


- **DIP** suggests that high-level modules should depend on abstractions (like interfaces).
- **DI** helps implement DIP by injecting these abstractions (or interfaces) into classes, keeping the modules loosely coupled.






# ****










# **How will you implement caching in a Spring Boot application to improve performance?**




### **Default Built-in Cache Provider in Spring Boot**
By default, Spring Boot uses **ConcurrentMapCache** as the built-in cache provider. This is an in-memory cache that stores the cached data in a simple map (like `ConcurrentHashMap`).

- **When to Use ConcurrentMapCache**:
    - For small-scale applications.
    - When you don't need to share cached data across multiple servers.
    - When low memory usage and simple caching requirements are enough.
    - Suitable for development and testing environments where performance isn’t critical.




**Redis** is a popular in-memory data store and is often used as a caching layer for Spring Boot applications because it supports data persistence, replication, and high availability.



Redis should be considered when:

- You need a **scalable** cache system.
- You require **shared cache** between multiple instances.
- The application’s **response time is critical**, and data needs to be retrieved quickly.
- You need **data persistence** even if the cache provider is restarted.


You would choose an external cache provider when the default in-memory caching solution (`ConcurrentMapCache`) doesn’t meet your application’s performance, scaling, or reliability needs.





```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<!-- For Redis as the cache provider -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```




```
@SpringBootApplication
@EnableCaching
public class YourApplication {
    public static void main(String[] args) {
        SpringApplication.run(YourApplication.class, args);
    }
}
```


```
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
```


You can configure **TTL (Time to Live)** for cache entries to ensure they don't stay forever. For example, when using Redis:

```
spring.cache.redis.time-to-live=600000  # 10 minutes in milliseconds
```


**@EnableCaching** activates caching.

**@Cacheable** caches the result of a method call.

**@CachePut** updates cache after modifying data.

**@CacheEvict** removes cache entries











# **Is ++ operator is thread-safe in Java? If not then why?**


No, the `++` operator is **not thread-safe** in Java.

### **Explanation**:
The `++` operator is a compound operation, meaning it involves multiple steps:

1. **Read the current value** of a variable.
2. **Increment the value** by 1.
3. **Write the new value** back to the variable.
In a **multithreaded environment**, if two threads try to increment the same variable at the same time, it can result in a **race condition**. Both threads could read the same value before it gets incremented, leading to an incorrect result.



### **How to make it thread-safe**:
You can use **synchronization** or atomic classes to make the increment operation thread-safe.





# ** What is Liskov's Substitution principle? Can you explain with an example?**


he **Liskov Substitution Principle (LSP)** is one of the five SOLID principles of object-oriented programming. It states that:

**"Objects of a superclass should be replaceable with objects of its subclass without affecting the correctness of the program."**

In simple terms, if you have a base class and a derived class, you should be able to use the derived class wherever the base class is expected, and the program should function properly without breaking.



Imagine you are building a Spring Boot application where you have a base service class for handling API responses, and there are different implementations for successful and error responses.



```
class ApiResponse {
    private String message;
    private int statusCode;

    // Constructors, Getters, and Setters
}

class SuccessResponse extends ApiResponse {
    private Object data;

    // Constructors, Getters, and Setters
}

class ErrorResponse extends ApiResponse {
    private String errorDetails;

    // Constructors, Getters, and Setters
}
```


```
@RestController
public class MyController {

    @GetMapping("/api/data")
    public ApiResponse getData() {
        if (dataExists) {
            return new SuccessResponse("Data found", 200, data);
        } else {
            return new ErrorResponse("Data not found", 404, "Details about the error");
        }
    }
}
```








# Difference between _map()_ and _flatMap()_ in streams.


### `map()` 
- **Use when**: You want to transform each element of a stream into another form, resulting in a **one-to-one** mapping.
- **Return type**: It returns a stream of the same structure but with transformed elements.
```
List<String> names = Arrays.asList("John", "Jane", "Jack");
List<Integer> nameLengths = names.stream()
    .map(String::length)  // Converts each name to its length
    .collect(Collectors.toList());
System.out.println(nameLengths);  // Output: [4, 4, 4]
```


###  `flatMap()` 
- **Use when**: You want to transform each element into a stream (like flattening lists), resulting in a **one-to-many** mapping.
- **Return type**: It "flattens" multiple streams into a single one by merging them together.


```
List<List<String>> nestedNames = Arrays.asList(
    Arrays.asList("John", "Jane"),
    Arrays.asList("Jack", "Jill")
);

List<String> allNames = nestedNames.stream()
    .flatMap(List::stream)  // Flattens nested lists into a single stream
    .collect(Collectors.toList());

System.out.println(allNames);  // Output: [John, Jane, Jack, Jill]
```
```
List<String> sentences = Arrays.asList(
    "Hello world",
    "Java streams are awesome",
    "FlatMap is useful"
);

List<String> words = sentences.stream()
    .flatMap(sentence -> Arrays.stream(sentence.split(" ")))  // Split sentences into words and flatten
    .collect(Collectors.toList());

System.out.println(words);  // Output: [Hello, world, Java, streams, are, awesome, FlatMap, is, useful]
```
# What is Response Entity in Spring-Boot?


In Spring Boot, `ResponseEntity<T>` represents the **entire HTTP response**, including the status code, headers, and body. It allows you to control everything that goes into an HTTP response from the server.





### Why Use `ResponseEntity`?
- To **set custom status codes** (other than the default `200 OK` ).
- To **include headers** in the response.
- To return an **error response** with custom messages or data.
- To send **flexible and controlled responses** in RESTful APIs
```
@RestController
public class UserController {

    @GetMapping("/user")
    public ResponseEntity<User> getUser() {
        User user = new User("Varsha", "Das");
        return new ResponseEntity<>(user, HttpStatus.OK);
    }
}
```


```
@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUserById(@PathVariable String id) {
        User user = findUserById(id);  // Assume this method fetches the user
        if (user == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);  // Return 404 if user not found
        }
        return new ResponseEntity<>(user, HttpStatus.OK);
    }
}
```


