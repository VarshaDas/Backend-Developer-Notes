(Java, Spring Boot, microservices)

# When to Use `HashSet` and `TreeSet` in Java?
## **HashSet**
- **Use Case**: When you need a collection of unique elements and don't care about the order of the elements.
- **Characteristics**:
    - **Fast Operations**: `HashSet`  provides constant time performance (O(1)) for basic operations like `add` , `remove` , and `contains` , assuming a good hash function.
    - **Unordered**: Elements in a `HashSet`  are not stored in any particular order, and the order can change over time.


## **TreeSet**
- **Use Case**: When you need a collection of unique elements in a sorted order.
- **Characteristics**:
    - **Sorted Order**: `TreeSet`  stores elements in a natural order (if they implement `Comparable` ) or according to a provided `Comparator` .
    - **Logarithmic Time**: Operations like `add` , `remove` , and `contains`  have a time complexity of O(log n) due to the underlying Red-Black Tree structure.




**Example**: Suppose you want to store a set of unique user names in alphabetical order.



**Use **`**HashSet**`  when you need fast access and don't care about the order of elements.

**Use **`**TreeSet**`  when you need elements to be stored in a sorted order and can afford the overhead of slower insertion and lookup times compared to `HashSet` .





Follow-up question



Use Comparator and apply on a TreeSet 



```
`﻿`

public class Main {
    public static void main(String[] args) {
        // Create a Comparator that sorts strings by length
        Comparator<String> lengthComparator = new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return Integer.compare(s1.length(), s2.length());
            }
        };

        // Create a TreeSet with the custom Comparator
        Set<String> strings = new TreeSet<>(lengthComparator);

        // Add elements to the TreeSet
        strings.add("banana");
        strings.add("apple");
        strings.add("kiwi");
        strings.add("strawberry");

        // Print the TreeSet
        System.out.println(strings); // Output: [kiwi, apple, banana, strawberry]
    }
} 

```




Follow-up question:



But TreeSet is not thread safe, what will you use then?



### **ConcurrentSkipListSet**


