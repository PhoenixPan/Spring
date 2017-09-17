# Annotation
## @SpringBootApplication
* `@SpringBootApplication` is equal to `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`
	* `@Configuration` tags the class as a source of bean definitions for the application context
	* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings
	* `@ComponentScan` tells Spring to look for other components, configurations, and services in the package
* The use of default package is discouraged, or some annotations will go wrong
* Locate your main Application.java in the root package
* Only one `@EnableAutoConfiguration` class should be used, it is recommended to add it to the primary `@Configuration` class, which is main

## @Controller vs @RestController
* `@RestController` is equal to `@Controller` and `@ResponseBody`
* `@RestController` returns a object as JSON (no Jackson config required)
```
@Controller
public class restClassName{

  @RequestMapping(value={"/uri"})
  @ResponseBody
  public ObjectResponse functionRestName(){
      //...
      return instance
   }
}
```
```
@RestController
public class restClassName{

  @RequestMapping(value={"/uri"})
  public ObjectResponse functionRestName(){
      //...
      return instance
   }
}
```

# Other

Add home page: src/main/resources/static(or public)/index.html


## Enabling hot swapping
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```
and this configuration in spring-boot-maven-plugin:
```
<configuration>
    <addResources>true</addResources>
</configuration>
```
