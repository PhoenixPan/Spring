# MySQL server connection

## Configure the `application.properties` file
```
spring.datasource.url=jdbc:mysql://dns.rds.amazonaws.com:3306/database_name
spring.datasource.username=username
spring.datasource.password=password
```

## Import required dependency
```xml
</dependency>
    <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

## Create a Java class which reflects table structure
```java
public class UserAccount {
  private String username;
  private String password;

  public UserAccount(String username, String password) {
    this.username = username;
    this.password = password;
  }
  // getter, setter, and toString() omitted
}
```

## Create a controller to handle the queries
```java
@RestController
@RequestMapping("/login")
public class UserAccountController {

  // Get a logger
  private static final Logger log = LoggerFactory.getLogger(UserAccountController.class); (1)

  @Autowired
  JdbcTemplate jdbcTemplate;

  @RequestMapping("") // to use the default path
  public void login(@RequestParam("username") String username, @RequestParam("password") String password) {
    log.info("Verify: " + username + ", " + password);
    
    // use "?" to prevent SQL injection
    String findQuery = "SELECT * FROM user_account WHERE username = ? AND password = ?"; 
     
    // 1. query(PreparedStatementCreator, PreparedStatementSetter, ResultSetExtractor<T>) returns a List<T>
    // 2. parse the data returned to Java object, a corresponding constructor is required
    List<UserAccount> accounts = jdbcTemplate.query(findQuery, (rs, rowNum) ->
        new UserAccount(rs.getString("username"), rs.getString("password")));
    accounts.forEach(account -> log.info(account.toString()));
  }

  @RequestMapping("/test")
  public void getAll() {
    log.info("All accounts:");
    String selectAllQuery = "SELECT * FROM user_account";
    List<UserAccount> accounts = jdbcTemplate.query(selectAllQuery, (rs, rowNum) ->
        new UserAccount(rs.getString("username"), rs.getString("password")));
    accounts.forEach(account -> log.info(account.toString()));
  }
}
```
