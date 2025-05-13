
# Microservices Architecture with Spring Boot  

## 🌐 What are Microservices?  

Microservices is an architecture style where an application is broken down into small, independent services that communicate over a network (usually HTTP). Each service focuses on a single business function, is independently deployable, and can be developed, scaled, and maintained separately.

---

## 🚀 Why Use Spring Boot for Microservices?  

Spring Boot provides all the necessary tools and integrations (e.g., REST APIs, Eureka, Config Server, etc.) to build robust, production-ready microservices quickly.

✅ **Benefits of Microservices with Spring Boot**:  
1. Loose coupling between services
2. Independent deployment and scaling
3. Technology flexibility (each microservice can use different tech)
4. Easier debugging, testing, and maintenance
5. Faster time-to-market
---

## 🔧 What is a Service Registry in Microservices? 

A Service Registry is a central directory where all microservices register themselves so they can be discovered and communicate with each other without hardcoding IPs or URLs.

In Spring Boot, the most common Service Registry is Eureka Server (from Netflix OSS).

Note: 
- Eureka server runs by default on port 8761.
- If you run eureka server on port 8761. Client (micro services will automatically register with eureka server)


# 🛠️ Eureka Server Setup Guide

## 📋 Step-by-Step Instructions

### 1️⃣ Create Eureka Server (Service Registry)

| Step | Action | Details |
|------|--------|---------|
| 1a | Create Spring Boot project | Use [start.spring.io](https://start.spring.io) or your IDE |
| 1b | Add dependency | ```xml <dependency> <groupId>org.springframework.cloud</groupId> <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId> </dependency> ``` |
| 1c | Annotate main class | ```java @SpringBootApplication @EnableEurekaServer public class EurekaServerApp { ... } ``` |
| 1d | Configure properties | ```properties server.port=8761 eureka.client.register-with-eureka=false eureka.client.fetch-registry=false ``` |

💡 **Why these settings?**  
Note: 
This configuration in .properties file tells the Eureka Server:
Don't register itself as a client.
Don't try to fetch registry data (because it's the registry). 

### 2️⃣ Run and Access
```bash
Run Eureka Server                                    
Access:  http://localhost:8761   
```

#############################################
### 🛠️ What is Spring Boot Admin Server?
#############################################

Spring Boot Admin Server is a web-based UI dashboard that lets you monitor and manage Spring Boot applications in real time. It provides insight into application health, metrics, environment, thread dumps, and more — all using Spring Boot Actuator endpoints under the hood.

✅ Key Features of Spring Boot Admin:
- Real-time health status of services
- Memory, thread, and CPU usage
- View logs, environment properties, and actuator endpoints
- Email/Slack notifications for service status
- UI-based access to /actuator endpoints

📋 How It Works:
-------------------
a. You create a Spring Boot Admin Server (dashboard).
b. Other Spring Boot apps register as Admin Clients.
c. The Admin Server shows their status and health metrics.

📌 Summary:
Use Actuator to make your app observable.
Use Admin Server to see all your services and their health in one place.

#############################
####  📜 Step-by-Step Instructions:
#############################
1. Set Up Spring Boot Admin Server:
- Create a Spring Boot Project for the Admin Server.

- You can use Spring Initializr or your IDE to create a new Spring Boot project.

- Add Dependencies to your pom.xml:

```xml
<dependency>
  <groupId>de.codecentric</groupId>
  <artifactId>spring-boot-admin-starter-server</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
----------------------------------------------------------------------------------
### Enable Admin Server by adding @EnableAdminServer annotation to your main class:

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import de.codecentric.boot.admin.server.config.EnableAdminServer;

@SpringBootApplication
@EnableAdminServer
public class AdminServerApplication {
  public static void main(String[] args) {
    SpringApplication.run(AdminServerApplication.class, args);
  }
}
```
--------------------------------------------------------------------------------
### Configure application.properties for Admin Server:
```properties
server.port=8080  # Admin Server running on port 8080
spring.boot.admin.ui.title=Spring Boot Admin Server  # Custom title
```

------------------------------------------------------------------------------

##############################
### 📡 What is Zipkin Server?
###############################

Zipkin Server is a distributed tracing system that helps you trace and visualize the flow of requests across multiple microservices. It shows you how long each service call takes and helps diagnose latency issues or failures in a microservices architecture.

✅ Key Features of Zipkin:
----------------------------
- Tracks request paths across microservices (traceId)
- Shows latency and response time of each service
- Helps identify bottlenecks or failures
- Integrates with Spring Cloud Sleuth for automatic tracing

#### Step to Install Zipikin Server:

1. Download Zipkin Servers: https://zipkin.io/pages/quickstart
2. To run jar file (at command prompt with file directory):  java -jar file-name
3. Access that on port 9411



#############################################
### First Microservices App
############################################


1. Create Spring Boot With following dependency

| Dependency Name               | Maven Artifact ID                      | Purpose                             |
|-------------------------------|---------------------------------------|-------------------------------------|
| Eureka Discovery Client       | spring-cloud-starter-netflix-eureka-client | Registers service in Eureka     |
| Spring Boot Admin Client      | spring-boot-admin-starter-client       | Sends monitoring data to Admin UI   |
| Spring Web                    | spring-boot-starter-web                | Enables REST API support             |
| DevTools                      | spring-boot-devtools                   | Enables auto-restart and live reload|
| Spring Actuator               | spring-boot-starter-actuator           | Exposes health/metrics endpoints     |
| Zipkin Tracing                | spring-cloud-starter-zipkin            | Sends tracing data to Zipkin         |
| Sleuth (auto tracing)         | spring-cloud-starter-sleuth            | Generates trace & span IDs           |

### Step 2: Annotate the Main Class with @EnableDiscoveryClient
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class MyServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyServiceApplication.class, args);
    }
}
```

### Step 3: Configure application.properties file

##### Basic Info
```properties
spring.application.name=my-service
server.port=8081
```
## Eureka - Optional if you are running eureka on port 8671
```properties
eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```
## Admin Server
```properties
spring.boot.admin.client.url=http://localhost:8080
management.endpoints.web.exposure.include=*
```
## Zipkin - Optional to mention. It will register with ZIPKIN Automatically
```properties
spring.zipkin.base-url=http://localhost:9411
spring.sleuth.sampler.probability=1.0
```
### Very Important Note:
##########################
✅ Tools That Support Service Name Access via Eureka:

| Tool               | Can Use Service Name (e.g., MICROSERVICES-1) | Requires Eureka + Config                      |
|--------------------|---------------------------------------------|-----------------------------------------------|
| Feign Client       | ✅ Yes                                      | Built-in support (just use @FeignClient)      |
| RestTemplate       | ✅ Yes                                      | Requires @LoadBalanced on bean                |
| WebClient          | ✅ Yes                                      | Requires @LoadBalanced on WebClient builder   |
| Browser / Postman  | ❌ No                                       | Needs actual host URL like host.docker.internal |

------------------
**So how to access application with Service name then**? To do so, we have to use client like Feign, Webclient, RestTemplate.
Seond These Clients provide load balancer concept. Our microservices will continue to run even when url changes as microservice are being accessed using microservice names from eureka server.


### Difference Between Rest Template, Webclient & Feign client
##############################################################


Feature                         | RestTemplate (with setup)  | WebClient (with setup)  | Feign Client (automatic)
------------------------------- | -------------------------- | ----------------------- | --------------------------
Can use microservice name       | ✅ Yes                        | ✅ Yes                     | ✅ Yes
Requires manual configuration   | ✅ Yes (@LoadBalanced)        | ✅ Yes (@LoadBalanced)     | ❌ No
Built-in load balancing         | ✅ Yes                        | ✅ Yes                     | ✅ Yes
URL hardcoded by default        | ✅ Yes                        | ✅ Yes                     | ❌ No
Effort level                    | Medium                     | Medium                  | Low


Example:
```
Tool            | Call Example
--------------- | --------------------------------------------------------------------------------
RestTemplate    | restTemplate.getForObject("http://MICROSERVICES-1/api/data", String.class);

WebClient       | webClient.get().uri("http://MICROSERVICES-1/api/data").retrieve().bodyToMono(); 

Feign Client    | @FeignClient(name = "MICROSERVICES-1")
                | public interface Client {
                |     @GetMapping("/api/data") String getData();
                | }
```

####################################################################################
### Create Micro service 2 - Use Inter microservices communication --> Use Feign Client
####################################################################################

Step 1: Create Micro Service 2 Spring boot project
Step 2: Add Following Dependencies
| Dependency Name               | Maven Artifact ID                      | Purpose                             |
|-------------------------------|---------------------------------------|-------------------------------------|
| Eureka Discovery Client       | spring-cloud-starter-netflix-eureka-client | Registers service in Eureka     |
| Spring Boot Admin Client      | spring-boot-admin-starter-client       | Sends monitoring data to Admin UI   |
| Spring Web                    | spring-boot-starter-web                | Enables REST API support             |
| DevTools                      | spring-boot-devtools                   | Enables auto-restart and live reload|
| Spring Actuator               | spring-boot-starter-actuator           | Exposes health/metrics endpoints     |
| Zipkin Tracing                | spring-cloud-starter-zipkin            | Sends tracing data to Zipkin         |
| Sleuth (auto tracing)         | spring-cloud-starter-sleuth            | Generates trace & span IDs           |
| OpenFeign(Client)             | org.springframework.cloud              | Performs Communication with other microservice|
-----------

Step 3: Create Fiegn Interface

```java
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

