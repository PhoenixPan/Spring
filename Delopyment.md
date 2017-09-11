## Delopyment Spring Boot server on AWS
1. Execute the jar file to run the app `java -jar app.jar`
2. Review the page on dns.*compute.amazonaws.com:8080*. To change the port, we can either:
    1. Set property `server.port=80` in `application.properties`, which should be placed in `src/main/recoures` to be loaded automatically.Or specify the location with flag `java -jar app.jar --spring.config.location=classpath:/another-location.properties`
    2. Add flag when execute the jar: `java -jar app.jar --server.port=80`
3. However, we will have an error with port=80. On linux ports below 1024 can be opened only by root, so the port 80 is restricted by default. The solution is easy, add sudo: `sudo java -jar app.jar --server.port=80`

## Set up domain
1. In DNS provider's setting, add two records:  
    1. A record, @, public IP of the instance
    2. CNAME record, www, public dns of the instance
2. Enabling HTTPS:

Test python server： create an index.html file and `python -m SimpleHTTPServer`


