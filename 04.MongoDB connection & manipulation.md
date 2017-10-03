# MongoDB connection & manipulation

## Setup
1. Create User object and UserRepository object. 
    1. We need getters for fields to be included in GET responses.
    2. Use `@Document(collection = "users")` if the collection name is different from the class name.
    ```java
    public class User {
      @Id private String id; // good practice to use @id
      private String firstName;
      private String lastName;
      private double accountBalance;
      // getters    
    }
    ```

    ```java
    @RepositoryRestResource //(collectionResourceRel = "users", path = "users") // routing path
    public interface UserRepository extends MongoRepository<User, String> {
      User findById(String id);
      List<User> findByAccountBalanceGreaterThan(double balance); // explained in Read section
    }
    ```

2. By default, the program will try to connect your local MongoDB. To connect to a remote db (in this case, from mLab), configure in `application.properties`
    ```
    spring.data.mongodb.uri=mongodb://<db user>:<user's pwd>@ds139984.mlab.com:39984/xxx-api
    ```

3. Now you should be about to visit `http://localhost:8080/users` and see the json response.   

## Handle basic CURD request
### Setup
Create a controller to handle all related requests.
  ```java
  @RequestMapping("/users")
  public class UserController {

    @Autowired
    private UserRepository repository;

     // request mapppings
  }
  ```

### Create & Update
```java
  @PostMapping
  public void update(@RequestBody User user) {
    this.repository.save(user);  // have existing id? update, otherwise create this document
  }
```
1. `save()` performs `upsert()`, update the existing item (based on ID) or create a new one if not existed. 
2. Inexistent fields will be ignored.
3. To update a document, send POST requests use the first format (from GET response) rather then the second one (copy from MongoDB).
    ```json
    {
        "id": "59c4981c9df6613de0b873cf",
        "firstName": "John",
        "lastName": "SnowUpdate"
    }
    ```
    ```json
    {
        "_id": {
            "$oid": "59c4981c9df6613de0b873cf"
        },
        "_class": "com.fmc.api.web.model.User",
        "firstName": "John",
        "lastName": "SnowUpdate"
    }
    ```
4. To create a new document, we could also use `insert()` with PUT request. 
    ```java
    @PutMapping
    public void create(@RequestBody User user) {
      this.repository.insert(user);
    }
    ```

### Delete
```java
@DeleteMapping("/{id}")
public void delete(@PathVariable("id") String id) {
  this.repository.delete(id);
}
```

### Read
#### Basic Read
```java
@RequestMapping("/all")
public List<User> getAll() {
  List<User> list = this.repository.findAll();
  return list;
}
```
#### Read with parameters
```java
@RequestMapping("/{id}")
public User getById(@PathVariable("id") String id) {
  User user = this.repository.findById(id);
  return user;
}
```
1. `findAll()` and `getById()`is implemented by Spring. Learn more about the naming conventions in the Query section.
2. Use `@PathVariable` to read parameters.   
3. Inexistent fields (created in Java Object but not in MongoDB) will be set to default value, i.e. for integer is 0.

#### Read with pre-made queries 
```
@RequestMapping("/balance/{minBalance}")
public List<User> getByAccountBalance(@PathVariable("minBalance") double balance) {
  List<User> primeUsers = this.repository.findByAccountBalanceGreaterThan(balance);
  return primeUsers;
}
```
1. Provided functions (GreaterThan) for query methods can be found [here](https://docs.spring.io/spring-data/mongodb/docs/1.2.0.RELEASE/reference/html/mongo.repositories.html)
2. No implementation required

#### Read with self-defined queries
In UserRepository
```java
@Query(value = "{address.city:?0}")
List<User> findByCity(String city);
```
In controller
```java
@RequestMapping("/address/{city}")
public List<User> getByCity(@PathVariable("city") String city) {
List<User> cityUsers = this.repository.findByCity(city);
return cityUsers;
}
```
The value is case sensitive and will differentiate cases, for instance, to get `"city": "New York"`, we need url `/users/address/New%20York`. Yet this approach is not flexible enough.  
