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
         * for more detail, go to "Department.java" file
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
         * for more detail, go to "UserService.java" file
 ### User information with Department
 [![Screenshot-2021-12-15-at-00-46-07.png](https://i.postimg.cc/6q4NF700/Screenshot-2021-12-15-at-00-46-07.png)](https://postimg.cc/GTRNym68)
