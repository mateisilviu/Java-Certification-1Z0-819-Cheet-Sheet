## Generics and Collections

### Using method references

- method reference and lambda behave the same way
- can be applied to static methods, instance methods on a particular instance, constructors, instance methods on a parameter determined at runtime
- you should know each method signature if takes 1,2 etc parameter and what returns

#### Static methods

- Java extracts the right method usage based on context

```Java
Consumer<List<Integer>> methodRef = Collections::sort;    // sort is overloaded but Java knows it's a Consumer and calls sort with 1 parameter
Consumer<List<Integer>> lambda = x -> Collections.sort(x); 
```

#### Instance Methods on a Particular Object

```Java
var str = "abc";
Predicate<String> methodRef = str::startsWith;    // takes a parameter and returns boolean , Predicate is
Predicate<String> lambda = s -> str.startsWith(s);

var random = new Random();
Supplier<Integer> methodRef = random::nextInt;    //no need to take parameter so that's way Supplier is used 
Supplier<Integer> lambda = () -> random.nextInt();
```

#### Instance Methods on a Parameter

```Java
  Predicate<String> methodRef = String::isEmpty;
  Predicate<String> lambda = s -> s.isEmpty();
  
  BiPredicate<String, String> methodRef2 = String::startsWith;
  BiPredicate<String, String> lambda2 = (s, p) -> s.startsWith(p);
```

#### Constructors

- usually for constructors a _Supplier_ is provided

```Java
    Supplier<List<String>> methodRef5 = ArrayList::new;
    Supplier<List<String>> lambda5 = () -> new ArrayList();
    // all 3 are equivalent :
  Function<Integer, List<String>> methodRef6 = ArrayList::new;
  Function<Integer, List<String>> lambda6 = (Integer x) -> new ArrayList<String>(x);
  Function<Integer, List<String>> lambda6a = x -> new ArrayList(x);
```

#### Reviewing Method References

| Type                                    | Before colon           | After colon | Example           |
| --------------------------------------- | ---------------------- | ----------- | ----------------- |
| Static methods                          | Class name             | Method name | Collections::sort |
| Instance methods on a particular object | Instance variable name | Method name | str::startsWith   |
| Instance methods on a parameter         | Class name             | Method name | String::isEmpty   |
| Constructor                             | Class name             | new         | ArrayList::new    |

```Java
public class Penguin {
       public static Integer countBabies(Penguin... cuties) {
          return cuties.length;
       }
       
       public static void main(String[] args) {
     // no parameters
        Supplier<Integer> methodRef1 = Penguin::countBabies; 
        Supplier<Integer> lambda1 = () -> Penguin.countBabies();

     // Penguin parameter, returns Integer :
        Function<Penguin, Integer> methodRef2 = Penguin::countBabies; 
        Function<Penguin, Integer> lambda2 = (x) -> Penguin.countBabies(x);
        Function<Penguin, Integer> lambda2a = (Penguin x) -> Penguin.countBabies(x);

     // 2 Penguin parameter, returns Integer
        BiFunction<Penguin, Penguin, Integer> methodRef3 = Penguin::countBabies;
        BiFunction<Penguin, Penguin, Integer> lambda3 =  (x, y) -> Penguin.countBabies(x, y); 
        BiFunction<Penguin, Penguin, Integer> lambda3a =  (Penguin x, Penguin y) -> Penguin.countBabies(x, y); 
      // this could exists in theory : 
        Custom100Function<Penguin,Penguin,...,Penguin, Integer> methodRef100 = Penguin::countBabies; 
       }

    }
 ```

### Wrapper Classes

- _autoboxing_ happens when compiler converts a primitive to the corresponding wrapper
- _unboxing_ happens when compiler converts a wrapper class back to a primitive
- compiler will always prefer methods that match the primitive or the wrapper class and not execute autoboxing , unboxing

