## Delopyment Spring Boot server on AWS
1. Execute the jar file to run the app `java -jar app.jar`
2. Review the page on dns.*compute.amazonaws.com:8080*. To change the port, we can either:
    1. Set property `server.port=80` in `application.properties`, which should be placed in resources folder root to be loaded automatically.Or specify the location with flag `java -jar app.jar --spring.config.location=classpath:/another-location.properties`
    2. Add flag when execute the jar: `java -jar app.jar --server.port=80`
3. However, we will have an error with port=80. On linux ports below 1024 can be opened only by root, so the port 80 is restricted by default. The solution is easy, add sudo: `sudo java -jar app.jar --server.port=80`


## Enabling HTTPS
1. Generate a self-signed certificate in current directory, put the generated `keystore.p12` in resources folder root
    ```
    keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 3650
    ```
2. Configure SSL certificate for Spring Boot project in application.properties by adding
    ```
    server.port: 443
    server.ssl.key-store: classpath:keystore.p12
    server.ssl.key-store-password: [mypassword]
    server.ssl.keyStoreType: PKCS12
    server.ssl.keyAlias: tomcat
    ```
3. However, Spring Boot won't allow configurations for both HTTP and HTTPS in `application.properties`. So we need to do it programatically:
    ```java
    @Configuration
    public class HttpsConfig {

      @Value("${server.port}")
      private int httpsPort;

      @Value("${server.port.http}")
      private int httpPort;

      @Bean
      public EmbeddedServletContainerFactory servletContainer() {
        TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {
          @Override
          protected void postProcessContext(Context context) {
            SecurityConstraint securityConstraint = new SecurityConstraint();
            securityConstraint.setUserConstraint("CONFIDENTIAL");
            SecurityCollection collection = new SecurityCollection();
            collection.addPattern("/*");
            securityConstraint.addCollection(collection);
            context.addConstraint(securityConstraint);
          }
        };
        tomcat.addAdditionalTomcatConnectors(initiateHttpConnector());
        return tomcat;
      }

      // redirect from httpPort -> httpsPort
      public Connector initiateHttpConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setScheme("http");
        connector.setPort(httpPort);
        connector.setSecure(false);
        connector.setRedirectPort(httpsPort); // redirect to httpsPort
        return connector;
      }
    ```
    Configure ports in `application.properties` accordingly:  
    ```
    server.port.http: 8080
    server.port: 8443
    ```
4. Now, all request to `localhost:8080` will be redirected to `https://localhost:8443/`

## Set up domain
In DNS provider's setting, add three A records:  
1. @, public IP of the instance
2. www, public IP of the instance
3. \*, public IP of the instance
[Reference](https://www.namecheap.com/support/knowledgebase/article.aspx/319/2237/how-can-i-set-up-an-a-address-record-for-my-domain)

## Set up a real SSL for HTTPS connections
1. Get csr. A generator can be find [here](https://www.digicert.com/easy-csr/keytool.htm). You may also need some references [here](https://helpdesk.ssls.com/hc/en-us/articles/204299792-How-to-make-sure-domain-is-correct-in-the-CSR-). 


### Set JAVA_HOME (Why it't not set!)
1. Find java location, usually `usr/lib/jvm/`. `java-1.8.0-openjdk-amd64` is simply the shortcut of `java-8-openjdk-amd64`, so we use the real path.
2. Add environment varible for current user by putting these two lines at the end of `sudo /etc/profile`:
    ```
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    export JAVA_HOME
    ```
 3. Flush the result using `source /etc/profile` or `. /etc/profile`
