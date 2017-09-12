## Delopyment Spring Boot server on AWS
1. Execute the jar file to run the app `java -jar app.jar`
2. Review the page on dns.*compute.amazonaws.com:8080*. To change the port, we can either:
    1. Set property `server.port=80` in `application.properties`, which should be placed in `src/main/recoures` to be loaded automatically.Or specify the location with flag `java -jar app.jar --spring.config.location=classpath:/another-location.properties`
    2. Add flag when execute the jar: `java -jar app.jar --server.port=80`
3. However, we will have an error with port=80. On linux ports below 1024 can be opened only by root, so the port 80 is restricted by default. The solution is easy, add sudo: `sudo java -jar app.jar --server.port=80`

## Set up domain
In DNS provider's setting, add two records:  
1. A record, @, public IP of the instance
2. CNAME record, www, public dns of the instance
[Reference](https://www.namecheap.com/support/knowledgebase/article.aspx/319/2237/how-can-i-set-up-an-a-address-record-for-my-domain)

## Enabling HTTPS
1. Generate a self-signed certificate in current directory, put the generated `keystore.p12` in project root folder
    ```
    keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 3650
    ```
2. Configure SSL certificate for Spring Boot project in application.properties by adding
    ```
    server.port: 443
    server.ssl.key-store: keystore.p12
    server.ssl.key-store-password: mypassword
    server.ssl.keyStoreType: PKCS12
    server.ssl.keyAlias: tomcat
    ```
3. However, Spring Boot won't allow configurations for both HTTP and HTTPS in application.properties. 




Test python server： create an index.html file and `python -m SimpleHTTPServer`




### Set JAVA_HOME (Why it't not set!)
1. Find java location, usually `usr/lib/jvm/`. `java-1.8.0-openjdk-amd64` is simply the shortcut of `java-8-openjdk-amd64`, so we use the real path.
2. Add environment varible for current user by putting these two lines at the end of `sudo /etc/profile`:
    ```
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    export JAVA_HOME
    ```
 3. Flush the result using `source /etc/profile` or `. /etc/profile`
