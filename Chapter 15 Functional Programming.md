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
  - printing a stream will result into something like _java.util.stream.ReferencePipeline$3@4517d9a3_
  
  |Scenario| Intermediate operation|Terminal operation  |
  |----|----|----|
  |Required part of a useful pipeline?|No|Yes |
  |Can exist multiple times in a pipeline?|Yes|No |
  |Return type is a stream type?|Yes|No |
  |Executed upon method call?|No|Yes |
  |Stream valid after call?|Yes|No|
  
  |Method|Finite or infinite?|Notes  |
  |----|-----|-----|
  |Stream.empty() |Finite|Creates Stream with zero elements |
  |Stream.of(varargs) |Finite|Creates Stream with elements listed |
  |coll.stream() |Finite|Creates Stream from a Collection |
  |coll.parallelStream() |Finite|Creates Stream from a Collection where the stream can run in parallel |
  |Stream.generate(supplier) |Infinite|Creates Stream by calling the Supplier for each element upon request |
  |Stream.iterate(seed, unaryOperator)|Infinite|Creates Stream by using the seed for the first element and then calling the UnaryOperator for each subsequent element upon request |
  |Stream.iterate(seed,  predicate, unaryOperator) |Finite or infinite|Creates Stream by using the seed |
  
  ### Common terminal operations
  - _reductions_ are a special type of terminal operation because they combine all content of a stream into a single primitive or _Object_
  
  | Method | What happens for infinite streams | Return value |Reduction  |
  |----|---|---|---|
  |count() |Does not terminate|long Yes |
  |min() | Does not terminate|Optional<T> |Yes |
  |max() | | | |
  |findAny() | Terminates | Optional<T> | No |
  |findFirst() | | | |
  |allMatch() |Sometimes terminates | boolean | No |
  |anyMatch() | | | |
  |noneMatch()  | | | |
  |forEach() | Does not terminate |void | No |
  |reduce() | Does not terminate | Varies | Yes  |
  |collect() | Does not terminate |Varies |Yes |
  
  #### count()
    - returns a _long_ value representing the number of elements in a finite stream
    - hangs in case of infinite stream
  #### min() and max()
    - allows to pass a custom _Comparator_ as parameter that will sort the stream using it and find the smalles or largest value in a finite stream
    - hangs in case of infinite stream 
  
  ```
  Optional<T> min(Comparator<? super T> comparator)
  Optional<T> max(Comparator<? super T> comparator)
  ```
  
```
Stream<String> s = Stream.of("monkey", "ape", "bonobo");
Optional<String> min = s.min((s1, s2) -> s1.length()-s2.length());
min.ifPresent(System.out::println); // ape

Optional<?> maxEmpty = Stream.empty().max((s1, s2) -> 0);
System.out.println(maxEmpty.isPresent()); // false
```
  
  #### findAny() and findFirst()
    - if stream is empty they will return an empty _Optional_
    - it can terminate an infinite stream 
    - _findAny_ should return first element in stream but is not guaranteed especially when used in _parallel_ streams
   
  ```
  Optional<T> findAny()
  Optional<T> findFirst()
  ```
  
  ```
  Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
  Stream<String> infinite = Stream.generate(() -> "chimp");

  s.findAny().ifPresent(System.out::println);        // monkey (usually)
  infinite.findAny().ifPresent(System.out::println); // chimp
  ```
  #### allMatch(), anyMatch(), and noneMatch()
    - all methods use the predicated in parameter to search in stream elements
    - all return a _boolean_ value if not hanging infintly 
    - might terminate on infinite streams in certain conditions 
  
  ```
  var list = List.of("Aaa", "232", "bbB");
  Predicate<String> pred = x -> Character.isLetter(x.charAt(0));

  System.out.println(list.stream().anyMatch(pred));  // true
  System.out.println(list.stream().allMatch(pred));  // false
  System.out.println(list.stream().noneMatch(pred)); // false
  ```

  |code example | method  | terminates infinite |
  |----| ---- | ---- | 
  | Stream<String> infinite = Stream.generate(() -> "Aaa");       | anyMatch | yes |
  | Predicate<String> pred = x -> Character.isLetter(x.charAt(0));| | |
  | System.out.println(infinite.anyMatch(pred));       // true    | | |
  | Stream<String> infinite = Stream.generate(() -> "123Aaa");       | anyMatch| no |
  | Predicate<String> pred = x -> Character.isLetter(x.charAt(0));| | |
  | System.out.println(infinite.anyMatch(pred));       // hangs   | | |
  
  #### forEach()
    - iterate over elements of a stream
    - will not terminate in case of infinite stream
    - is the only terminal operation with void
  
  ```
  void forEach(Consumer<? super T> action)
---
  Stream<String> s = Stream.of("Monkey", "Gorilla", "Bonobo");
  s.forEach(System.out::print); // MonkeyGorillaBonobo
  ```
  
  #### reduce()
  - combines a stream into a single object
  - it processes all elements and it's a reduction
  - if the stream is empty an empty _Optional_ is returned
  - if the stream has 1 element , that is returned
  - _combiner_ is it used to mix different java types