| Primitive type | Wrapper class | Example of initializing    |
| -------------- | ------------- | -------------------------- |
| boolean        | Boolean       | Boolean.valueOf(true)      |
| byte           | Byte          | Byte.valueOf((byte) 1)     |
| short          | Short         | Short.valueOf((short) 1)   |
| int            | Integer       | Integer.valueOf(1)         |
| long           | Long          | Long.valueOf(1)            |
| float          | Float         | Float.valueOf((float) 1.0) |
| double         | Double        | Double.valueOf(1.0)        |
| char           | Character     | Character.valueOf('c')     |

```Java
List<Integer> numbers = new ArrayList<Integer>();g
numbers.add(1);      // autoboxing to Integer , 1 is added : [1] 
numbers.add(Integer.valueOf(3)); // Integer , 3 is added : [1,3]
numbers.add(Integer.valueOf(5)); // Integer , 5 is added : [1,3,5]
// remove is overloaded remove(int index) ; remove(Object o)
numbers.remove(1);     // no autoboxing executed as direct primitive prefered, so element with index 1 removed : [1,5]
numbers.remove(Integer.valueOf(5)); // remove(Object o) applied , 5 is removed 
System.out.println(numbers);  //outputs [1]
```

### Diamond Operator

- is used for shorhand notation of long declaration of generics
- could exists only in the right side of = , before = will cause a compilation error

```Java
        Map<Long,List<Integer>> mapLists = new HashMap<Long,List<Integer>>();
        Map<Long,List<Integer>> mapLists2 = new HashMap<>(); // usage of <> short code 
         
        List<> list = new ArrayList<Integer>();      // DOES NOT COMPILE - diamond <> cannot exist on left side
        
        // not equivalent 
        var list1 = new ArrayList<Integer>(); // ArrayList<Integer>
        var list2 = new ArrayList<>();  // ArrayList<Object>
```

### Lists, Sets, Maps, and Queues

- List : a ordered collection of elements that allow duplicate entries, elements are accesed by an _int_ index
- Set : a collection that dose not allow duplicate entries
- Queue : a collection of elements where order of processing is important (first-in,first-out, etc)
- Map : a collection where keys are used to hold values and no duplicate keys are allowed

#### Common Collections Methods

- List , Queue , Set are under _Collection_ interface
- Map is a collection but not related via inheritence with _Collection_ interface

##### add()

- boolean add(E element) // method signature
- adds an element to a collection and returns _true_ if succesfull or _false_ otherwise
- adding to a _Set_ might return _false_ if element already exists
- trick question could contain method _put_ that exists for _Map_ causing compilation error

```Java
Collection<String> list = new ArrayList<>();
System.out.println(list.add("Element")); // true
System.out.println(list.add("Element")); // true

Collection<String> set = new HashSet<>();
System.out.println(set.add("Element")); // true
System.out.println(set.add("Element")); // false
System.out.println(set.put("Alien")); // DOSE NOT COMPILE - 'put' not a method of Set
```

##### remove()

- boolean remove(Object object) // method signature
- boolean remove(int index) // method signature
- removes a single value that will match the object in parameter
- removing an element while looping cause a ConcurrentModificationException unless specific types of lists are used
- using an _int_ as parameter will remove respective element on the index and not match an object

```Java
Collection<String> birds = new ArrayList<>();
birds.add("hawk");                            // [hawk]
birds.add("hawk");                            // [hawk, hawk]
System.out.println(birds.remove("cardinal")); // false
System.out.println(birds.remove("hawk"));     // true - removes only 1st match 
System.out.println(birds);                    // [hawk]
birds.remove(100);         // IndexOutOfBoundsException

for (String bird : birds) // ConcurrentModificationException
   birds.remove(bird);
```

##### isEmpty() and size()

- boolean isEmpty() // method signature
- int size() // method signature
- isEmpty returns _true_ boolean when the collection is empty or _false_ otherwise
- size returns an integer represeting number of elements in collection
- sometimes tricky question call .lenght() or .lenght ; this results in compilation error

##### clear()

- void clear() // method signature
- a way to discard all elements of a collection

##### contains()

