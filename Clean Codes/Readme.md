# Clean Codes
-- By Hao Hsiang Song

Generally, writing clean codes is as important as writing efficient codes. And, in some cases, we might choose cleaner codes over efficiencies. 

---

## So What is Clean Code?
Clean codes basically are codes written in such a readable way that the piece of codes can be understood easily without exhausive comments.

## So What Would Be Considered Clean?

#### 1. No long functions
Personally, I consider 4 - 10 lines the best length of a java function. Anything longer than could be modulized into helper functions. Multiple helper functions not only provide meaningful name to the piece of code but also simply unit testing (more on Unit Test topic). 

#### 2. Early returning
In order to minimize lines and show off to others, people on Leetcode really some effort on shrinking the codes. One of the coding style I found useful is early returns, at least that is how I think of it. When implementing complex logic or checking corner cases, there usually are bunch of conditions that should be met before the main logic, for example null checks.
Take a look at this code:
```java
if (node != null) {
  if (node.value > = 0) {
    sum += node.val;
    if (node.left != null) {
      // ...
      // do something
      // ...
    } else {
      // do nothing
    }
  } else {
    // it seems to do nothing
  }
}
return sum;
```
Hmm... well ... I think it might be summing up the values, but I could not keep trace of all conditions.
Let's look at another version:
```java
if (node == null)
  return 0;
sum += node.val;
if (node.left == null)
  return sum;
// ...
// do something
// ...
return sum;
```
Right! I am confident there are trivial conditions and the returned values are straightforward! Humans have limited memory. We could not keep branching out for different conditions. This early returning makes us focus just on main logic.


#### 3. Consistent Layout
Either tabs or spaces for indentation are fine as long as the whole team follows the same template. Not only does it improve readibility but also avoid potential git merge conflicts because git sees tabs different from spaces. If someone who does not follow the guideline hits reformat. Whoever merges his code would see the whole file conflicting.


#### 4. Meaningful variable names
Obviously, meaningful names are far effective than having bunch of comments explaining them later on. Good practices, for example, would be plurals for collection. 

#### 5. For each
Let's jump into example:
```java
for (List item : items) {
  // do something
}

```
This is for each loop. It is sightly different from getting each item by the index. The implementation behind the for each loop is to use iterators. Collections including List are iterables. Thus they are perfectly suitable for this usage. Note that arrays are not part of the collections, yet it can be iterated through a for each loop too! 
Bottom line here is whether use a ordinary for loop or the for each version totally depend on the scenario. When specific index or element counts is involved, for each loop becomes clumsy. Choose them wisely!


#### 6. Do not reuse variable
Within a function, there could be multiple usages of the same data type, say int. In some scenario, a variable is of no use from a point on. It might seem to be a good memory management to resue it for a totally different purpose. Please don't! It does not save you any memory at all. Modern java compiler is smart enough to optimize variable usages when compiling. Excessive variable declares are omitted. If you decompile a class file, you will see how meaningless v1 is used throughout the whole function. Isn't java great?
(One more thing, it used to be a good practice having the keyword final for arguments. However, I recall that modern compiler also does the optimization for you. I personally think, for readibility, some final could be omitted as well.)


#### 7. Public, protected, private or default
The access controll is there for a reason. It maintains data and flow integrity which is good. Yet, it provides useful information for readibility: whether it is a public API or a private helper function, whether this piece of code is potentially unit tested (more on unit test topic)?


#### 8. Biolerplates

#### 9. Functional style