```
  T reduce(T identity, BinaryOperator<T> accumulator)

  Optional<T> reduce(BinaryOperator<T> accumulator)

  <U> U reduce(U identity, 
     BiFunction<U,? super T,U> accumulator, 
     BinaryOperator<U> combiner)
```

```
  Stream<String> stream = Stream.of("w", "o", "l", "f");
  String word = stream.reduce("", (s, c) -> s + c);     // using lambda expresion 
  System.out.println(word); // wolf

  Stream<String> stream = Stream.of("w", "o", "l", "f");
  String word = stream.reduce("", String::concat);    // using method reference 
  System.out.println(word); // wolf

  Stream<String> stream = Stream.of("w", "o", "l", "f!");
  int length = stream.reduce(0, (i, s) -> i+s.length(), (a, b) -> a+b); // i is type Integer ; s is type String ; a,b are Integer
  System.out.println(length); // 5
```

  #### collect()
  - is a special type of _mutable reduction_
  - is more efficent than reduction
  - common mutable object _StringBuilder_ and _ArrayList_
  - _Collectors_ contains numerouse methods be used in _collect_ method as argument transforming to different collections 

```
  <R> R collect(Supplier<R> supplier, 
     BiConsumer<R, ? super T> accumulator, 
     BiConsumer<R, R> combiner)

  <R,A> R collect(Collector<? super T, A,R> collector)
```

```
  Stream<String> stream = Stream.of("w", "o", "l", "f");

  TreeSet<String> set = stream.collect(
     TreeSet::new, 
     TreeSet::add,
     TreeSet::addAll);

  System.out.println(set); // [f, l, o, w]
  // equivalent of : 
  Stream<String> stream = Stream.of("w", "o", "l", "f");
  TreeSet<String> set = stream.collect(Collectors.toCollection(TreeSet::new)); //Set<String> set = stream.collect(Collectors.toSet());
  System.out.println(set); // [f, l, o, w]
```
  ### Common intermediate operations
  - an intermediate operation produces a stream as it's result
  - can deal with infinite stream and produces another infinite stream

  #### filter()
  - returns a stream with elements that match a given expresion 

```
Stream<T> filter(Predicate<? super T> predicate) // signature

Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
s.filter(x -> x.startsWith("m")).forEach(System.out::print); // monkey
```
  #### distinct()
  - returns a stream with duplicate values removed
  - java will call _equals()_ in order to check if 2 objects are the same

```
Stream<T> distinct() // signature

Stream<String> s = Stream.of("duck", "duck", "duck", "goose");
s.distinct().forEach(System.out::print); // duckgoose
```
  #### limit() and skip()
  - could transform an infinite stream to a finite one

```
Stream<T> limit(long maxSize) // signature
Stream<T> skip(long n)        // signature

Stream<Integer> s = Stream.iterate(1, n -> n + 1); // infinite stream
s.skip(5)     // still an infinite stream returned here
   .limit(2)   // transform to finite stream
   .forEach(System.out::print); // 67
```

  #### map()
  - creates a one-to-one mapping for the elements in the stream
  - it is used for transforming data
  - is different of interface _Map_ that has keys values pairs

```
  <R> Stream<R> map(Function<? super T, ? extends R> mapper) // signature

  Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
  s.map(String::length)   // or x ‐> x.length()
     .forEach(System.out::print); // 676
```
  
  #### flatMap()
  - returns a _Stream_ of the type of the mapper by eliminate / combine elements of same type
  
