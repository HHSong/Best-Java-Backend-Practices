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
  @override
  public void handle(Request request) {
    if (!doable) { 
      next.handle();
      return; // as mentioned in Clean Code section, explicitly tell the reader it ends here.
    }
    // do something
  }
}

public class SwordHandler extends Handler {
  @override
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


#### 3. Interpreter
#### 4. Iterator
#### 5. Mediator
#### 6. Memento
#### 7. Observer
#### 8. State
#### 9. Strategy
#### 10. Template Method
#### 11. Visitor