Since it implements **SortedSet<E>** and **NavigableSet<E>**, it is similar to [﻿TreeSet](https://www.geeksforgeeks.org/treeset-in-java-with-examples/) with an added feature of being concurrent. Since it is a thread-safe, it can be used by multiple threads concurrently whereas TreeSet is not thread-safe.



---

Question:



![image.png](https://eraser.imgix.net/workspaces/WfewMlxJqqLbSHvOCSnP/wWRfOan6zVfYt3tup4I0EWF0rar1/sJePDfVniQoZiolrC88kB.png?ixlib=js-3.7.0 "image.png")

### 1. **Codebase**: One Codebase, Multiple Deploys
- **Principle**: Each microservice should have a single codebase that is tracked in a version control system (e.g., Git). Different versions of the service are deployed from this single codebase.
- **Practice**: Avoid duplicating code across services. Instead, use shared libraries where necessary.
### 2. **Dependencies**: Explicitly Declare and Isolate Dependencies
- **Principle**: Declare all dependencies explicitly using dependency management tools. Each microservice should be self-contained with its dependencies isolated from others.
- **Practice**: Use tools like Maven, Gradle (Java), npm (Node.js), or pip (Python) to manage dependencies.
### 3. **Config**: Store Configurations in the Environment
- **Principle**: Configuration data, such as database URLs, API keys, etc., should be stored in the environment, not in the codebase.
- **Practice**: Use environment variables to manage configurations, ensuring the same codebase can be used in different environments (dev, test, prod) without modification.
### 4. **Backing Services**: Treat Backing Services as Attached Resources
- **Principle**: Treat backing services (e.g., databases, message queues) as attached resources, accessed via URLs or service bindings.
- **Practice**: Attach and detach services easily by changing configuration rather than code.
### 5. **Build, Release, Run**: Strictly Separate Build and Run Stages
- **Principle**: The build, release, and run stages should be separate. Build the code into an executable artifact, combine it with the configuration to create a release, and then run the release in an environment.
- **Practice**: Use CI/CD pipelines to automate the build and release process.
### 6. **Processes**: Execute the App as One or More Stateless Processes
- **Principle**: Each microservice should be stateless, with any persistent data stored in external services like databases.
- **Practice**: Scale microservices horizontally by running multiple instances of stateless processes.
### 7. **Port Binding**: Export Services via Port Binding
- **Principle**: Each microservice should expose its functionality by binding to a port, making it accessible as a service.
- **Practice**: Microservices should listen on a specific port and be accessed via HTTP or other protocols.
### 8. **Concurrency**: Scale Out via the Process Model
- **Principle**: Use the process model to scale out the application. Each process handles a specific task and can be scaled independently.
- **Practice**: Use multiple processes for different workloads, such as web servers, background workers, and batch jobs.
### 9. **Disposability**: Maximize Robustness with Fast Startup and Graceful Shutdown
- **Principle**: Microservices should start up and shut down quickly and gracefully, allowing for rapid scaling and easy recovery.
- **Practice**: Handle termination signals properly and clean up resources (e.g., close connections) before shutting down.
### 10. **Dev/Prod Parity**: Keep Development, Staging, and Production as Similar as Possible
- **Principle**: Keep development, staging, and production environments as similar as possible to avoid surprises during deployment.
- **Practice**: Use similar configurations, dependencies, and environments across all stages.
### 11. **Logs**: Treat Logs as Event Streams
- **Principle**: Treat logs as a stream of time-ordered events, aggregating them in a central location for monitoring and analysis.
- **Practice**: Use tools like ELK (Elasticsearch, Logstash, Kibana) or Splunk for centralized logging and monitoring.
### 12. **Admin Processes**: Run Admin/Management Tasks as One-off Processes
- **Principle**: Any administrative tasks, like database migrations or backups, should be run as one-off processes.
- **Practice**: Use scripts or commands that can be executed as one-time processes separate from the main application.
---

# How do you prevent circular dependency in Spring?


### Cyclic Dependency in Spring
A **cyclic dependency** occurs when two or more beans in a Spring application depend on each other directly or indirectly, creating a cycle. For example, if bean A depends on bean B, and bean B depends on bean A, Spring will encounter a problem when trying to create these beans, leading to a `BeanCurrentlyInCreationException`.



**Using **`**@Lazy**`** Annotation**:

- The `@Lazy`  annotation tells Spring to initialize the bean only when it is first needed, rather than at startup. This can help break the cycle by delaying the creation of one of the beans.
```
@Component
public class ClassA {
    private final ClassB classB;

    @Autowired
    public ClassA(@Lazy ClassB classB) {
        this.classB = classB;
    }
}

@Component
public class ClassB {
    private final ClassA classA;

    @Autowired
    public ClassB(@Lazy ClassA classA) {
        this.classA = classA;
    }
}
```




**Constructor Injection vs. Field/Setter Injection**:

- **Constructor Injection**: This is where cyclic dependency issues often occur, as Spring tries to resolve all constructor arguments before creating the bean.
- **Field/Setter Injection**: Since Spring can set fields or call setters after the bean is created, this can help break the cycle.








---



# Where would you use parallelStream and sequential stream?


### Normal Stream
- **Sequential Processing**: A normal stream processes elements one by one in a single thread. This means it processes each element in the order it appears in the stream.
- **Single Thread**: All operations on the stream are executed in a single thread, so the processing is sequential and predictable.
```
public static void main(String[] args) {
    List<String> names = Arrays.asList("John", "Jane", "Jack", "Doe", "Alice", "Bob");

    List<String> filteredNames = names.stream()
                                      .filter(name -> name.startsWith("J"))
                                      .collect(Collectors.toList());

    System.out.println("Names starting with J: " + filteredNames);
}
```


### Parallel Stream
- **Parallel Processing**: A parallel stream splits the data into multiple chunks and processes them concurrently in different threads. This allows for faster processing on large datasets by taking advantage of multiple CPU cores.
- **Multiple Threads**: It uses the ForkJoinPool under the hood to run operations in multiple threads simultaneously. The results from these threads are combined at the end.
- **Order**: The order of processing is not guaranteed in a parallel stream, especially for unordered data sources like `HashSet` . However, for ordered collections like `List` , the order can still be maintained.


```



    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Jane", "Jack", "Doe", "Alice", "Bob");

        List<String> filteredNames = names.parallelStream()
                                          .filter(name -> name.startsWith("J"))
                                          .collect(Collectors.toList());

        System.out.println("Names starting with J (Parallel): " + filteredNames);
    }


```
### When to Use a Parallel Stream
1. **Large Data Sets**: If you're processing a large collection of elements, parallel streams can help by splitting the workload across multiple threads, potentially reducing the total processing time.
2. **CPU-Bound Tasks**: Tasks that require significant CPU resources (e.g., complex calculations) can benefit from parallel streams because they can take advantage of multiple CPU cores.
3. **No Strict Order Required**: If the order of processing doesn't matter, a parallel stream is a good fit, as it can process elements out of order.
4. **Non-Blocking Operations**: If your stream operations don't involve blocking calls (e.g., I/O operations like reading from a file or a network), parallel streams can work well. Blocking operations can negate the performance benefits by causing threads to wait.




### When Not to Use a Parallel Stream
1. **Small Data Sets**: For small collections, the overhead of managing multiple threads might outweigh the benefits, leading to slower performance than a normal stream.
2. **Order-Sensitive Tasks**: If the order of elements is critical (e.g., when working with ordered collections like lists), a parallel stream might cause issues because it processes elements concurrently, which can disrupt the order.
3. **IO-Bound Tasks**: Operations that involve I/O (e.g., reading from a file, writing to a database) are typically slower and can block threads. Using a parallel stream in such cases might not improve performance and could even degrade it due to context switching and thread management overhead.
4. **Thread-Safe Concerns**: If the operations in the stream are not thread-safe or involve shared mutable state, using a parallel stream can lead to race conditions and other concurrency issues.




# @Bean vs @Component in spring boot- when to use which one 
@Component and @ 



`**@Component**`:

- Used at the class level.
- Automatically detected and registered by Spring’s component scanning.
- Suitable for general-purpose Spring-managed classes.
-  It’s typically used for general-purpose classes that perform various tasks within the application, such as service classes, repository classes, and utility classes.


```


@Component
public class MyComponent {
    
    public void doSomething() {
        System.out.println("Doing something in MyComponent...");
    }
}
```
`**@Bean**`:

- Used at the method level within a `@Configuration`  class.
- Provides explicit bean definition and configuration. 
- Useful for beans that require specific setup or are not automatically detected.
- Use `@Bean`  to create beans for third-party libraries or external resources where you need to configure the bean explicitly.
```


@Configuration
public class MyConfiguration {

    @Bean
    public MyService myService() {
        return new MyService();
    }
}

class MyService {
    
    public void performAction() {
        System.out.println("Performing action in MyService...");
    }
}
```


`**@Component**`  is used for standard Spring-managed components like services, repositories, or controllers. It simplifies bean creation by allowing automatic detection through component scanning.

`**@Bean**`  is used within `@Configuration`  classes to explicitly define and configure beans, especially when you need more control over the bean's creation and configuration process, such as setting up data sources or other external dependencies.





---

# Walk us through the steps you would take to mitigate  slow DB queries for high traffic spring boot app?


###  **Identify Slow Queries**
1. **Enable Query Logging**:
    - **JPA/Hibernate**: Enable SQL logging to capture executed queries. This can be done by setting properties in `application.properties`  or `application.yml` 
    - spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
2. **Analyze Slow Query Logs**:
    - Examine the logs for long-running queries. Identify patterns or specific queries that take longer than expected.
3. **Database Monitoring Tools**:
    - **Database Tools**: Use database monitoring tools (e.g., MySQL Workbench, pgAdmin, Oracle Enterprise Manager) to analyze query performance.
    - **Application Performance Monitoring (APM)**: Tools like New Relic, Dynatrace, or Datadog can help identify slow queries and provide insights into application performance.
### 2. **Analyze and Optimize Queries**
1. **Examine Query Execution Plans**:
    - **Explain Plan**: Use `EXPLAIN`  or `EXPLAIN ANALYZE`  to understand how the database executes the query. Look for full table scans, missing indexes, or inefficient joins.
2. **Optimize Queries**:
    - **Indexes**: Add or adjust indexes to improve query performance. Ensure indexes are used for columns involved in `WHERE` , `JOIN` , and `ORDER BY`  clauses.
    - **Query Refactoring**: Rewrite complex queries or break them into simpler parts. Avoid unnecessary subqueries or complex joins.
3. **Database Schema Optimization**:
    - **Normalization**: Ensure the database schema is properly normalized to reduce redundancy and improve query efficiency.
    - **Denormalization**: In some cases, denormalization can be used to optimize read performance by reducing the need for complex joins.
### 3. **Optimize Application Code**
1. **Efficient Data Access**:
    - **Batch Processing**: Use batch processing for operations involving large volumes of data to reduce the number of database round-trips.
    - **Lazy Loading**: Implement lazy loading where appropriate to avoid loading unnecessary data.
2. **Caching**:
    - **In-Memory Caching**: Use caching solutions like Spring Cache with providers such as Ehcache, Redis, or Memcached to cache frequently accessed data.
    - **Query Result Caching**: Cache the results of expensive queries to avoid hitting the database repeatedly.
3. **Pagination**:
    - **Pagination**: Implement pagination for queries that return large result sets to limit the amount of data processed and transferred at once.
### 4. **Database Configuration and Tuning**
1. **Connection Pooling**:
    - **Configure Connection Pool**: Use a connection pool (e.g., HikariCP) to manage database connections efficiently. Adjust pool size based on traffic and load.
2. **Database Tuning**:
    - **Resource Allocation**: Tune database parameters such as memory allocation, buffer size, and cache settings based on your workload.
    - **Query Optimization**: Periodically review and optimize database configurations and queries.
### 5. **Load Testing and Performance Benchmarking**
1. **Load Testing**:
    - **Simulate Traffic**: Use load testing tools (e.g., JMeter, Gatling) to simulate high traffic and identify performance bottlenecks.
    - **Analyze Results**: Review the results to understand how the application and database perform under load.
2. **Benchmarking**:
    - **Performance Metrics**: Benchmark the performance of queries and application components to track improvements and identify areas needing further optimization.
### 6. **Continuous Monitoring and Improvement**
1. **Monitoring**:
    - **Ongoing Monitoring**: Continuously monitor query performance and application metrics using monitoring tools and APM solutions.
2. **Iterative Optimization**:
    - **Regular Reviews**: Regularly review and optimize queries and application code as new performance issues arise or application requirements change.
### Summary


1. **Identify** slow queries through logging and monitoring.
2. **Analyze** query execution plans and optimize queries and schema.
3. **Optimize** application code, implement caching, and use pagination.
4. **Configure** and tune the database and connection pool.
5. **Load Test** and benchmark to assess performance under high traffic.
6. **Monitor** continuously and make iterative improvements


























# Why do we need an embedded server in Spring?


1. Embedding the server within the application simplifies the deployment process. Instead of having to install a separate application server (like Tomcat or WebLogic) and configure it, you only need to install Java and run the application. This eliminates multiple steps involved in traditional deployment processes.
2.  In a microservices architecture, where numerous microservice instances are deployed, embedding the server allows each microservice to be self-contained. This supports automation in development and deployment, which is crucial for managing many microservices effectively.
3.  Embedded servers fit well with modern containerization practices. When applications are containerized (e.g., using Docker), having an embedded server means the entire application, including the server, is bundled into a single container. This simplifies the deployment and management of applications in environments like Kubernetes.
4. An application with an embedded server can be executed in any environment that has Java installed, without needing to pre-configure or install an external server. This increases portability and ensures consistency across different deployment environments.
5. Embedded server images are generally smaller compared to traditional server setups. This helps in creating lighter container images, which can lead to faster deployment times and more efficient resource usage




![image.png](https://eraser.imgix.net/workspaces/WfewMlxJqqLbSHvOCSnP/wWRfOan6zVfYt3tup4I0EWF0rar1/lpMn8uNLu4GjV6Yk6z0xd.png?ixlib=js-3.7.0 "image.png")



****

Normal logs are often isolated to individual services and lack context across the entire request flow. Distributed tracing provides a unified view by linking logs from multiple services, allowing you to trace the entire request path.



 In a microservices architecture, a single user request might interact with multiple services. Distributed tracing allows developers to follow the journey of that request across services, identify bottlenecks, troubleshoot issues, and improve overall system performance.



A trace represents the complete lifecycle of a request across services. A span is a single unit of work within a trace, representing an operation in a single service. A trace ID is a unique identifier that links all spans within a trace together.



### Key Concepts of Distributed Tracing
1. **Trace**: A trace represents the entire journey of a request as it travels through different services in your system. It’s made up of multiple spans.
2. **Span**: A span is a single unit of work done in a service. It includes details like start time, end time, and metadata about the operation. A trace is composed of one or more spans.
3. **Trace ID**: A unique identifier for a trace, allowing you to follow a request across all services.
4. **Span ID**: A unique identifier for a span, allowing you to identify specific operations within a trace.
5. **Parent-Child Relationship**: Spans can have parent-child relationships, indicating that one span triggered another. This forms a hierarchy that represents the flow of requests.




#### **Spring Cloud Sleuth**
- **Purpose**: Sleuth is a Spring library that adds trace and span IDs to logs, and it can automatically propagate these IDs between services.






# ****






# When Is a Monolith More Appropriate Than Microservices?


A monolithic architecture might be more suitable in these scenarios:

1. **Simple or Small Applications**: For small, straightforward applications where the overhead of managing multiple services would be unnecessary and complex.
2. **Latency-Sensitive Applications**: In applications where low latency is critical, microservices might introduce unwanted latency due to the overhead of network calls between services. A monolithic approach, where all components run in the same memory space, can be faster.
3. **Resource Constraints**: If your team is small or lacks the expertise to manage the complexities of microservices, such as distributed data management, network issues, or service orchestration, a monolith might be simpler to develop and maintain.
4. **Deployment Simplicity**: A monolithic application can be easier to deploy, as you only need to manage a single codebase and deployment process. This can be beneficial if you want to minimize the complexity of your CI/CD pipeline.
5. **Consistency**: Monolithic applications can maintain stronger consistency because all components are typically deployed and updated together. This avoids the versioning issues that can arise in microservices when different services are updated at different times.
6. **Performance Optimization**: If your application requires tight performance optimization and is highly optimized for a specific set of tasks, a monolithic architecture can sometimes offer better performance, as all components can be tightly integrated without the overhead of inter-service communication.


