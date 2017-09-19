# MongoDB connection

Create User object and UserRepository object. 

```java
public class User {
  @Id private String id; // good practice to use @id
  private String firstName;
  private String lastName;
  // getters and setters are ignored here
}
```

```java
@RepositoryRestResource(collectionResourceRel = "users", path = "users") // routing path
public interface UserRepository extends MongoRepository<User, String> {
  User findByLastName(@Param("name") String name);
}
```

By default, the program will find MongoDB at localhost. To connect to a remote db (in this case, from mLab), configure in `application.properties`
```
spring.data.mongodb.uri=mongodb://<db user>:<user's pwd>@ds139984.mlab.com:39984/xxx-api
```

Now you should be about to visit `http://localhost:8080/users` and see the json object.   