- boolean contains(Object object) // method signature
- checks if an element exists in collection , returns _true_ or _false_
- method calls _equals()_ so a bad implementation could result in bad outcome

##### removeIf()

- boolean removeIf(Predicate<? super E> filter) // method signature
- removes all elements that match predicate in parameter

```Java
  Collection<String> list = new ArrayList<>();
    list.add("Magician");
    list.add("Assistant");
    list.add("");
    System.out.println(list);     // [Magician, Assistant,""]
    list.removeIf(s -> s.startsWith("A")); // literal "A" as parameter 
    list.removeIf(String::startsWith); // DOSE NOT COMPILE - a literal is need as parameter
    list.removeIf(String::isEmpty); // "" is removed from list 
    System.out.println(list);     // [Magician]
```

##### forEach()

- void forEach(Consumer<? super T> action) // method signature
- used to loop a collection and takes a _Consumer_ and dose not return anything

#### List Interface

- _ArrayList_
  - is a resizable array, that automatically grows when new elements are added
  - access is in constant time (best usage when reading is most offen than writing)
  - adding/removing element slower than reading
- _LinkedList_
  - implements both _List_ and _Queue_
  - advantage of usage when you need to add,remove from beginning and end of list in constant time (use it like a queue)

##### Creating a List with a Factory

| Method                  | Description                                                      | Can add elements? | Can replace element? | Can delete elements? |
| ----------------------- | ---------------------------------------------------------------- | ----------------- | -------------------- | -------------------- |
| Arrays.asList(varargs)  | Returns fixed size list backed by an array                       | No                | Yes                  | No                   |
| List.of(varargs)        | Returns immutable list                                           | No                | No                   | No                   |
| List.copyOf(collection) | Returns immutable list with copy of original collection's values | No                | No                   | No                   |

##### List Methods

| Method                               | Description                                                                                                                      |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| boolean add(E element)               | Adds element to end (available on all Collection APIs)                                                                           |
| void add(int index, E element)       | Adds element at index and moves the rest toward the end                                                                          |
| E get(int index)                     | Returns element at index                                                                                                         |
| E remove(int index)                  | Removes element at index and moves the rest toward the front                                                                     |
| void replaceAll(UnaryOperator< E> op) | Replaces each element in the list with the result of the operator                                                                |
| E set(int index, E e)                | Replaces element at index and returns original. Throws IndexOutOfBoundsException if the index is larger than the maximum one set |

```Java
  List<String> list = new ArrayList<>();
  list.add("SD");                  // [SD]
  list.add(0, "NY");               // [NY,SD]
  list.set(1, "FL");               // [NY,FL]
  System.out.println(list.get(0)); // NY
  list.remove("NY");               // [FL]
  list.remove(0);                  // []
  list.set(0, "?");                // IndexOutOfBoundsException

  List<Integer> numbers = Arrays.asList(1, 2, 3);
  numbers.replaceAll(x -> x*2);
  System.out.println(numbers);   // [2, 4, 6]
```

#### Set Interface

- _HashSet_
  - elements are stored in a _hash table_ based on _hashCode()_ method called for the stored object
  - adding a duplicate element into collection is checked in constant time
  - order of elements added is lost
- _TreeSet_
  - elements are stored into a sorted tree structure
  - main benefit is that the collection is always sorted
  - adding and retriving elements takes longer compared to _HashSet_ most evident when dataset is very big

##### Set Methods

```Java
  Set<Character> duplicate = Set.of('a', 'a', 'b'); // IllegalArgumentException: duplicate element: a
  Set<Character> letters = Set.of('z', 'o', 'b');
  Set<Character> copy = Set.copyOf(letters);
  
  Set<StringBuilder> sbSet = Set.of(new StringBuilder("test1"), new StringBuilder("test2"));
  sbSet.forEach(System.out::println);  // "test1 test2"
  sbSet.forEach(x -> x.append(" modified")); 
  sbSet.forEach(System.out::println);  // "test1 modified test2 modified"
  
  letters.add('n');   // UnsupportedOperationException 
  copy.add('o');    // UnsupportedOperationException 
  letters.remove('t'); // UnsupportedOperationException
  copy.remove('k');   // UnsupportedOperationException 
  
  letters.add(null);  // NullPointerException
  copy.add(null);     // NullPointerException
```

