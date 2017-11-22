# 責任(Reponsibility)
 -- by Hao Hsiang Song

A corperate architect once told me the two-word secret sauce in software development -- 責任(Reponsibility). What he told me was nothing about ownership or professional ethic, but the responsiblities of compenents. Here is why, 

---

#### 1. Anemic domain model
What is this? A highly renowned java developer - [Martin Fowler](https://martinfowler.com) -- introduces an idea of anemic domain model.
>The fundamental horror of this anti-pattern is that it's so contrary to the basic idea of object-oriented designing; which is to combine data and process them together. The anemic domain model is just a procedural style design, exactly the kind of thing that object bigots like me ... have been fighting since our early days in Smalltalk. What's worse, many people think that anemic objects are real objects, and thus completely miss the point of what object-oriented design is all about.
> -- Martin Fowler
From my understanding, as oppose to have 100 in one multicart gameboy, anemic model is simply seperating data from logic. We have POJO(plain old java object) as object that holds the data and we have service class that operates upon those POJOs. Typically, we have a folder named entity or model that has all the anemic models. By doing so, there is a clear structure of application architecture. For example, we have TreeNode as the domain model and Tree as the service.
```java
public class TreeNode {
  int value;
  TreeNode left;
  TreeNode right;
}

public class Tree {
  public int getHeight() {}
  public void insert(TreeNode node, TreeNode another) {}
}
```

Furthermore, I combine the idea with responsibilities. I am not proposing having no function in a POJO. The design I adapted is based on responsibilities. What is the reponsibility of the class? Is it the Tree's responsibility to get a string value from the TreeNode or is it TreeNode's own responsibility? Take a look at HashMap implementation, 
```java
Entry(int h, K k, V v, Entry<K,V> n) {
  value = v;
  next = n;
  key = k;
  hash = h;
}

private abstract class HashIterator<E> implements Iterator<E> {
  Entry<K,V> next;        // next entry to return
  int expectedModCount;   // For fast-fail
  int index;              // current slot
  Entry<K,V> current;     // current entry
}
```
There is an entry inner class that holds the actually data for each key value pair and there is iterator inner class that is responsible for the iteration and operation. However, there is definitely some responsiblity of entry's say toString() and equals(). It is not the iterator's job to know that actual structure of the entity is to get the string value of it. And it certainly is not its job to tell if two entries are equal because entry implementation should be an unknown to the iterator. 

#### 2. Three-layered service architecture
So they are Application layer, Business layer, and Data layer. Each of them presents different responsibilities: gain controls to the frontend, perform business logic, data storage operation. And of course, there is one more layer at frontend for presentation. The typical folder structure would be:
```
|
|--- controller (resource)
|
|--- service
|
|--- repository (DAO)
```
The backend is seperated in this way so that the application layer is reponsible for providing controls while the business layer does the validation and data transformation, and the data layer interact with whatever data storage you are having. Think about responsiblities, if we are introducing a new data storage server say MongoDB, do you verify the null data in the new MongodbRepository class? Definitely not! Whatever data passing from service layer should be trustworthy. Same for the other two layers, do you care about ORM (object-realational-mapping) in service layer? What if there is MongoDB, Oracle, Redis, and file system, the services would be overwhelmed by numerous result set parsing.

#### 3. Database is for storage
I have seen a company intensely use stored procedure for business operation. Even single insert query has its own stored procedure. And they say it is fast. Well... it is, but do you have enough compuational resources? If you have billion-dollar database servers, never mind. If you don't, the db would be a bottleneck for your server scalibility. The modern architecture gradually moves toward microservices. It is so much easier to spin up light-weight container like docker or vm boxes to overload your server computational resources. And there is more resilient stuff in microservices which is off the topic. So, treate you database as storage. Think about responsibility!


#### 4. Single Responsibility
I bet you and I both have seen something like this:
```java
public Data getArchieve(People people) {
  Data data = null;
  Flag flag = new Flag();
  if (people.getDepartment() == HR) {
    ...
    SecurityChecker.verify(people);
    ...
    RawData raw = hrDptHandler.handle(people);
    hrData = new MaskedData(raw);
    flag.setIsAuthorized(true);
    ...
  } else {
    ...
    ...
    ...
    generalData = generalHandler.hand(people);
    flag.setIsAuthorized(false);
    ...
  }

  ...
  // we use the value set previously
  if (flag.isAuthorized()) {
    ...
    data = hrParser.parse(hrData);
  } else {
    ...
    data = generalParser.parse(generalData);
  }
  return data;
}
```
The flow for different data values or data types are fundamentally different, yet there are functions so generic and so testable that it has convoluted if else blocks in order to process various of inputs. From my point of view, this is definitely a bad smell. 

First of all, the if else blocks tend to go so deep that it would not pass sonar scans. Also, it is so hard to keep tracking all the conditions for developers.

Secondly, the code is so hard to test that we have to prepare data in so many highly coupled way. Imagine we have 12 different test cases written for this function and they all pass. Now as the requirement changes, there is a new condition introduced and the flag has one more attribute that signals the approval for specific accounting department. Since we never had this flag before, the changes might break all 12 cases where 10 of them should not be affected in any condition.

The problem here is that the reponsibility of this function is no longer singular. It controls the flow, manipulates the data, verifies the input, and so on. Potentially the best thing we can do is to separate the concerns by having modules serving different purposes: flow control, verificaiton, data manipulation, etc.



