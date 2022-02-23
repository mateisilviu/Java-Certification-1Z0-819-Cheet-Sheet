# Functional Programming

## Common functional interfaces

- functioanl interfaces are in java package _java.util.function_

| Functional interface | Return type | Method name | # of parameters  |
| ----- | ---- | ---- | ---- |
|Supplier<T> | T | get() | 0 |
|Consumer<T> |void | accept(T) | 1 (T) |
|BiConsumer<T, U> | void | accept(T,U) | 2 (T, U) |
|Predicate<T> |boolean |test(T) |1 (T) |
|BiPredicate<T, U> |boolean | test(T,U) | 2 (T, U) |
|Function<T, R> | R | apply(T) | 1 (T) |
|BiFunction<T, U, R> | R | apply(T,U) |2 (T, U) |
|UnaryOperator<T> | T | apply(T) | 1 (T) |
|BinaryOperator<T> |T |apply(T,T) |2 (T, T)|
  
  ### Supplier
  - a supplier is used to generate or supply values without taking any input
  ```
  @FunctionalInterface
  public interface Supplier<T> {
     T get();
  }
  ```
  ```
  Supplier<LocalDate> s1 = LocalDate::now; // method reference
  Supplier<LocalDate> s2 = () -> LocalDate.now(); // lambda expression

  LocalDate d1 = s1.get();
  LocalDate d2 = s2.get();

  System.out.println(d1);
  System.out.println(d2);
  
  System.out.println(s1); // something like mypackage.MyClass$$Lambda$1/0x0000000800066840@4909b8da
  // MyClass is the name of the testing class that exists in mypackage
  ```
  ### Consumer and BiConsumer
  - a _Consumer_ is used to do something with a parameter and don't return anything. _BiConsumer_ dose same thing but it needs 2 parameters.
  ```
  @FunctionalInterface
  public interface Consumer<T> {
     void accept(T t);
     // omitted default method
  }

  @FunctionalInterface
  public interface BiConsumer<T, U> {
     void accept(T t, U u);
     // omitted default method
  }
  ```
  ```
  Consumer<String> c1 = System.out::println;        // method reference
  Consumer<String> c2 = x -> System.out.println(x); // lambda expression

  c1.accept("Annie");
  c2.accept("Annie");
  
  var map = new HashMap<String, Integer>();
  BiConsumer<String, Integer> b1 = map::put;                // method reference
  BiConsumer<String, Integer> b2 = (k, v) -> map.put(k, v); // lambda expression

  b1.accept("chicken", 7);
  b2.accept("chick", 1);

  ```
  
  ### Predicate and BiPredicate
  - a _Predicate_ takes 1 argument and tests a value againts an expression
  - it's used for filtering or matching
  - _BiPredicate_ takes 2 arguments with same outcome 
  
  ```
  @FunctionalInterface
  public interface Predicate<T> {
     boolean test(T t);
     // omitted default and static methods
  }

  @FunctionalInterface
  public interface BiPredicate<T, U> {
     boolean test(T t, U u);
     // omitted default methods
  }
  ```
  
  ```
  Predicate<String> p1 = String::isEmpty;  // method reference
  Predicate<String> p2 = x -> x.isEmpty(); // lambda expression

  System.out.println(p1.test(""));  // true
  System.out.println(p2.test(""));  // true

  BiPredicate<String, String> b1 = String::startsWith;
  BiPredicate<String, String> b2 = 
     (string, prefix) -> string.startsWith(prefix);

  System.out.println(b1.test("chicken", "chick"));  // true
  System.out.println(b2.test("chicken", "chick"));  // true
  ```
  
  ### Function and BiFunction
    - a _Function_ takes 1 parameter and applies a conversion (changes) to respective parameter and returns different type of parameter
    - a _BiFunction_ takes 2 parameters and returns a 3rd type of parameter
  
  ```
  @FunctionalInterface
  public interface Function<T, R> {
     R apply(T t);
     // omitted default and static methods
  }

  @FunctionalInterface
  public interface BiFunction<T, U, R> {
     R apply(T t, U u);
     // omitted default method
  }
  ```
  
  ```
  Function<String, Integer> f1 = String::length;  // method reference
  Function<String, Integer> f2 = x -> x.length(); // lambda expression

  System.out.println(f1.apply("cluck")); // 5 - turns a string into an integer
  System.out.println(f2.apply("cluck")); // 5 - turns a string into an integer
  
  BiFunction<String, String, String> b1 = String::concat;                           // method reference
  BiFunction<String, String, String> b2 = (string, toAdd) -> string.concat(toAdd);  // lambda expression
 
  System.out.println(b1.apply("baby ", "chick")); // baby chick - turns a String into another String
  System.out.println(b2.apply("baby ", "chick")); // baby chick - turns a String into another String
  ```
  