@FeignClient(name = "MICROSERVICES-1") 
public interface Client {
	
	@GetMapping("/message")
	public String getData();

}
```

Step 4: (Optional if you get an error creating Client bean, add the following to Main class )
```java
@SpringBootApplication
@EnableFeignClients(basePackages = "com.microservices3")
public class Microservices2Application {

	public static void main(String[] args) {
		SpringApplication.run(Microservices3Application.class, args);
	}
}
```

Step 5:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SecondController {
	
	@Autowired
	private Client client;
	
	@GetMapping("/fromsecondcontroller")
	public String getMessageFromMicroservices1() {
		return client.getData();
	}
}
```

Step 6: Add the following to yaml file:
```yml
spring:
  application:
    name: microservices-3
  boot:
    admin:
      client:
        url: http://localhost:8080

server:
  port: 8085

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka

management:
  endpoints:
    web:
      exposure:
        include: '*'
  tracing:
    sampling:
      probability: 1.0
  zipkin:
    tracing:
      endpoint: http://localhost:9411/api/v2/spans
```
Step 7: Test using this url: http://localhost:8085/fromsecondcontroller

######################################################



#################################################
## Load Balancer Demonstration in microservices
#################################################

In microservice 1 remove port number and start the application on different port using Spring--> run configurations--->arguments---> -Dserver.port=8082 etc

### Basic Info
```properties
spring.application.name=my-service
#server.port=8081-------> remove this
```
# Eureka - Optional if you are running eureka on port 8671
```properties
eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```
# Admin Server
```properties
spring.boot.admin.client.url=http://localhost:8080
management.endpoints.web.exposure.include=*
```
# Zipkin - Optional to mention. It will register with ZIPKIN Automatically
```properties
spring.zipkin.base-url=http://localhost:9411
spring.sleuth.sampler.probability=1.0
```

In Spring Cloud, Feign Client integrates with Ribbon to provide client-side load balancing. Here’s an explanation of how Feign and Ribbon work together. This is auto configured. No extra configuration is required


########################
## Why API gateway?
##################
1. Simplified Client Communication: Clients (mobile apps, web apps) only need to interact with one API endpoint (the API Gateway) rather than multiple microservices.

2. Decoupling: It decouples the client from the internal workings of the microservices. The client doesn't need to know how the services are structured or how they communicate internally.

3. Centralized Management: It centralizes concerns like security, authentication, and logging

How to implement API gateway:
----------------------------

Step 1 create APi gateway Spring boot project with following dependencies
-------------------------------------------------------------------------
```xml
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-gateway</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
```
Step 2: Mention routing in yaml file
--------------------------------------------
```yml
server:
  port: 5555
  
spring:
  application:
    name: API-Gateway

  cloud:
    gateway:
      routes:
        - id: microservice-api-1
          uri: lb://MICROSERVICES-1
          predicates:
            - Path=/micro1/**
          filters:
            - RewritePath=/micro1/(?<segment>.*), /${segment}
        
        - id: microservice-api-3
          uri: lb://MICROSERVICES-3
          predicates:
            - Path=/micro3/**
          filters:
            - RewritePath=/micro3/(?<segment>.*), /${segment}
```
Step 3: Register with eureka server
-----------------------------------
```java
@SpringBootApplication
@EnableDiscoveryClient
public class ApiGatewayApplication {

	public static void main(String[] args) {
		SpringApplication.run(ApiGatewayApplication.class, args);
	}

}
```

Step 4: Perform Testing:
-----------------------------------
http://localhost:5555/micro3/fromsecondcontroller

################################################################################################



################################################
# ✅ What is a Spring Cloud Config Server?
################################################

Spring Cloud Config Server is a central configuration management server that allows you to store, manage, and serve external configurations for all your microservices from a single location — typically from a Git repository or file system.

🔧 Why is it needed?
------------------------------------
- In a microservices architecture:
- Each service may have different configuration (ports, DB URLs, API keys).
- You may want to change config without redeploying the service.
- Managing configs across dozens of services becomes a nightmare.

💡 Key Features:
----------------------------------------
| Feature           | Description                      |                        
|-------------------------------|---------------------------------------|
| Centralized config       |  All services fetch config from one place |


--------



### Best Regards
## Jayant Samal