```
  <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)

  List<String> zero = List.of();
  var one = List.of("Bonobo");
  var two = List.of("Mama Gorilla", "Baby Gorilla");
  Stream<List<String>> animals = Stream.of(zero, one, two);

  animals.flatMap(m -> m.stream()).forEach(System.out::println); //Bonobo Mama Gorilla Baby Gorilla
```

  #### sorted()
  - returns a stream sorted using natural ordering
  - accepts a _Comparator_ as parameter for custom sorting 

```
  Stream<T> sorted()                                  //signature
  Stream<T> sorted(Comparator<? super T> comparator)  //signature

  Stream<String> s = Stream.of("brown-", "bear-");
  s.sorted().forEach(System.out::print); // bear-brown-

  Stream<String> s = Stream.of("brown bear-", "grizzly-");
  s.sorted(Comparator.reverseOrder())
     .forEach(System.out::print); // grizzly-brown bear-
```

  #### peek()
  - usefull for debugging 
  - operates on the stream without actaully changeing the stream or developer should not change stream 

```
  Stream<T> peek(Consumer<? super T> action) // signature
  
  var stream = Stream.of("black bear", "brown bear", "grizzly");
  long count = stream.filter(s -> s.startsWith("g"))
     .peek(System.out::println).count();              // grizzly
  System.out.println(count);                          // 1
```

  ### Primitive Streams
  - **IntStream** for primitives type _int,short,byte_ and _char_
  - **LongStream** for _long_
  - **DoubleStream** for _double_ and _float_
  - all have methdos like **.emtpy(), of(...)**
  - _flatMap()_ exists for primitives also as _flatMapToInt(...),flatMapToDouble(...),flatMapToLong(...)_
  - _boxed()_ method will return the Stream<Type> of the primitive stream applied to 
  - _mapToObj_ will return same as boxed()
  - summary statistics are used to get multiple data from a stream regarding _min(),max(),average()_ and there are statistics of specific primitive as per table below

```
private static Stream<Integer> mapping(IntStream stream) {
   return stream.mapToObj(x -> x);
}
 
private static Stream<Integer> boxing(IntStream stream) {
  return stream.boxed();
}
```

  #### Common primitive stream methods

|Method|Primitive stream|Description  |
|---|----|---|
|OptionalDouble average() |IntStream |The arithmetic mean of the elements |
| | LongStream |
| | DoubleStream |
|Stream<T> boxed() | IntStream  | A Stream<T> where T is the wrapper class associated with the primitive value |
| | LongStream |
| | DoubleStream |
| OptionalInt max() | IntStream | The maximum element of the stream |
| OptionalLong max() | LongStream  |  | 
| OptionalDouble max() |DoubleStream |  |
| OptionalInt min() | IntStream | The minimum element of the stream  |
| OptionalLong min() | LongStream  | |
| OptionalDouble min() | DoubleStream  | |
| IntStream range(int a, int b) | IntStream | Returns a primitive stream from a (inclusive) to b (exclusive)  |
| LongStream range(long a, long b) | LongStream  | | 
| IntStream rangeClosed(int a, int b) | IntStream | Returns a primitive stream from a (inclusive) to b (inclusive)  |
| LongStream rangeClosed(long a, long b) | LongStream  | | 
| int sum() | IntStream | Returns the sum of the elements in the stream  | 
| long sum() | LongStream  | |
| double sum() | DoubleStream  | |
|IntSummaryStatistics summaryStatistics() | IntStream | Returns an object containing numerous stream statistics such as the average, min, max, etc.  |
|LongSummaryStatistics summaryStatistics() | LongStream  | |
|DoubleSummaryStatistics summaryStatistics() | DoubleStream | |

  #### Mapping methods between types of streams
  
|Source stream class|To create Stream|To create DoubleStream|To create IntStream|To create LongStream  |
|----|---|---|---|---|
|Stream<T> |map() |mapToDouble() |mapToInt() |mapToLong()  |
|DoubleStream |mapToObj() |map() |mapToInt() |mapToLong()  |
|IntStream |mapToObj() |mapToDouble() |map() |mapToLong()  |
|LongStream |mapToObj() |mapToDouble() |mapToInt() |map() |

#### Function parameters when mapping between types of streams
   
|Source stream class|To create Stream|To create DoubleStream|To create IntStream|To create LongStream  |
|----|---|---|---|---|
|Stream<T> |Function<T,R> |ToDoubleFunction<T> |ToIntFunction<T> |ToLongFunction<T>  |
|DoubleStream |Double Function<R> |DoubleUnary Operator |DoubleToInt Function |DoubleToLong Function  |
|IntStream |IntFunction<R> |IntToDouble Function |IntUnary Operator |IntToLong Function  |
|LongStream |Long Function<R> |LongToDouble Function |LongToInt Function |LongUnary Operator |

