## Behavior Patterns
Human communication is hard, especially on Tinder. Software communication can be as hard depending on dull the other component is. So, why not be cheesy for a moment and start working on a few pick-up lines?

#### 1. Chain of Responsibility
Sometimes, you want to provide simplicity to your client programmer. You, as a multi-headed demogorgon, has no centralized idea of what other heads are doing. So, you simply chain up and either act on or pass along. And the best part, O(1) plugability!

Also, if you have crazy if, else if, else if, ..., else blocks each performs an intensive operation, that is a great chance for CofR to chime in.

Basically, it is just a linked list of operations. 

```java
public abstract class Handler {
  protected Handler next = null;
  public abstract void handle(Request request);
  public Handler getNext() {
    return next;
  }
  public Handler setNext(Handler handler) {
    next = handler;
    return this;
  }
}

pubic class SpillHandler extends Handler {
  @Override
  public void handle(Request request) {
    if (!doable) { 
      next.handle();
      return; // as mentioned in Clean Code section, explicitly tell the reader it ends here.
    }
    // do something
  }
}

public class SwordHandler extends Handler {
  @Override
  public void handle(Request request) {
    if (!doable) { 
      next.handle();
      return;
    }
    // do something
  }
}

public class DemogorgonFactory {
  public static getInstance() {
    return new SwordHandler()
            .setNext(
              new SpillHander()
            );
  }
}
```

#### 2. Command
Honestly, this is so convoluted. This is me trying my best: 

First of all, there are four components
1. Reciever(acutal work is done here)
2. Command(stateful object with receiver and parameters in it)
3. Invoker(a remote control that abstracts function calls)
4. Client(user of our codes)

Secondly, the relationships:
1. Client holds a Invoker and a bunch of Commands he wants
2. Invoker stores a list of Commands and executes them
3. Command is an interface with multiple implementation on execute(). Each of them delegate the call to a Receiver
4. Receiver has the actual logic needed for the Command

Finally, the flow:
1. Receivers are initialized internally in Commands, which is hidden from the Clients
2. Commands are built via a factory, a builder, or new since they are stateful and client sensitive
3. Invoker is stateful as well. It could be as simple as holding a list of Commands and expose one execute() method to the Client. Yet, it could be so complicated as to bookkeep the execution records and stuffs.
4. Client sets up the Invoker with Commands and maybe even passes it along to another user and make the call later on.
*Note that a user no longer is tied to the objects performing the tasks.*
Also note that there is a perfect match to Composite Pattern if you want to reduce complexity by combining the Invoker with Command. That is an Invoker is itself a Command with a list of sub-Commands and They both expose a simple execute() method. And you could still bookkeep your sub-Command executions internally in the class.

Receiver: in order to demonstrate that Receivers are not necessarily the same interface
```java
// one of the Receiver
public interface PizzaOrderer {
  Receipt place(Order order);
}

// another Receiver
public interface MoneySplitter {
  Confirmation split(TransactionInfo info);
}
```

Command
```java
public interface Command {
  void execute();
  void execute(Consumer callback);
}

public class PizzaOderCommand implements Command {
  @Autowired private PizzaOrderer orderer;
  private Order order;

  public execute() {
    orderer.place(order);
  }

  public execute(Consumer callback) {
    Receipt receipt = orderer.place(order);
    callback.accept(receipt);
  }

  // getter and setter
}

public class MoneySplitCommand implements Command {
  @Autowired private MoneySplitter splitter;
  private TransactionInfo info;

  public execute() {
    splitter.split(info);
  }

  public execute(Consumer callback) {
    Confirmation confirmation = splitter.split(info);
    callback.accept(confirmation);
  }

  // getter and setter
}

// Composite Invoker and Command
public class BookkeepInovkerCommand implements Command {
  private List<Command> commands = new ArrayList<>();
  private Consumer bookkeepingCallback; // optional

  public execute() {
    for (Command command : commands)
      command.execute(bookkeepingCallback);
  }

  public execute(Consumer callback) {
    for (Command command : commands)
      command.execute(bookkeepingCallback);
    callback.accept(bookkeepingCallback);
  }

  // add and remove commands
}

```
An example of Command pattern is a Level 3 REST api aka HATEOS. The point is to provide available actions embedded in the response to frontend. The available actions are the Commands in this case with other REST endpoint URLs as Receivers.

#### 3. Interpreter
I do not have much marks regarding to Interpreter pattern. It seems to focus specifically on parsing. I guess any kind of parser is an interpreter of some sort.

#### 4. Iterator
The idea is simple but the implementation could be complex. The reason to use an iterator is to decouple the data structure from accessing. Each of them could vary separately.

Since this is a widely-used pattern in Java collection, I would suggest just go ahead and look into the underlying code in ArrayList implementation. 

#### 5. Mediator
If you have a set of objects that would interact with one another or one with multiple ones, and you want to decouple the binding so that these objects are reusable, introduce a Mediator pattern. Essentially, it is an additional object that in between of the communication. One does not need to know another except for the Mediator.

#### 6. Memento
Memento is a pattern you want to consider when you want to modify a thing and you want it rolled back afterwards. So how is it possible?

There is a basic setup of three classes:
1. Originator: the object with states you want to modify upon
2. CareTaker: despite the naming, this is more like a user of your code
3. Memento: a checkpoint that has essential state

Several remarks here:
1. you have Originator exposes save and restore methods that returns a Memento and takes a Memento respectively.
2. Memento should be "opaque" to the users, which means no operation should or could be performed upon it.
3. A sophisticated example would be Spring transaction management. It handles those service-layer database transactions. That is, several different function calls could be made to databases and got rolled back all together. The way Spring does it is through JDBC Savepoint and AOP. When a function annotated with @Transactional is called, it disables auto-commit feature and initializes a stack of Savepoint. Instead of actually committing each statements to the database, it push a savepoint to the stack and the JDBC connection would pretend that the query was executed. If everything is fine, it will commit the changes in the end. However, things could go south. That's why we want the help of transaction manager. It would retrieve the savepoints and have the state rolled back.

#### 7. Observer aka. Subscriber aka. Listerner
The pattern should be as simple as it sounds. There is a million application including React.js, Redux.js, and every reactive programming paradigm. The implementation should be trivial. Also Spring has build-in annotation that does the job. Even, Java Objec.notifyAll() leverages the pattern. So I guess the question is, why? Why do we want it? 

The answer is obvious: decoupling! We want the ability to actively publish information to an unknown number of Listeners. Meanwhile, a listener should be anonymous to the Publisher. Thererfore, we have one directional dependency in this pattern.

#### 8. State
The pattern describes a state machine. Operations available depend on the states. For that reason, we would have states as different implementation and each time an operation is performed, the state machine changes its state and expose a new set of operations to the user. 

#### 9. Strategy
There is really not much to say about Strategy pattern. It has already internalized in every Java programmer's heart. If you use late binding, you are using Strategy.

#### 10. Template Method
If you have ever used any framework that you don't specify a main function and it runs. How magical! This simply because the framework has a main function. And it is the framework who controls the flow of execution. It simply takes our implementation detail either via Dependency Injection or through overriding methods in a class. This is Template pattern. We set up a placeholder for user of our program to swap in implementation.

#### 11. Visitor
Visitor propose to reserve future extension by having object storing a visitor interface. The object does no more than just calling the visitor for a visit(object). And the visitor, in turn, takes the object and act upon it.


