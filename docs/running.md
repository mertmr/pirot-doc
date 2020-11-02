---
id: running
title: Running Pirot
---
### Building an executable JAR / WAR file

#### With Maven

- To package the application as a "production" JAR, please type:

    `./mvnw -Pprod clean verify`

    This will generate a file `target/pirot-0.0.1-SNAPSHOT.jar` (if your application is called "pirot").


- To package the application as a "production" WAR:

    - Modify the `pom.xml` to change the application packaging to `war` like:

    ```diff
    -    <packaging>jar</packaging>
    +    <packaging>war</packaging>
    ``` 
    
    - Modify the `pom.xml` to change the scope of `spring-boot-starter-undertow` dependency to `provided` like:

    ```diff
        <id>prod</id>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-undertow</artifactId>
    +           <scope>provided</scope>
            </dependency>
        </dependencies>
    ``` 
    - To generate an executable `war` along the original `war`, type command: 
    
    ```bash
    ./mvnw -Pprod clean verify
    ```
  - This will generate these files (if your application is called "pirot"): 
   
    * `target/pirot-0.0.1-SNAPSHOT.war`
    * `target/pirot-0.0.1-SNAPSHOT.war.original` 

## <a name="run"></a> Running in production

### Executing the JAR file without an application server

Instead of deploying to an application server, many people find it easier to have a single executable JAR file.

With the JAR file generated in the previous step, you can run it in "production" mode by typing (on Mac OS X or Linux):

`./pirot-0.0.1-SNAPSHOT.jar`

If you are on Windows, use:

`java -jar pirot-0.0.1-SNAPSHOT.jar`

**Please note** that this JAR file uses the profile we selected when building it. As it was built using the `prod` file in the previous section, it will therefore run with the `prod` profile.

You can specify the context path as an environment variable or as a command line parameter like:
```bash 
java -jar pirot.jar --server.servlet.context-path=/pirot
```

### Running the application in a Docker container

pirot has first-class support for Docker: it bundles your executable JAR file in a Docker image, and run it inside Docker.

To learn how to package your application with Docker, please read our [Docker Compose documentation]({{ site.url }}/docker-compose/).

### Run as a service

It is also possible to run the Jar as a Linux service, and you may want to force in your `pom.xml` file before packaging. To do it, add the following property inside `<configuration>` of `spring-boot-maven-plugin` plugin.

```
<embeddedLaunchScriptProperties>
    <mode>service</mode>
</embeddedLaunchScriptProperties>
```

Next, setup your init.d with: 

`ln -s pirot-0.0.1-SNAPSHOT.jar /etc/init.d/pirot`

Secure your application with:

`chown puser:puser pirot-0.0.1-SNAPSHOT.jar
sudo chattr +i your-app.jar`

Considering `puser` a non-root OS account that will run the application, then the application can be run this way:

`service pirot start|stop|restart`

There are many other options that you can find in [Spring Boot documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment-install.html), including more security steps and Windows service.