#### Common functional interfaces for primitives

|Functional interfaces|# parameters|Return type|Single abstract method  |
|---|---|---|---|
|DoubleSupplier |0|double|getAsDouble|
|IntSupplier ||int|getAsInt| | 
|LongSupplier ||long|getAsLong| | 
|DoubleConsumer |1 (double) |void | accept| 
| IntConsumer | 1 (int) | | | 
| LongConsumer |  1 (long) | | |
|DoublePredicate | 1 (double) | boolean | test|
|IntPredicate |  1 (int) | | |
|LongPredicate | 1 (long) | | |
|DoubleFunction<R> |1 (double) | R | apply|
| IntFunction<R> |  1 (int) | | |
|LongFunction<R> |  1 (long) | | |
|DoubleUnaryOperator | 1 (double)| double|applyAsDouble|
|IntUnaryOperator | 1 (int)|int|applyAsInt |
|LongUnaryOperator | 1 (long)|long|  applyAsLong|
|DoubleBinaryOperator |2 (double, double)|double|applyAsDouble|
|IntBinaryOperator | 2 (int, int)|int|applyAsInt|
|LongBinaryOperator | 2 (long, long)|long|applyAsLong|

#### Primitive‐specific functional interfaces

|Functional interfaces|# parameters|Return type|Single abstract method  |
|---|----|---|---|
|ToDoubleFunction<T> |1 (T)|double|applyAsDouble|
|ToIntFunction<T> ||int|applyAsInt|
|ToLongFunction<T> ||long|applyAsLong|
|ToDoubleBiFunction<T, U> |2 (T, U)|double|applyAsDouble|
|ToIntBiFunction<T, U> ||int|applyAsInt|
|ToLongBiFunction<T, U> ||long|applyAsLong|
|DoubleToIntFunction |1 (double)|int|applyAsInt|
|DoubleToLongFunction |1 (double)|long|applyAsLong|
|IntToDoubleFunction |1 (int)|double|applyAsDouble|
|IntToLongFunction |1 (int)|long|applyAsLong|
|LongToDoubleFunction| 1 (long)|double|applyAsDouble|
|LongToIntFunction |1 (long) |int|applyAsInt|
|ObjDoubleConsumer<T> |2 (T, double)|void |accept|
|ObjIntConsumer<T> | 2 (T, int)|||
|ObjLongConsumer<T> |2 (T, long)|| |

### Advanced Stream Pipeline Concepts

#### Checked exception
  - if a method declares a checked exception it cann't be used as method reference in _Supplier_ for example
  - 

#### Examples of grouping/partitioning collectors

|Collector|Description|Return value when passed to collect  |
|----|----|----|
|averagingDouble(ToDoubleFunction f) |Calculates the average for our three core primitive types | Double
|averagingInt(ToIntFunction f) | ||
|averagingLong(ToLongFunction f) |||
|counting() |Counts the number of elements|Long  |
|groupingBy(Function f) |Creates a map grouping by the specified function with the optional map type supplier and optional downstream collector|Map<K, List<T>>  |
|groupingBy(Function f, Collector dc) |||
|groupingBy(Function f, Supplier s, Collector dc) |||
|joining(CharSequence cs) |Creates a single String using cs as a delimiter between elements if one is specified|String  |
|maxBy(Comparator c) |Finds the largest/smallest elements|Optional<T> |
|minBy(Comparator c) |||
|mapping(Function f, Collector dc) |Adds another level of collectors|Collector  |
|partitioningBy(Predicate p) |Creates a map grouping by the specified predicate with the optional further downstream collector|Map<Boolean, List<T>>  |
|partitioningBy(Predicate p, Collector dc) |||
|summarizingDouble(ToDoubleFunction f) |Calculates average, min, max, and so on|DoubleSummaryStatistics
|summarizingInt(ToIntFunction f) ||IntSummaryStatistics|
|summarizingLong(ToLongFunction f)| |LongSummaryStatistics|
|summingDouble(ToDoubleFunction f) | Calculates the sum for our three core primitive types|Double|
|summingInt(ToIntFunction f) | |Integer|
|summingLong(ToLongFunction f) || Long  |
|toList() |Creates an arbitrary type of list or set|List|
|toSet() || Set|
|toCollection(Supplier s) |Creates a Collection of the specified type|Collection  |
|toMap(Function k, Function v) |Creates a map using functions to map the keys, values, an optional merge function, and an optional map type supplier|Map|
|toMap(Function k, Function v, BinaryOperator m) |||
|toMap(Function k, Function v, BinaryOperator m, Supplier s) |||

  #### Collecting Using Grouping, Partitioning, and Mapping
  - the type used in the method **groupingBy** will give the key part of the resulting _Map_
  - values of the resulting map will remain of same type as per initial collection
  - _groupingby_ cannot return _null_ because you can't have _null_ keys in _Map_