#### Queue Interface

- _FIFO_ first-in,first-out
- _LIFO_ last-in,last-out or also known as _stack_
- _LinkedList_
  - it's a double ended queue, elements can be inserted and removed from both front/back
- _ArrayDeque_
  - not in scope of exame

##### Queue Methods

| Method             | Description                                                                      | Throws exception on failure |
| ------------------ | -------------------------------------------------------------------------------- | --------------------------- |
| boolean add(E e)   | Adds an element to the back of the queue and returns true or throws an exception | Yes                         |
| E element()        | Returns next element or throws an exception if empty queue                       | Yes                         |
| boolean offer(E e) | Adds an element to the back of the queue and returns whether successful          | No                          |
| E remove()         | Removes and returns next element or throws an exception if empty queue           | Yes                         |
| E poll()           | Removes and returns next element or returns null if empty queue                  | No                          |
| E peek()           | Returns next element or returns null if empty queue                              | No                          |

```Java
Queue<Integer> queue = new LinkedList<>();
System.out.println(queue.offer(10)); // true [10]
System.out.println(queue.offer(4));  // true [10]-[4]
System.out.println(queue.peek());    // 10   [10]-[4]
System.out.println(queue.poll());    // 10   [4]
System.out.println(queue.poll());    // 4    [null]
System.out.println(queue.peek());    // null
```

#### Map Interface

- _HashMap_
  - is a map implementation where the keys are stored in a hash table
  - _hashCode()_ method is called for the objectat that will be added into collection
  - retriving an existing element by key is in constant time
  - order of elements added is not preserved
- _LinkedHashMap_
  - not in scope of the exam
- _TreeMap_
  - is a map implementation where the keys are stored into a sorted tree structure
  - keys are always in sorted order
  - adding and checking if a key is present takes longer proportional with data set size

##### Map Methods

| Method                                             | Description                                                                                                 |
| -------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| void clear()                                       | Removes all keys and values from the map.                                                                   |
| boolean containsKey(Object key)                    | Returns whether key is in map.                                                                              |
| boolean containsValue(Object value)                | Returns whether value is in map.                                                                            |
| Set< Map.Entry< K,V>> entrySet()                   | Returns a Set of key/value pairs.                                                                           |
| void forEach(BiConsumer(K key, V value))           | Loop through each key/value pair.                                                                           |
| V get(Object key)                                  | Returns the value mapped by key or null if none is mapped.                                                  |
| V getOrDefault(Object key, V defaultValue)         | Returns the value mapped by the key or the default value if none is mapped.                                 |
| boolean isEmpty()                                  | Returns whether the map is empty.                                                                           |
| Set< K> keySet()                                   | Returns set of all keys.                                                                                    |
| V merge(K key, V value, Function(< V, V, V> func)) | Sets value if key not set. Runs the function if the key is set to determine the new value. Removes if null. |
| V put(K key, V value)                              | Adds or replaces key/value pair. Returns previous value or null.                                            |
| V putIfAbsent(K key, V value)                      | Adds value if key not present and returns null. Otherwise, returns existing value.                          |
| V remove(Object key)                               | Removes and returns value mapped to key. Returns null if none.                                              |
| V replace(K key, V value)                          | Replaces the value for a given key if the key is set. Returns the original value or null if none.           |
| void replaceAll(BiFunction< K, V, V> func)         | Replaces each value with the results of the function.                                                       |
| int size()                                         | Returns the number of entries (key/value pairs) in the map.                                                 |
| Collection< V> values()                            | Returns Collection of all values.                                                                           |

