# Learning_Microservices
### - Learning Microservice by implementing with Spring Boot.
* Creating Department Service
    * Create Model, Repository, Service and Controller class for the service/module
    * POST & GET department
* Creating User Service
    * Create Model, Repository, Service and Controller class for the service/module
    * POST & GET user
    * "ValueObject" package is created where inside the package "Department" class is created.
         * reason of creating Department class is User table has an attribute of "departmentId"
         * in this class no annotations and no entity have to create
         * for more detail, go to "[Department.java](https://github.com/Kowshik890/Learning_Microservices/blob/main/user-service/src/main/java/com/example/user/valueobject/Department.java)" file
    * Create one RAPPER object "ResponseTemplateVO" that contains User & Department both together
    * Create GET method in UserController class which return type is ResponseTemplateVO
    * To access data one Microservice from another Microservice-
         * RestTemplate object is created in "ProjectNameApplication.java" file (Here, UserServiceApplication.java)
         ```
            @Bean
            private RestTemplate restTemplate() {
               return new RestTemplate();
            }
         ```
         * for more detail, go to "[UserService.java](https://github.com/Kowshik890/Learning_Microservices/blob/main/user-service/src/main/java/com/example/user/service/UserService.java)" file
 ### User information with Department
 [![Screenshot-2021-12-15-at-00-46-07.png](https://i.postimg.cc/6q4NF700/Screenshot-2021-12-15-at-00-46-07.png)](https://postimg.cc/GTRNym68)

 * Implementing Service Registry
      * Create "Service-Registry" project with dependency "Eureka Server (Spring Cloud Discovery)"
      * Add "@EnableEurekaServer" annotation in ServiceRegistryApplication.java file
      * Add the below PROPERTIES, DEPENDENCIES, DEPENDENCYMANAGEMENT, BUILD in both USER & DEPARTMENT's pom.xml file
      ```
         <properties>
               <java.version>1.8</java.version>
               <spring-cloud.version>2021.0.0</spring-cloud.version>
         </properties>
      ```
      ```
         <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
		   </dependency>

         <dependencyManagement>
		      <dependencies>
			      <dependency>
				      <groupId>org.springframework.cloud</groupId>
				      <artifactId>spring-cloud-dependencies</artifactId>
				      <version>${spring-cloud.version}</version>
				      <type>pom</type>
				      <scope>import</scope>
			      </dependency>
		      </dependencies>
	      </dependencyManagement>

         <build>
		      <plugins>
			      <plugin>
				      <groupId>org.springframework.boot</groupId>
				      <artifactId>spring-boot-maven-plugin</artifactId>
			      </plugin>
		      </plugins>
	      </build>
     ```
      * Add below configuration code in both USER & DEPARTMENT's application.yml file
     ```
         spring:
           application:
             name: PROJECTNAME 
	     
	 // Eureka Client Configuration
         eureka:
           client:
             register-with-eureka: true
             fetch-registry: true
             service-url:
               defaultZone: http://localhost:8761/eureka/
           instance:
             hostname: localhost
     ```
      * LoadBalance annotation
         * it is added in "RestTemplate" method of USER-SERVICE - "UserServiceApplication.java" file
         * it is used when multiple services are available, which connected to e.g. SERVICE-REGISTRY, it will load balance the respective requests 
 * Implementing API Gateway
      * Create an API Gateway where all the request coming from the user or internet will come to the API Gateway first then based on the url pattern it will traverse to the particular microservices
      * Create "Cloud-Gateway" project with dependency "Eureka Discovery Client (Spring Cloud Discovery)", "Gateway (Spring Cloud Routing)" & "Spring Boot Actuator (OPS)"
      * Create "application.yml" file to configure the port number
      * Add below configuration code in both Cloud-Gateway's application.yml file
      ```
         spring:
           application:
             name: PROJECTNAME // API-GATEWAY
	     
	 // Eureka Client Configuration
         eureka:
           client:
             register-with-eureka: true
             fetch-registry: true
             service-url:
               defaultZone: http://localhost:8761/eureka/
           instance:
             hostname: localhost
     ```
      * Add spring->cloud->gateway->routes: this routes will contain the list of different routes.
      ```
      cloud:
	    gateway:
	      routes:
		- id: USER-SERVICE
		  uri: lb://USER-SERVICE
		  predicates:
		    - Path=/users/**
		- id: DEPARTMENT-SERVICE
		  uri: lb://DEPARTMENT-SERVICE
		  predicates:
		    - Path=/departments/**
      ```
      * Add "@EnableEurekaClient" annotation in ServiceRegistryApplication.java file
      * After running all created applications, microservices have been seen in Eureka with their respective port
      [![Screenshot-2021-12-18-at-01-36-58.png](https://i.postimg.cc/HL6PG8vr/Screenshot-2021-12-18-at-01-36-58.png)](https://postimg.cc/qtCGyqTr)
      * Instead of other port number for different microservices, we can use "CLOUD-GATEWAY" port number, which is http://localhost:9191/
      [![Screenshot-2021-12-18-at-01-41-44.png](https://i.postimg.cc/wMXXpSxw/Screenshot-2021-12-18-at-01-41-44.png)](https://postimg.cc/4H37v29c)

 * Implementing Circuit Breaker
 	* It is used to identify which services are not running and it will run the "FALLBACKMETHOD" and notify the users like which service is not working and which not
 	* As a circuit breaker, need to implement "Hystrix Libraries" & "Hystrix Dashboard" to identify which services are running and which one is not in a Dashboard manner
 	* Implement "Hystrix Library" in Cloud-Gateway and FallBackMethods for User-Service & Department-Service 
 	* Add dependency "Hystrix [Maintenance] (Spring Cloud Circuit Breaker)" in Cloud-Gateway's pom.xml
 	```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
		</dependency>
	```
	### N.B: Hystrix Library is not used anymore. Instead of this, "Resilience4J" is used 
	