```
var ohMy = Stream.of("lions", "tigers", "bears");
Map<Integer, List<String>> map = ohMy.collect(
   Collectors.groupingBy(String::length)); // because length is an int / Integer is the key in the Map above 
System.out.println(map);    // {5=[lions, bears], 6=[tigers]}
```

```
var ohMy = Stream.of("lions", "tigers", "bears");
TreeMap<Integer, Set<String>> map = ohMy.collect(
   Collectors.groupingBy(
      String::length,  // this determins the key as Integer
      TreeMap::new,    // this creates the TreeMap
      Collectors.toSet())); // this will convert values to a Set (HashSet)
//  Collectors.toList())); line above could be replaced with this one but also 2nd line with List<String> insteed of Set<String>
System.out.println(map); // {5=[lions, bears], 6=[tigers]}
```
  - partitioning meaning separate values in 2 possible groupes _true_ / _false_
  - resulting _Map_ has always the key as _Boolean_
```
var ohMy = Stream.of("lions", "tigers", "bears");
Map<Boolean, List<String>> map = ohMy.collect(
   Collectors.partitioningBy(s -> s.length() <= 5));
System.out.println(map);    // {false=[tigers], true=[lions, bears]}
///////
var ohMy = Stream.of("lions", "tigers", "bears");
Map<Boolean, List<String>> map = ohMy.collect(
   Collectors.partitioningBy(s -> s.length() <= 7));
System.out.println(map);    // {false=[], true=[lions, tigers, bears
```

  ## Exam tricks questions
  ### Incomplete functions or missing parameters
  
  ```
  Function<List<String>> ex1 = x -> x.get(0); // DOES NOT COMPILE - missing return type as function needs 2 parameters declared in left between <>
  UnaryOperator<Long> ex2 = (Long l) -> 3.14; // DOES NOT COMIPLE - wrong return type, expected Long but returned 3.14 - double 
  Predicate ex4 = String::isEmpty;            // DOES NOT COMPILE - missing parameter type from Predicated, expected Predicate<String>
  ```
  
  ### Wrong syntax used
  ```
  Stream<Integer> oddNumberUnder100 = Stream.iterate(1;n -> n<100; n-> n + 2); // use of semicolons like in 'for' insteed of commas
  ```
 
  ### Use a stream that is already terminated
  ```
  ```

  ### Iterate over stream with for-each 
```
  Stream<Integer> s = Stream.of(1);
  for (Integer i  : s) {} // DOES NOT COMPILE because Stream dosn't implements Iterable interface
```

  ### Using wrong method reference
```
  Stream<String> s = Stream.of("brown bear-", "grizzly-");
  s.sorted(Comparator::reverseOrder); // DOES NOT COMPILE
  // because referseOrder it's a function with 0 parameters that retuns a Comparator

```
  ### Using wrong collect toMap method
  - it throws following exception: _Exception in thread "main" java.lang.IllegalStateException: Duplicate key 5_ 
  - the problem is that they key for the map is the length of each string and is not unique 

```
var ohMy = Stream.of("lions", "tigers", "bears");
Map<Integer, String> map = ohMy.collect(Collectors.toMap(
   String::length, 
   k -> k)); // compiles but throws exception
// solution :
var ohMy = Stream.of("lions", "tigers", "bears");
Map<Integer, String> map = ohMy.collect(Collectors.toMap(
   String::length,
   k -> k, 
  (s1, s2) -> s1 + "," + s2)); // will concatenate in case of same key
System.out.println(map);            // {5=lions,bears, 6=tigers}
System.out.println(map.getClass()); // class java.util.HashMap

```
