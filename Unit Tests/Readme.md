# Unit Tests
 -- by Hao Hsiang Song

The whle idea of unit testing: writing a function that calls a function and verifies the result all done by myself? Sounds like a scam, at least to me in the beginning.

---

## What Is a Unit Test?
Unit testing, by definition, tests the functionality component by component. We use JUnit in java that provides easy-to-use annotations that we can set up variables, perform the testing and verify the results. JUnit leverages reflection which I would love to talk about in the Dependency Injection topic.

## What Are The Key Concepts?

#### 1. @Before, @Test @After
There isn't much fun saying those out loud. @Before and @After are the annotations for setting up environment and tearing it down before and after each tests respectively. One thing noteworthy is that there could be multiple set-up functions and tear-down functions. However, since JUnit relies on relection, the order of execution is not tweakable for current JUnit. And there is definitely more including @BeforeClass, @AfterClass and all sort of verifications.

### 2. What should we test?
We test the logic flow, i.e. service layer, both positive flow and exception flows as detailed as possible. 
Why not the endpoints? Yes, that is testable with RestAssured. However, the typical endpoint/controller should not have complex functionality. Hence, there is not so much to test. It would be more like test the framework fucntionalities which is absurd. 
How about the Data Access Layer? Well... you do not. It is covered by the integration test. It is pointless, again, to test your ORM library or any connection wrapper library.

### 3. Wait... but my function calls another function that yet calls another function
When testing involves multiple function calls internally, it makes testing hard and the codes grow exponentially. How do we pitch this? Mock it! There are two libraries I am familiar with, Mockito, and PowerMock. The latter was an extension of the prior. 
So what Mockito can do is to create a dummy proxy object that looks like an object but with programmable behaviors called stubbing. 
```java
@Mock private Validator validator;

@Test
public void certainLogicTest () {
  when(validator.validate(myObject)).thenReturn(true);
}

```
We use an annotation version here and we say that the validation should pass no matter what. There is a function call version of mocking. More detail on http://site.mockito.org.

That is weird. I cannot program the private function of the validator and it could be from a 3rd party library say Apache StringUtils. There! PowerMock comes to save the day. It has the ability to use reflection for mocking (private) or protected methods. The parentheses are there because, from a long hour painful experience, there is bug with the Powermock library: private just don't work in certain cases. AND there sometimes is a problem mix-using both Mockito and PowerMock libraries. You will know when you sometimes see error messages like this:
```java
org.mockito.exceptions.misusing.UnfinishedStubbingException: 
Unfinished stubbing detected here:
    -> at com.a.b.DomainTestFactory.myTest(DomainTestFactory.java:355)

    E.g. thenReturn() may be missing.
    Examples of correct stubbing:
        when(mock.isOk()).thenReturn(true);
        when(mock.isOk()).thenThrow(exception);
        doThrow(exception).when(mock).someVoidMethod();
    Hints:
     1. missing thenReturn()
     2. you are trying to stub a final method, you naughty developer!

        at a.b.DomainTestFactory.myTest(DomainTestFactory.java:276)
        ..........
```
Try chaning the mock() package from Mockito to PowerMockito or the other way around. See if problem gets resolved.

### 4. Exception thrown in a function that I specifically programmed to ignore
Another several painful hours here. The when().thenReturn structure always executes the stubbed code. The solution here is to use doReturn().when() function chain. And there are variation that doNothing or sometime handle function with parameters or static or is protected.

### 5. How to prepare the data?
If your test data is hard to generate, that could be a code smell saying it is poorly designed. It should not be hard at all to inject a mocked object. We certainly need not worry so much on initializing the data for test. When it happens, I might even pass a mock data to a mock function.

### 6. Design, design and design
First of Dependency Injection pattern makes it so much easier for decoupling the pieces (more detail on the DI topic). The reason why one cannot set the mocked data is that the modules are highly coupled. An intialization of an object requires an initialization of another. That is where setting up for testing gets nasty. 

Secondly, access control as mentioned helps. What I would do is that helper functions are defined as package protected and your test class happen to be in the same package. So, magically it works haha. Then only those functions I am confident that are less error-prone would be declared private by me.


Thirdly, think about OOP design and responsibilities. A good practice would have multiple objects dedicated for multiple funcitonalities. It all comes back to pluggability. As you can see, how loose coupling is the essence here and there.