```Java
        Map<String, String> map = new HashMap<>();
        map.put("koala", "bamboo");
        map.put("lion", "meat");
        map.put("giraffe", "leaf");
        String food = map.get("koala"); // bamboo
        for (String key: map.keySet())
           System.out.print(key + ","); // koala,giraffe,lion,
        
        Map<String, String> tree = new TreeMap<>();
        map.put("koala", "bamboo");
        map.put("lion", "meat");
        map.put("giraffe", "leaf");
        food = tree.get("koala"); // bamboo
        for (String key: tree.keySet())
           System.out.print(key + ","); // giraffe,koala,lion,
        
        map.add("invalid","method"); // DOES NOT COMPILE - add() not available for Map
        for (String key: map.keys()) // DOES NOT COMPILE - keys() not available for Map
         System.out.print(key + ",");
        for (String value: map.values()) // compiles 
         System.out.print(value + ",");  //
        //System.out.println(map.contains("lion")); // DOES NOT COMPILE
        System.out.println(map.containsKey("lion")); // true
        System.out.println(map.containsValue("lion")); // false
        System.out.println(map.size()); // 3
        map.clear();
        System.out.println(map.size()); // 0
        System.out.println(map.isEmpty()); // true
```

- _forEach()_ is used with a lambda with 2 parameters
- _entrySet()_ is of type _java.util.Map.Entry< K, V>_ and can be used to interate pairs of key/value
- _getOrDefault()_ is used to retrive data from collection but if not found, another parameter default value will be returned
- _replace(),replaceAll()_ are used mandatory with pair key/value paramters or in lambda
- _putIfAbsent()_ will add a new key value to collection, if key already exists, will be replaced only if == _null_
- _merge()_
  - V merge​(K key, V value, BiFunction<? super V,​? super V,​? extends V> remappingFunction) // method signature
  - used to combine elements based on rules of BiFunction
| If the requested key ________ | And mapping function returns ________ | Then:                                                                          |
| ----------------------------- | ------------------------------------- | ------------------------------------------------------------------------------ |
| Has a null value in map       | N/A (mapping function not called)     | Update key's value in map with value parameter.                                |
| Has a non‐null value in map   | null                                  | Remove key from map.                                                           |
| Has a non‐null value in map   | A non‐null value                      | Set key to mapping function result.                                            |
| Is not in map                 | N/A (mapping function not called)     | Add key with value parameter to map directly without calling mapping function. |

```Java
   Map<Integer, Character> map = new HashMap<>();
    map.put(1, 'a');
    map.put(2, 'b');
    map.put(3, 'c');
    map.forEach((k, v) -> System.out.println(v));
    map.values().forEach(System.out::println);
    map.entrySet().forEach(e -> System.out.println(e.getKey() + e.getValue()));
    
    System.out.println("26th letter of alphabet is " + map.get(26)); //null
    System.out.println("26th letter of alphabet is " + map.getOrDefault(26, 'z')); // 'z' 
    
    map.replace(2, 'd'); // [a,d,c]
    map.replaceAll((k, v) -> 'z'); //[z,z,z]
    map.putIfAbsent(4, null); //[z,z,z,null]
    map.putIfAbsent(4, 'a'); //[z,z,z,a]
    map.putIfAbsent(4, 'b'); //[z,z,z,a]
    map.forEach((k, v) -> System.out.println(v));
    
    BiFunction<Character, Character, Character> mapper = (v1, v2)-> {return (char) (v2 + 1); };
    map.put(5, null);
    map.merge(5, 'b', mapper);//[z,z,z,a,b] because value on 5 was null
    map.merge(5, 'c', mapper); // [z,z,z,a,d] because value of mapper returns 'c' + 1 => 'd'
```

#### Comparing Collection Types

| Type  | Can contain duplicate elements? | Elements always ordered?         | Has keys and values? | Must add/remove in specific order? |
| ----- | ------------------------------- | -------------------------------- | -------------------- | ---------------------------------- |
| List  | Yes                             | Yes (by index)                   | No                   | No                                 |
| Map   | Yes (for values)                | No                               | Yes                  | No                                 |
| Queue | Yes                             | Yes (retrieved in defined order) | No                   | Yes                                |
| Set   | No                              | No                               | No                   | No                                 |

