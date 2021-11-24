# Springfox-Swagger
Adding swagger to spring boot for API documentation

### Introduction

- Swagger is a tool which helps a developer by automatically writing documentation for their APIs. 
- For more : https://swagger.io/docs/specification/2-0/what-is-swagger/

---

## Adding Swagger to spring boot project

### STEP 1 : Adding the dependencies

```xml
		<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.9.2</version>
		</dependency>


		<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.9.2</version>
		</dependency>

```

### STEP 2 : Making a Congifuration class and enabling swagger

#### SwaggerConfig.java

```java

package com.rohitThebest.config;

import java.util.Collections;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

import springfox.documentation.builders.PathSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@EnableSwagger2
@ComponentScan(basePackages = {"com.rohitThebest"})
@Configuration
public class SwaggerConfig {

}
```

- That's it swagger is enabled now.

### STEP 3 : Running swagger in web browser

- **Note:** : Make sure to add a @RequestMApping("/your_root_path") to the controller class, in this example we are going to use @RequestMapping("/api") as root path.
- hit the endpoint - *http://localhost:8080/swagger-ui.html*
- now you can see something like below
- ![swagger_basic](https://user-images.githubusercontent.com/63965898/143193101-54b87895-3660-4c9d-9df8-a4d76f39b294.png)
- Now you can even test your APIs from web browser itself

---

## Configuring Swagger

- In the above image you must have see that there is no proper title, description, contact details, etc. for your API.
- Also you must have observed an API called *basic-error-controller*, which we don't need.
- In order to configure everything we need to make som changes to the **SwaggerConfig.java** class.

### STEP 1 : Making a Docket Bean

- *Docket* : It is an object which is used to configure Swagger. It follows the builder pattern. 

#### SwaggerConfig.java

```java

@EnableSwagger2
@ComponentScan(basePackages = {"com.rohitThebest"})
@Configuration
public class SwaggerConfig {

	@Bean
	public Docket configureSagger() {
		
		return new Docket(DocumentationType.SWAGGER_2)
				.select()
				.paths(PathSelectors.regex("/api.*"))
				//.apis(RequestHandlerSelectors.basePackage("com.rohitThebest*"))
				.build();
	}

}

```

### STEP 2 : Again hit the same end-point (http://localhost:8080/swagger-ui.html) and observe

- ![image](https://user-images.githubusercontent.com/63965898/143194696-674125ff-ad2c-4188-a428-bceaeff6324c.png)

- we can now see that we are getting the endpoints with "/api/" and not the basic error controller

### STEP 3 : Like this way we can add more information to our API

- *ApiInfo* : It is an object which is used to add basic information about the API.
- An instance of ApiInfo obejct can be added to the Docket and the information will be displayed on the swagger page.

### STEP 4 : Making ApiInfo instance

```java

@EnableSwagger2
@ComponentScan(basePackages = {"com.rohitThebest"})
@Configuration
public class SwaggerConfig {

	
	@Bean
	public Docket configureSagger() {
		
		return new Docket(DocumentationType.SWAGGER_2)
				.apiInfo(apiInfo())
				.select()
				.paths(PathSelectors.regex("/api.*"))
				//.apis(RequestHandlerSelectors.basePackage("com.rohitThebest*"))
				.build();
	}
	
	private ApiInfo apiInfo() {
		
//		return new ApiInfoBuilder()
//				.title("ManageYourRenters API")
//				.version("1.0")
//				.contact(new Contact("Rohit Kumar", "https://github.com/rkumar0206/", "rkumar8092378845@gmail.com"))
//				.build();
//		
		
		// OR
		
		return new ApiInfo(
				"ManageYouRenters API",
				"manage your renters ",
				"1.0", 
				"Need to have an account",
				new Contact("Rohit Kumar", "https://github.com/rkumar0206/", "rkumar8092378845@gmail.com"),
				"API Licence", 
				"https://github.com/rkumar0206", 
				Collections.emptyList());
			
	}
	
}

```

#### STEP 5 : Hit the endpoint again (http://localhost:8080/swagger-ui.html)

- ![image](https://user-images.githubusercontent.com/63965898/143195700-3a5d7c9b-1d2f-4c26-9cd3-3fc75ce0e583.png)

- observe that now there is an appropriate title, description, contact info, etc. for our API.

**For more info on how to configure Swagger got to https://springfox.github.io/springfox/docs/current/#springfox-spring-mvc-and-spring-boot**

---


## Using Swagger Annotations

- There are many annotations out there for making our API documentation more informative.
- Some of them are : 
  1. @ApiOperation - it is used on the request methods for providing additional details about the endpoint.
     ```java
     
      @GetMapping("/{uid}/expenseCategories/uid")
	    @ApiOperation(
			value = "Find all the expense categories by uid",
			notes = "Provide a user id (uid) to get all the expense categories saved by that user",
			response = ExpenseCategory.class
			)
    	public ResponseEntity<HashMap<String, List<ExpenseCategory>>> getExpenseCategoriesByUid(@PathVariable String uid) {

		  HashMap<String, List<ExpenseCategory>> map = new HashMap<>();
		
		  map.put("expenseCategories", service.findByUid(uid));
		
		  return new ResponseEntity<>(map, HttpStatus.OK);
	  }
    
     ```
  2. @ApiModel - Used for adding additional information about the model class. ex : @ApiModel(description = "Expense category info")
    
    ```java
    @ApiModel(description = "Expense category info")
    @Entity
    @Table(name = "expense_category")
    public class ExpenseCategory {

    ```
    
  3. @ApiModelProperty - Used for adding the additional details about the model property.
     ```java
     
      @ApiModelProperty("the unique id given for every expense category")
	    @Id
	    @GeneratedValue(strategy = GenerationType.IDENTITY)
	    private long id;
      
     ```
 - There are many more, go to https://www.javainuse.com/spring/boot_swagger_annotations

