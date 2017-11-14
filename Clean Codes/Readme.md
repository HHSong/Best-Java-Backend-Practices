# Clean Codes
-- By Hao Hsiang Song

Generally, writing clean codes is as important as writing efficient codes. And, in some cases, we might choose cleaner codes over efficiencies. 

---

### So What is Clean Code?
Clean codes basically are codes written in such a readable way that the piece of codes can be understood easily without exhausive comments.

### So What Would Be Considered Clean?

##### 1. No long functions
Personally, I consider 4 - 10 lines the best length of a java function. Anything longer than could be modulized into helper functions. Multiple helper functions not only provide meaningful name to the piece of code but also simply unit testing (more on Unit Test topic). 

##### 2. Early returning
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