| Type       | Java Collections Framework interface | Sorted? | Calls hashCode? | Calls compareTo? |
| ---------- | ------------------------------------ | ------- | --------------- | ---------------- |
| ArrayList  | List                                 | No      | No              | No               |
| HashMap    | Map                                  | No      | Yes             | No               |
| HashSet    | Set                                  | No      | Yes             | No               |
| LinkedList | List, Queue                          | No      | No              | No               |
| TreeMap    | Map                                  | Yes     | No              | Yes              |
| TreeSet    | Set                                  | Yes     | No              | Yes              |

### Sorting Data

- _Collections.sort()_ returns _void_ and will sort the parameter used in method

#### Comparable Interface

- _Comparable_ is an interface and any class could implement it
- contains method : int compareTo(T o);
- if 0 is returned in the implementation of _compareTo()_ then it means objects are equal
- if negative number is returned in the implementation of _compareTo()_ then current object is **smaller** than the argument
- if positive number is returned in the implementation of _compareTo()_ then current object is **smaller** than the argument
- for legacy code with no generics, a cast is needed for comp
- it's recommanded to check for _null_ values when implement _compareTo()_
- it's recommanded to keep _equals()_ consistent with _compareTo()_ meaning if x.equals(y) == true <=> x.compareTo(y) == 0

```Java
package com.silviu;

public class Animal implements Comparable<Animal> {
 private int id;
 private String name;

 public Animal(int id,String name) {
  this.id = id;
  this.name = name;
 }

    @Override
 public String toString() {
  return "Animal [id=" + id + ", name=" + name + "]";
 }

 public int compareTo(Animal a) {
  if (a == null)
   throw new IllegalArgumentException("Not a valid comparation!"); // recommanded null checkup
  return id - a.id; // sorts ascending by id
 }

 public int hashCode() {
  return id;
 }

 public boolean equals(Object obj) {
  if (!(obj instanceof Animal))
   return false;
  var other = (Animal) obj;
  return this.id == other.id;
 }
//    public int compareTo(Object obj) { // only in case of legacy code : public class Animal implements Comparable { ... }
//     Animal d = (Animal) obj;     // cast because no generics
//      return id - a.id;
//   }

 public static void main(String[] args) {
  var a1 = new Animal(5,"sheep");
  var a2 = new Animal(7,"horse");
  System.out.println(a1.compareTo(a2)); // -2
  System.out.println(a1.compareTo(a1)); // 0
  System.out.println(a2.compareTo(a1)); // 2
 }
}
```

#### Comparing Data with a Comparator

- Comparator interface is imported from package _java.util.Comparator;_
- this interface is used to sort objects in different ways at different times
- a lambda implementation of it can be used as argument for _Collection.sort()_

```Java
// adding to class above in main() function : 
 Comparator<Animal> byName = new Comparator<Animal>() {
   @Override
   public int compare(Animal o1, Animal o2) {
    return o1.name.compareTo(o2.name);
   }
  }; // don't forget about ';'
  
  var animals = new ArrayList<Animal>(Arrays.asList(a1,a2)) ;
  Collections.sort(animals);   // normal way using Comparable and compareTo
  System.out.println(animals); // [Animal [id=5, name=sheep], Animal [id=7, name=horse]]
  Collections.sort(animals, byName); // using Comparator, compare by name 
  System.out.println(animals); // [Animal [id=7, name=horse], Animal [id=5, name=sheep]]
```

#### Difference between Comparable and Comparator

| Difference                                        | Comparable  | Comparator |
| ------------------------------------------------- | ----------- | ---------- |
| Package name                                      | java.lang   | java.util  |
| Interface must be implemented by class comparing? | Yes         | No         |
| Method name in interface                          | compareTo() | compare()  |
| Number of parameters                              | 1           | 2          |
| Common to declare using a lambda                  | No          | Yes        |

