# RESTful mappinp

## Models

1. We need default constructors in **ALL** classes engaged to resolve incoming JSON object (POST, PUT) 

```java
public class User {

  @Id private String id;
  private String firstName;
  private String lastName;
  private double accountBalance;
  private Address address;

  public User() {}

  public User(String firstName, String lastName, Address address) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.address = address;
  }
  // getters omitted here but needed in production
}
```
```java
public class Address {
  private String country;
  private String city;

  public Address() {};

  public Address(String country, String city) {
    this.country = country;
    this.city = city;
  }
  // getters omitted here but needed in production
}
```

## Controllers
1. `@RestController` returns an object in JSON format 
2. `@RequestBody` receives JSON objects and convert them into Java objects

```java
@RestController
@RequestMapping("/users/portfolio")
public class UserPortfolioController {

  @Autowired
  private UserPortfolioRepository repository;

  @RequestMapping("/all")
  public List<User> getAll() {
    List<User> list = this.repository.findAll();
    return list;
  }
  
  @PostMapping
  public void update(@RequestBody User user) {
    this.repository.save(user);  // have existing id? update, otherwise create this document
  }
}
```
