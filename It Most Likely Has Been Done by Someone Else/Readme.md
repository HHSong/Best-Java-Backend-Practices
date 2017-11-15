# It Most Likely Has Been Done by Someone Else
 -- by Hao Hsiang Song

DRY principle, don't repeat yourself, could be general enough saying that do not re-invent the wheels. Knowing libraries and frameworks is so much productive than writing 10 lines of codes per minute.

---

#### 1. Apache Libraries -- StringUtils
StringUtils provides extensive features including isBlank, padding, replace, search all with null handling. Check StringUtils first before diving into string manipulations.

#### 2. java.util.Collections
Java has build-in package of collections. The most used ones is the List. List is just an interface that defines several member functions, say add, get, addAll, etc, that should be implemented for all implementation. The one I use frequently is the ArrayList. It has array implementation internally. The accessing and adding are constant time operation which is great for most cases. 
When choosing a data structure, we should always keep in mind what the usages are. Are we resizing the list frequently? Are we inserting and deleting elements often times? Do we have to perform searches? We might choose from LinkedList, ArrayList or even a Concurrent data structure in concurrent package.

#### 3. java.util.Map
Map is not part of the collection, yet it is powerful and used ubiquitously. I use HashMap all the time. The implementation behind the scene is a smart hashing function that maps every key passed in (of course the key Objec class should overrides hash and equals functions cause they are used internally when deciding the location where the value should store) into an int that leads to an array of buckets that holds the key-value pair in an inner class entry. 
Note that the hashing and the internal hash collapse multiple values into the same value. That is, there are several entry in the same bucket. We call the phonomenon a collision. Java 7 used to handle collision in a linked list. As one can imagine, the constant access property becomes less favorable when the linked list gets larger. Hence, starting from Java 8, a red-black is introduced internally. When the collision hits the level of 8 elements, it converts the linked list into red-black tree for optimal binary search performance.