```Java
// trick question : 
Comparator<Animal> byName = new Comparator<Animal>() {
   @Override
   public int compareTo(Animal o1, Animal o2) { // DOSE NOT COMPILE - a Comparator must implement compare() not compareTo()
    return o1.name.compareTo(o2.name);
   }
  }; // don't forget about ';'
```

#### Comparing Multiple Fields

- helper static methods for Comparator

| Method                    | Description                                                                                          |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| comparing(function)       | Compare by the results of a function that returns any Object (or object autoboxed into an Object).   |
| comparingDouble(function) | Compare by the results of a function that returns a double.                                          |
| comparingInt(function)    | Compare by the results of a function that returns an int.                                            |
| comparingLong(function)   | Compare by the results of a function that returns a long.                                            |
| naturalOrder()            | Sort using the order specified by the Comparable implementation on the object itself.                |
| reverseOrder()            | Sort using the reverse of the order specified by the Comparable implementation on the object itself. |

- helper default methods for Comparator

| Method                        | Description                                                                                                                                |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| reversed()                    | Reverse the order of the chained Comparator.                                                                                               |
| thenComparing(function)       | If the previous Comparator returns 0, use this comparator that returns an Object or can be autoboxed into one.                             |
| thenComparingDouble(function) | If the previous Comparator returns 0, use this comparator that returns a double. Otherwise, return the value from the previous Comparator. |
| thenComparingInt(function)    | If the previous Comparator returns 0, use this comparator that returns an int. Otherwise, return the value from the previous Comparator.   |
| thenComparingLong(function)   | If the previous Comparator returns 0, use this comparator that returns a long. Otherwise, return the value from the previous Comparator.   |

```Java
public class Squirrel {
   private int weight;
   private String species;
   // Assume getters/setters/constructors provided
   
   //
 Comparator<Squirrel> c = Comparator.comparing(Squirrel::getSpecies)
  .thenComparingInt(Squirrel::getWeight);
    var d = Comparator.comparing(Squirrel::getSpecies).reversed();
}
```

#### Sorting and Searching

- _Collections.sort()_ uses _compareTo()_ method to sort the collection and will not compile if objects in collection don't implement this method
- adding a _Comparator_ to _sort()_ parameter solve issue above

```Java
public class SortRabbits {
 static class Rabbit {
  int id;
 }

 public static void main(String[] args) {
  List<Rabbit> rabbits = new ArrayList<>();
  rabbits.add(new Rabbit());
  Collections.sort(rabbits); // DOES NOT COMPILE

  Comparator<Rabbit> c = (r1, r2) -> r1.id - r2.id;
  Collections.sort(rabbits, c); // compiles

  Set<Rabbit> rabbitsTree1 = new TreeSet<>((r1, r2) -> r1.id-r2.id);
  rabbitsTree1.add(new Rabbit()); // compiles run without issue

  Set<Rabbit> rabbitsTree2 = new TreeSet<>();
  rabbitsTree2.add(new Rabbit());  // ClassCastException - Rabbit class dose not implement Comparable
 }
}
```

- binarySearch() can be used with comparator parameter to sort collection befor the search

```Java
 List<Integer> list = Arrays.asList(6,9,1,8);
  Collections.sort(list); // [1, 6, 8, 9]
  System.out.println(Collections.binarySearch(list, 6)); // 1
  System.out.println(Collections.binarySearch(list, 3)); // -2

  var names = Arrays.asList("Fluffy", "Hoppy");
  Comparator<String> c = Comparator.reverseOrder(); // sorted will be in reverse natural order
  var index = Collections.binarySearch(names, "Hoppy", c);
  System.out.println(index); //-1 undefined because not sorted well 
```

### Working with Generics

- Generics are used to enforce data types in Lists, Classes, etc.

- *ClassCastException* could be thrown in case generics are not used and different types of objects are put in a List for example

#### Generic Classes

- Generics can be used in custom classes

- Syntax for a generic declaration in a class is using the < > diamond , and betheen < and > it's the generic type

