## Generics and Collections
### Using method references
- method reference and lambda behave the same way
- can be applied to static methods, instance methods on a particular instance, constructors, instance methods on a parameter determined at runtime
- you should know each method signature if takes 1,2 etc parameter and what returns
#### Static methods
  - Java extracts the right method usage based on context
```
Consumer<List<Integer>> methodRef = Collections::sort;    // sort is overloaded but Java knows it's a Consumer and calls sort with 1 parameter
Consumer<List<Integer>> lambda = x -> Collections.sort(x); 
```
#### Instance Methods on a Particular Object
```
var str = "abc";
Predicate<String> methodRef = str::startsWith;    // takes a parameter and returns boolean , Predicate is
Predicate<String> lambda = s -> str.startsWith(s);

var random = new Random();
Supplier<Integer> methodRef = random::nextInt;    //no need to take parameter so that's way Supplier is used 
Supplier<Integer> lambda = () -> random.nextInt();
```
#### Instance Methods on a Parameter

```
		Predicate<String> methodRef = String::isEmpty;
		Predicate<String> lambda = s -> s.isEmpty();
		
		BiPredicate<String, String> methodRef2 = String::startsWith;
		BiPredicate<String, String> lambda2 = (s, p) -> s.startsWith(p);
```

#### Constructors
- usually for constructors a _Supplier_ is provided
```
    Supplier<List<String>> methodRef5 = ArrayList::new;
    Supplier<List<String>> lambda5 = () -> new ArrayList();
    // all 3 are equivalent :
		Function<Integer, List<String>> methodRef6 = ArrayList::new;
		Function<Integer, List<String>> lambda6 = (Integer x) -> new ArrayList<String>(x);
		Function<Integer, List<String>> lambda6a = x -> new ArrayList(x);
```

#### Reviewing Method References

|Type|Before colon|After colon|Example  |
|----|----|----|----|
|Static methods|Class name|Method name|Collections::sort  |
|Instance methods on a particular object|Instance variable name|Method name|str::startsWith  |
|Instance methods on a parameter|Class name|Method name|String::isEmpty  |
|Constructor|Class name|new |ArrayList::new |

