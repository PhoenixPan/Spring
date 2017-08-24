# Spring

## Getting started
https://spring.io/guides/gs/rest-service/

1. Create a Maven project
2. Update Maven project's pom.xml:  
3. Run the project.
    1. Run in IDE
    2. Run in project root folder with cmd `mvn spring-boot:run`
    3. Run .jar. Pack the project first with `mvn clean package`, which will generate a .jar file in target folder in root. Then execute the jar file using `java -jar target/name-version.jar` 
    Possible error: port. The default port is 8080, if it's occupied, run cmd `netstat -ano | findstr: 8080` to find the task that is listening to this port and terminate it with cmd   `taskkill /pid 8080`.  





Add home page: src/main/resources/static(or public)/index.html

`@SpringBootApplication` is equal to `@Configuration`, `@EnableAutoConfiguration`, `@EnableWebMvc`, and `@ComponentScan`.  