### UnaryOperator and BinaryOperator
  - _UnaryOperator_ extends _Function_ and transforms a value and returns same type of value
  - _BinaryOperator_ extends _BiFunction_ that takes 2 values of same time and returns a 3rd value of same type
  
  ```
  @FunctionalInterface
public interface UnaryOperator<T> extends Function<T, T> { 
    T apply(T t);       
  }
 
@FunctionalInterface
public interface BinaryOperator<T> extends BiFunction<T, T, T> { 
   T apply(T t1, T t2); 
}
  ```
  
  ```
  UnaryOperator<String> u1 = String::toUpperCase;  // method reference
  UnaryOperator<String> u2 = x -> x.toUpperCase(); // lambda expression

  System.out.println(u1.apply("chirp"));  // CHIRP
  System.out.println(u2.apply("chirp"));  // CHIRP
  
  BinaryOperator<String> b1 = String::concat;                           // method reference
  BinaryOperator<String> b2 = (string, toAdd) -> string.concat(toAdd);  // lambda expression

  System.out.println(b1.apply("baby ", "chick")); // baby chick
  System.out.println(b2.apply("baby ", "chick")); // baby chick
  ```
  
  ### Helpfull methods on Functional Interfaces 
  - functional interfaces presented have several _default_ methods that could be used
  - _BiConsumer_, _BiFunction_, _BiPredicate_ have similar methods
  - methods are used to combine 2 _Predicate_ for example or to combine 2 _Consmer_ / _Function_
 ```
  Predicate<String> examJava =  s -> s.contains("java");
  Predicate<String> exam819 =  s -> s.contains("819");
  Predicate<String> java11 = examJava.and(exam819);
  Predicate<String> javaOther = examJava.and(exam819.negate());
  ```
  
  |Interface instance|Method return type|Method name|Method parameters  |
  |----|----|---|---|
  |Consumer |Consumer |andThen() |Consumer  |
  |Function |Function |andThen() |Function  |
  |Function |Function |compose() |Function  |
  |Predicate |Predicate |and() |Predicate  |
  |Predicate |Predicate |negate() | N/A |
  |Predicate |Predicate |or() |Predicate |
  
  - **andThen()** runs in sequance the instructions used for 
  ```
  Consumer<String> c1 = x -> System.out.print("1: " + x);
  Consumer<String> c2 = x -> System.out.print(",2: " + x);
  Consumer<String> combined = c1.andThen(c2);
  combined.accept("Combined");              // 1: Combined,2: Combined
  ```
  - **compose()** will chain functional interfaces passing the output of one to another 
  ```
  Function<Integer, Integer> before = x -> x + 1;
  Function<Integer, Integer> after = x -> x * 2;

  Function<Integer, Integer> combined = after.compose(before);
  System.out.println(combined.apply(3));   // 8 because the 'before' is applied 1st and the result is 4 (3+1), then 4 is input in 'after' and final result 8 ( 4 * 2)
  ```
  
  ## Optional <T>
  - it's used to wrapp values that might have _null_ or _not applicable_ _empty_ values and easy deal with them
  - it is in the package _java.util_
  
  |Method|When Optional is empty|When Optional contains a value  |Example for Optional<Double> opt = average(90, 100);|
  | ----|-----| ---- |----|
  |get() |Throws an exception|Returns value | System.out.println(**opt.get()**); // 95.0 |
  |ifPresent(Consumer c) |Does nothing|Calls Consumer with value | **opt.ifPresent**(System.out::println); //95.0|
  |isPresent() |Returns false|Returns true | if (**opt.isPresent()**) { //do get or anything else } | 
  |orElse(T other) |Returns other parameter|Returns value | System.out.println(**opt.orElse**(Double.NaN)); | 
  |orElseGet(Supplier s) |Returns result of calling Supplier|Returns value |System.out.println(**opt.orElseGet**(() -> Math.random())); | 
  |orElseThrow() |Throws NoSuchElementException|Returns value | System.out.println(**opt.orElseThrow()**); //NoSuchElementException|
  |orElseThrow(Supplier s) |Throws exception created by calling Supplier|Returns value|System.out.println(**opt.orElseThrow**( () -> new IllegalStateException()));|

  
  - _Optional.empty()_ creates an empty optional
  - _Optional.of(value)_ creates a specific value that is not null ; if a null value is in intput _NullPointerException_ is thrown
  - _Optional.ofNullable(value)_ creates a specific value that could be null 
  
  ## Streams
  - a _stream_ is a sequance of data in Java
  - a _stream pipeline_ is a series of operation run on a stream that produce a result
  - a stream has **source** , **intermediate  operations** and **terminal operation**
  - **Intermediate operations** are optional and could be missing from a stream, other parts are mandatory
  - a _stream_ could have many _intermeditate operations_ and only one **terminal operation**
  - _Stream<T>_ interface is located in _java.util.stream_ package
  
  |Scenario| Intermediate operation|Terminal operation  |
  |----|----|----|
  |Required part of a useful pipeline?|No|Yes |
  |Can exist multiple times in a pipeline?|Yes|No |
  |Return type is a stream type?|Yes|No |
  |Executed upon method call?|No|Yes |
  |Stream valid after call?|Yes|No|
  
  
  
  ## Exam tricks questions
  ### Incomplete functions or missing parameters
  
  ```
  Function<List<String>> ex1 = x -> x.get(0); // DOES NOT COMPILE - missing return type as function needs 2 parameters declared in left between <>
  UnaryOperator<Long> ex2 = (Long l) -> 3.14; // DOES NOT COMIPLE - wrong return type, expected Long but returned 3.14 - double 
  Predicate ex4 = String::isEmpty;            // DOES NOT COMPILE - missing parameter type from Predicated, expected Predicate<String>
  ```
