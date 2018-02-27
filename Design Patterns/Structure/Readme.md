## Structure Patterns


#### 1. Adapter
As self-explanatory as it can be, an adapter takes an existing object and exposes common interface to clients. One of my assignment at Innova Solutions was to create an abstraction of logs and event handling for the reason that there is no common interface exisiting. All major vendors like log4j, logback, ... have their own interface. There should not be any problem if we don't switch to another. Yet, somehow a concern was addressed back then and I was asked to provide with such flexibility. And it turned out, log4j did, at a critical landmark point, caused a file lock and blocked all the spring framework processes on the same machine from finishing initialization.

Anyway, by adapting adapter, command and singleton patterns, I exposed a common interface and delegate the actions to specified logging frameworks. Thankfully, the these frameworks provide capability of customizing log levels which varies framework by framework. Thus, even though different vendor has different severity levels, I was able to insert corresponding ones.
```java
public interface BreadCrumb { // BreadCrumb is the code name for the logger
  void lay(Flavor flavor, Object... msgs);
  void trace(Object... msgs);
  void debug(Object... msgs);
  void error(Object... msgs);
  // and so on
}
public class BreadCrumbManager {
  final private static Map<Class, BreadCrumb> registry = new HashMap<>();

  public static getBreadCrumb(Class invoker) {
    if (!registry.contains(invoker))
      init(invoker);
    return registry.get(invoker);
  }
}
public class BreadCrumbLog4jAdapter implements BreadCrumb {
  private Logger logger;
  public BreadCrumbLog4jDelegator(Class invoker) {
    logger = LogManager.getLogger(invoker);
  }

  @override
  public void lay(Flavor flavor, Object... msgs) {
    logger.log(
      toLog4jLevel(flavor),
      msgs
    );
  }

  @override
  public void debug(Object...msgs) {
    lay(Flavor.DEBUG, msgs);
  }

  private Level toLog4jLevel(Flavor flavor) {
    // you get the idea
  }
}
```

#### 2. Bridge 
When it comes to bridge pattern, you will see "decouple an abstraction from its implementation" being in every tutorials. But what does that mean? I took me such a long time to fully diagest the idea. If you google it, all the top hit examples are so dumb that you don't see the point of having the pattern.
So here is my two cents, it is about dimensions again, as I compare Abstract Factory to Factory Method. When there is only one dimension, it is intuitive to have an interface and multiple variation of implementations. However, it becomes clunky when we have higher dimensions. Imagine you have a registration service with 3 different implementation and each of them supports MongoDB, Oracle and Reddis repositories. Instead of creating 3 * 3 implementations, we can have service as interface and repository as another. Each of them was implemented by three different variations. Isn't it the standard practice of 3-layered backend structure? Yeah, it is such a nature that you don't even doubt if that is a pattern. To generalize this, an abstractation holding an interface is definitely a bridge pattern and each of them varies independently.

#### 3. Composite
Similar to Adapter pattern, Composite also exposes common interface to users except that this time, the classes to be commonized are node and collection. A collection is a node but also holds a bunch of more nodes. The classic example is arithmetic expressions are themselves expression.

#### 4. Decorator
In java world, wrapping an object with another object is a swiss knife. Whenever you want to make an adapter, bridge two interfaces, and so on, you wrap! Most design patterns involve wrapping and so does Decorator. In fact, it pushes toward such extend that adding features to an object without modifying the existing class - could be 3rd party libraries that you don't have control over - is done by wrapping the unmodifiable object with a class we defined. And we could further add more by recursively wrapping one with another.
```java
public abstract AbstractDecorator {
  private AbstractDecorator core;
  public AbstractDecorator(AbstractDecorator decorator) {
    core = decorator;
  }
  public void perform() {
    core.perform();
  }
}

public class IPhoneWrapper extends AbstractDecorator {
  private IPhone iPhone;
  // whatever constructor as long as we can have iphone before perform() is called
  @override
  public void perform() {
    iPhone.buzz();
  }
}

public class Bumper extends AbstractDecorator {
  public Bumper(AbstractDecorator decorator) {
    super(decorator);
  }
  @override
  public void perform() {
    protect();
    super.perform();
  }

  private void protect() {
    // protect
  }
}

public class CardHolder extends AbstractDecorator {
  public CardHolder(AbstractDecorator decorator) {
    super(decorator);
  }
  @override
  public void perform() {
    super.perform();
    holdCard();
  }
  private void holdCard() {
    // hold card
  }
}

public class UserClass {
  public void someFuntionUsesIPhone() {
    // do something
    AbstractDecorator iPhoneWithAddOns = new IPhoneWrapper(new IPhone());

    if (isBumperAdded)
      iPhoneWithAddOns = new Bumper(iPhoneWithAddOns);
    if (isCardHolderAdded)
      iPhoneWithAddOns = new CardHolder(iPhoneWithAddOns);

    // test the phone
    iPhoneWithAddOns.perform();
  }
}
```

#### 5. Facade
Think of Facade pattern as ease of use. The whole point is to provide user-friendly interface on top of a complex library. A user without prior knowledge would be able to use the core functionalities of the library. At the same time, advanced users would still be able to take full control over the library through its original APIs. In short, it is one extra entry point of the complicated system. 




