---
layout: post
section-type: post
title: How to document automatically your rest api with spring boot and swagger
category: tech
tags: [ 'tutorial', 'spring', 'documentation', 'swagger' ]
---

One of the more tedious things when you make a rest api is make the documentation. Tools like swagger, raml, ... do this things a little bit less bored but it still are a very boring task.

With springfox library is very easy create autogenerated documentantion for your rest api based in spring boot.

The first thing that you need is import this library in your project

- Maven

````
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.4.0</version>
</dependency>
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.4.0</version>
</dependency>
````

- Gradle

````
compile("io.springfox:springfox-swagger2:2.4.0")
compile("io.springfox:springfox-swagger-ui:2.4.0")
````

Once you have the libraries in your project the only thing you have to do is configure the springfox beans that this library needs to work propertly.

For that we create its own configuration class and configure it:

````
import com.fasterxml.classmate.TypeResolver;
import org.joda.time.LocalDate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.context.request.async.DeferredResult;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.builders.ResponseMessageBuilder;
import springfox.documentation.schema.ModelRef;
import springfox.documentation.schema.WildcardType;
import springfox.documentation.service.ApiKey;
import springfox.documentation.service.AuthorizationScope;
import springfox.documentation.service.SecurityReference;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.service.contexts.SecurityContext;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger.web.SecurityConfiguration;
import springfox.documentation.swagger.web.UiConfiguration;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.List;

import static com.google.common.collect.Lists.newArrayList;
import static springfox.documentation.schema.AlternateTypeRules.newRule;

@Configuration
@EnableSwagger2
@EnableWebMvc
public class SpringFoxSwaggerConfig {

    @Autowired
    private TypeResolver typeResolver;

    @Bean
    public Docket petApi() {
        return new Docket(DocumentationType.SWAGGER_2).select().apis(RequestHandlerSelectors.any()).paths(PathSelectors.any()).build().pathMapping("/")
                .directModelSubstitute(LocalDate.class, String.class).genericModelSubstitutes(ResponseEntity.class)
                .alternateTypeRules(newRule(typeResolver.resolve(DeferredResult.class, typeResolver.resolve(ResponseEntity.class, WildcardType.class)), typeResolver.resolve(WildcardType.class)))
                .useDefaultResponseMessages(false)
                .globalResponseMessage(RequestMethod.GET, newArrayList(new ResponseMessageBuilder().code(500).message("500 message").responseModel(new ModelRef("Error")).build()))
                .securitySchemes(newArrayList(apiKey())).securityContexts(newArrayList(securityContext()));
    }

    private ApiKey apiKey() {
        return new ApiKey("mykey", "api_key", "header");
    }

    private SecurityContext securityContext() {
        return SecurityContext.builder().securityReferences(defaultAuth()).forPaths(PathSelectors.regex("/anyPath.*")).build();
    }

    List<SecurityReference> defaultAuth() {
        AuthorizationScope authorizationScope = new AuthorizationScope("global", "accessEverything");
        AuthorizationScope[] authorizationScopes = new AuthorizationScope[1];
        authorizationScopes[0] = authorizationScope;
        return newArrayList(new SecurityReference("mykey", authorizationScopes));
    }

    @Bean
    SecurityConfiguration security() {
        return new SecurityConfiguration("test-app-client-id", "test-app-realm", "test-app", "apiKey");
    }

    @Bean
    UiConfiguration uiConfig() {
        return new UiConfiguration("validatorUrl");
    }

}

````

Lets explain what you see in this class:

* EnableSwagger2 annotation indicates to spring context that Swagger support has to be enabled.
* EnableMvc imports the spring MVC configuration
* The more importan thing is the Docket Bean creation, this bean configure the whole spring, swagger and sprinfox integration.

I don't want copy and paste the springfox documentation so <a href=http://springfox.github.io/springfox/docs/current/ target="_blank">here</a> you have the springfox official documentation.

Once configured when you start your spring boot application running it by gradle, maven, or even a executable jar you can get the swagger config in json format in the url: http://localhost:8080/v2/api-docs

And a swagger ui more friendly in this url:

http://localhost:8080/swagger-ui.html