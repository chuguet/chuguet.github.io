---
layout: post
section-type: post
title: How to make a test with InjectMock and Spring
category: tech
tags: [ 'tutorial', 'spring', 'test' ]
---

Surely you have had many times to do a test for a service class which contains injected components inside it.

This is a solution you can use to worry only the class you want tested it and forget the other injections it has.

Imagine we have a service class like this:

````
@Service
public class BasicMongoService {
    @Autowired
    private BasicMongoRepository basicMongoRepository;

    public void save (ObjectToSave objectToSave) {
        basicMongoRepository.save(objectToSave);
    }
}
````

In our tests the only thing we want to test is the method save, we shouldn't care about repository because this repository should have its own test method, so we mock this repository.

The first thing we have to consider is import the dependencies of mock to our application.

If we are working with spring boot and its testing suite, we only have to worry to import the spring starter test dependency, it contains all necesary libraries to allow us made our tests.

For gradle:

````
testCompile("org.springframework.boot:spring-boot-starter-test")
````

For maven:

````
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<version>1.3.3.RELEASE</version>
</dependency>
````

Once we have the dependency imported in our project is time to make our service test.

As I said before the only thing we should to worry is our service and all of the injections that our service has we only mock it.

````
public class BasicMongoServiceTest {

    @InjectMocks
    private BasicMongoService basicMongoService;

    @Mock
    private BasicMongoRepository basicMongoRepository;

    @Before
    public void init () {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void when_service_called_then_object_is_stored_in_DB () {
        ObjectToSave objectToSave = new ObjectToSave();
        objectToSave.setFoo("foo");
        when(basicMongoRepository.save(objectToSave)).thenReturn(new ObjectToSave());

        basicMongoService.save(objectToSave);

        verify(basicMongoRepository, times(1)).save(objectToSave);
    }
}
````

Lets explain this piece of code by steps:

1. For the class we have to test (in our case BasicMongoService) we have to create an attribute that will be annotated with the @InjectMocks annotation, this annotation allow us "tell" to Mockito that this is the class that will be received the mocks injected.
2. Later we see the repository, in this case is an ordinary attribute annotated with mock annotation that indicates to Mockito that it is not a real object but one mocked.
3. We need initialize the annotations to "tell" to Mockito which are the objects should inject to the service test.
4. Finally we develop our test like any other one, to the mock objects we indicate which should be their behaviour and we test our service class.

As you can see this way of testing is very powerful and allow us test common spring classes that usually include one or more injections without worried about this injections and only focus 
in the testing of our service.

Other possibility of testing our service class is inject their dependencies by constructor, it will allow us made the tests without @InjectMocks but this will be treated in other post.
