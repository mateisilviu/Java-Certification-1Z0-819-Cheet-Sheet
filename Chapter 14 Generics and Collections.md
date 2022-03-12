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
  - __
### Diamond Operator
### Lists, Sets, Maps, and Queues
#### Common Collections Methods
##### add()
##### remove()
##### isEmpty() and size()
##### clear()
##### contains()
##### removeIf()
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
