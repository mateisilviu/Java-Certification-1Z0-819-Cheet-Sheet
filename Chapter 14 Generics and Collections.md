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
#### List Interface
##### Creating a List with a Factory
##### List Methods
#### Set Interface 
##### Set Methods
#### Queue Interface
##### Queue Methods
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
