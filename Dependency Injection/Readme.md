# Dependency Injection
 -- by Hao Hsiang Song

Dependency Injection, in a nutshell, is a practice of decoupling by separating construction of service objects from using the services. That is, instead of newing an instance, we request one from the container.

---

## How Does DI Work?
A container typically a framework that manages the objects according to user configuration. It scans the designated package using reflection for @Inject/@Autowired annotations. Then it sets the value to the variables via one of the three common way: constructors, setters or directly to the fields. 
The idea behind this is a broader concept called inversion of control.

#### 1. Inversion of Control (IOC)
Inversion of control is the mainstream design principle adapted by most frameworks today. The idea is to let the framework control the flow instead of the (framework) users. It is quite abstract when first switching from an academic programming field.
With the application of template pattern, the implementation detail of the behaviors is left for the users to code. However, it is the framework that gets to decide when your implementation should be executed. (It may be configurable depending on frameworks.)

The most obvious example would be RESTful services. Typically there is no main class visible in the project because the entry point is in the container, say Tomcat. I imagine Tomcat uses a classloader to scan the WAR file deployed. It gets the package infomation from the web.xml and then delegate the request to the described mapping endpoints. (The internal machenism seems super complex. More info [here](https://www.quora.com/How-does-Apache-Tomcat-work-internally))

## Common Java DI Framework

#### 1. Spring DI
Spring, from my point of view, heavily relies on configuration since it is a one-stop shop that provides tons of functionalities including dependency injection, transaction management, jackson integration, etc. Traditionally Spring is configured through XML as can be seen in numerous tutorials. Now, it fully supports annotations. 

Vocabulary in Spring is slightly different: it is @Autowired instead of @Inject (Although @Inject is still usable with limited support), objects being managed are referred as beans. The scope of the beans are configurable as 
- singleton: one bean in the application's lifetime
- prototype: many beans
- request: one in a http request
- session: one in a http session
- global session: one in a http global session

Also, there is an "oppionated" view in Spring, which can be reffered to "convention over configuration". For a backend service, there are @Controller, @Service, and @Repository annotations for the three layer respectively. As far as I can tell, there is absolutely no difference bewteen these three guys. They are plain vanilla serving the same functionality as @Component apart from signaling other developers the purpose of the class. 

If a bean is required in the main class. It could be aquired from the Spring application context.

#### 2. Guice DI
Google also has this dependency injection framework called [Guice](https://github.com/google/guice). The motivation given in the wiki describes a testing problem with a pizza order system. A intuitive way of newing a credit card processor leads to an actual transaction during testing. An improvement could be made by using a factory pattern and swap a dummy processor during the unit test setup. So it is pretty much the same problem I gave in the unit test topic: high coupling. 
The configuration of Guice is through a module class. The basic syntax would be:
```java
bind(SomeInterface.class).to(SomeConcreteClassImplementingIt.class);
```
Similarly, the scopes are configurable (that's the whole point). An instance could be a singleton, request, or session.

#### 3. What if the object I need is from a third party library which I cannot annotate?
Hmm... well both framework supports similar syntax. Write a function annotated with @Bean for Spring and @Provides that initialize an instance and return it. There you go!