- Generic types are usually upper single letter ( E, K , V , N , T , S , U , V )

- Type Erasure is the process where compiler changes all reference to generics to *Object*

```Java
public class Crate<T> {
    private T contents;
    public T emptyCrate() {
       return contents;
    }
    public void packCrate(T contents) {
       this.contents = contents;
    }
 }

class Horse {}
class Car {}
class Truck extends Car {}

class Packing {
 
 public static void main(String[] args) {
  Crate<Horse> horseCrate = new Crate<Horse>();
  horseCrate.packCrate(new Horse());
  Crate<Car> carCrate = new Crate<>(); // type Car assured on left, can be omitted on right 
  carCrate.packCrate(new Car());
  carCrate.packCrate(new Horse()); // DOSE NOT COMPILE - generics are checked by compiler, Horse incompatible with Car
  carCrate.packCrate(new Truck()); // compiles - Truck extends Car, acceptable 
  
  Horse myPackagedHorse1 = horseCrate.emptyCrate();
  // following lines equivalent for type erasure done by compiler 
  CrateTypeErasure horseCrateNoGenerics = new CrateTypeErasure();
  horseCrateNoGenerics.packCrate(new Horse());
  // equivalent of what compiler dose for variable myPackagedHorse1
  Horse myPackagedHorse2 = (Horse) horseCrateNoGenerics.emptyCrate();
 }
 
}

class CrateTypeErasure { // equivalent class of Crate after compiling 
       private Object contents;
       public Object emptyCrate() {
          return contents;
       }
       public void packCrate(Object contents) {
          this.contents = contents;
       }
    }
```

#### Generic Interfaces

- similar to classes, generics can be applied on interfaces

- classes that imenplent the interface must specify the generic type used or might pass generics of it into interface

```Java
package com.silviu;

public interface Shippable<T> {
 void ship(T t);
}

class Robot {}

class ShippableRobotCrate implements Shippable<Robot> {
 public void ship(Robot t) {
 }
}
// re-use generics into a class
class ShippableAbstractCrate<U> implements Shippable<U> {
 public void ship(U t) {
 }
}
// T generic here is not the same as T from Shippable - tricky exam questions
class ShippableAbstractCrateConfusing<T> implements Shippable<T> {
 public void ship(T t) {
 }
}

// an example with 2 generics and U is passed on to Shippable
class ShippableAbstractCrateConfusing2<T,U> implements Shippable<U> {
 public void ship(U t) {
  new U(); // DOSE NOT COMPILE - you can't instanceate a generic
 }
}
```

#### Raw Types

- when you don't want to use generics is called *raw type*

- you are not allowed to create an instance of generic ( *new U();*)

- you are not allowed to create an array of the generic type

```Java
package com.silviu;

import java.util.ArrayList;

public interface Shippable<T> {
 void ship(T t);
}

class Robot {
}

class ShippableRobotCrate implements Shippable<Robot> {
 public void ship(Robot t) {
 }
}
// re-use generics into a class
class ShippableAbstractCrate<U> implements Shippable<U> {
 public void ship(U t) {
 }
}
// T generic here is not the same as T from Shippable - tricky exam questions
class ShippableAbstractCrateConfusing<T> implements Shippable<T> {
 public void ship(T t) {
 }
}

// an example with 2 generics and U is passed on to Shippable
class ShippableAbstractCrateConfusing2<T,U> implements Shippable<U> {
 static T variable; // DOSE NOT COMPILE - can't have static variables of generic 
 public void ship(U t) {
  new U(); // DOSE NOT COMPILE - can't create instance of generic 
 }
}
```

#### Generic Methods

- beside class and interface, a generic can be declared at the method level as well

- the generic type is declared befor the return type 

- same letter can be used as method generic and class generic and could be confusing 

#### Bounding Generic Types

##### Unbounded Wildcards

##### Upper‐Bounded Wildcards

##### Lower‐Bounded Wildcards

#### Resumat

##### Combining Generic Declarations

##### Passing Generic Arguments