```
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

|Primitive type|Wrapper class|Example of initializing  |
|----|----|----|
|boolean |Boolean |Boolean.valueOf(true)  |
|byte |Byte |Byte.valueOf((byte) 1)  |
|short |Short |Short.valueOf((short) 1)  |
|int |Integer |Integer.valueOf(1)  |
|long |Long |Long.valueOf(1)  |
|float |Float |Float.valueOf((float) 1.0)  |
|double |Double |Double.valueOf(1.0)  |
|char |Character |Character.valueOf('c') |

```
List<Integer> numbers = new ArrayList<Integer>();g
numbers.add(1);						// autoboxing to Integer , 1 is added : [1] 
numbers.add(Integer.valueOf(3));	// Integer , 3 is added : [1,3]
numbers.add(Integer.valueOf(5));	// Integer , 5 is added : [1,3,5]
// remove is overloaded remove(int index) ; remove(Object o)
numbers.remove(1);					// no autoboxing executed as direct primitive prefered, so element with index 1 removed : [1,5]
numbers.remove(Integer.valueOf(5)); // remove(Object o) applied , 5 is removed 
System.out.println(numbers);		//outputs [1]
```
### Diamond Operator
- is used for shorhand notation of long declaration of generics
- could exists only in the right side of = , before = will cause a compilation error
```
    	   Map<Long,List<Integer>> mapLists = new HashMap<Long,List<Integer>>();
    	   Map<Long,List<Integer>> mapLists2 = new HashMap<>(); // usage of <> short code 
    	    
    	   List<> list = new ArrayList<Integer>();      // DOES NOT COMPILE - diamond <> cannot exist on left side
    	   
    	   // not equivalent 
    	   var list1 = new ArrayList<Integer>(); // ArrayList<Integer>
    	   var list2 = new ArrayList<>();		// ArrayList<Object>
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
```
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
- boolean remove(int index)	// method signature
- removes a single value that will match the object in parameter
- removing an element while looping cause a ConcurrentModificationException unless specific types of lists are used 
- using an _int_ as parameter will remove respective element on the index and not match an object
```
Collection<String> birds = new ArrayList<>();
birds.add("hawk");                            // [hawk]
birds.add("hawk");                            // [hawk, hawk]
System.out.println(birds.remove("cardinal")); // false
System.out.println(birds.remove("hawk"));     // true - removes only 1st match 
System.out.println(birds);                    // [hawk]
birds.remove(100); 							 // IndexOutOfBoundsException

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
```
 	Collection<String> list = new ArrayList<>();
    list.add("Magician");
    list.add("Assistant");
    list.add("");
    System.out.println(list);     // [Magician, Assistant,""]
    list.removeIf(s -> s.startsWith("A"));	// literal "A" as parameter 
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
|Method|Description|Can add elements?|Can replace element?|Can delete elements?|
|---|----|----|----|----|
|Arrays.asList(varargs) |Returns fixed size list backed by an array|No	|Yes|No|
|List.of(varargs)|Returns immutable list|No|No|No|
|List.copyOf(collection)|Returns immutable list with copy of original collection's values|No|No|No|

##### List Methods
|Method|Description|
|---|---|
|boolean add(E element) |Adds element to end (available on all Collection APIs)|
|void add(int index, E element) |Adds element at index and moves the rest toward the end|
|E get(int index) |Returns element at index|
|E remove(int index) |Removes element at index and moves the rest toward the front|
|void replaceAll(UnaryOperator<E> op) |Replaces each element in the list with the result of the operator|
|E set(int index, E e) |Replaces element at index and returns original. Throws IndexOutOfBoundsException if the index is larger than the maximum one set|

```
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
```
  Set<Character> duplicate = Set.of('a', 'a', 'b'); // IllegalArgumentException: duplicate element: a
  Set<Character> letters = Set.of('z', 'o', 'b');
  Set<Character> copy = Set.copyOf(letters);
  
  Set<StringBuilder> sbSet = Set.of(new StringBuilder("test1"), new StringBuilder("test2"));
  sbSet.forEach(System.out::println);		// "test1 test2"
  sbSet.forEach(x -> x.append(" modified")); 
  sbSet.forEach(System.out::println); 	// "test1 modified test2 modified"
  
  letters.add('n'); 	 // UnsupportedOperationException 
  copy.add('o'); 		 // UnsupportedOperationException 
  letters.remove('t'); // UnsupportedOperationException
  copy.remove('k'); 	 // UnsupportedOperationException 
  
  letters.add(null);  // NullPointerException
  copy.add(null);     // NullPointerException
```
#### Queue Interface
- _FIFO_ first-in,first-out
- _LIFO_ last-in,last-out or also known as _stack_
- _LinkedList _
  - it's a double ended queue, elements can be inserted and removed from both front/back
- _ArrayDeque_ 
   - not in scope of exame
##### Queue Methods
|Method|Description|Throws exception on failure|
|---|---|---|
|boolean add(E e) |Adds an element to the back of the queue and returns true or throws an exception|Yes|
|E element() |Returns next element or throws an exception if empty queue|Yes|
|boolean offer(E e) |Adds an element to the back of the queue and returns whether successful|No|
|E remove() |Removes and returns next element or throws an exception if empty queue|Yes|
|E poll() |Removes and returns next element or returns null if empty queue|No|
|E peek() |Returns next element or returns null if empty queue|No|
#### Map Interface
##### Map Methods
#### Comparing Collection Types
### Sorting Data
#### Comparable Class
#### Comparing Data with a Comparator
#### Difference between Comparable and Comparator
#### Comparing Multiple Fields
#### Sorting and Searching
### Working with Generics
#### Generic Classes
#### Generic Interfaces
#### Raw Types
#### Generic Methods
#### Bounding Generic Types
##### Unbounded Wildcards
##### Upper‐Bounded Wildcards
##### Lower‐Bounded Wildcards
#### Resumat 
##### Combining Generic Declarations
##### Passing Generic Arguments
