## Encrypt with hash and salt

Older implementations, such as SHAPasswordEncoder, would require a salt value when encoding the password.BCrypt, however, will internally generate a random salt instead. This is important to understand because it means that each call will have a different result, and so we need to only encode the password once and it **cannot be decrypted!**(that's why attackers have to use a dictionary to match)

1. Configure the bean
    ```java
    @Configuration
    public class SecurityConfig {
      @Bean
      public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
      }
    }
    ```
2. Implement the service
    ```java
    @Autowired
    private PasswordEncoder passwordEncoder;

    @PostMapping("/register")
    public void registerNewUser(@RequestParam String username, @RequestParam String password) {
      String encoded = passwordEncoder.encode(password);
      String registerQuery = "INSERT INTO login (username, password) VALUES(?,?)";
      Object[] params = {username, encoded};
      Object[] types = {Types.VARCHAR, Types.CHAR}; // not necessary
      secondaryJdbcTemplate.update(registerQuery, params, types);
    }
    ```

### Questions
1. CHAR(60) works, how about BINARY(why should we use BINARY?)
2. Why this is not working? 
    ```
    Object[] types = {Types.VARCHAR, Types.CHAR};
    ```
    `Object[]` as well as `Integer[]` cannot be autoboxed to `int[]`. It causes "Data too long for column 'username'" exception(why?).

### References
1. [Why should we choose bcrypt?](https://codahale.com/how-to-safely-store-a-password/)
2. [为什么要在密码里加点“盐”](https://libuchao.com/2013/07/05/password-salt)
