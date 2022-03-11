## Annotations 
  - are used to assign metadata attributes to classes, methods, variables and other Java types
  - establish relationships that make it easier to manage data about our application
  - describes custom information on the declaration where it is defined
  - are optional metadata and by themselves do not do anything
  - if all annotations are removed the code will still compile and run
  - adding an annotation might trigger a compilation error (ex @Override ) 
  - only acceptable access modifier is _public,package-private,abstract_
  
### Creating an Annotation
  - _public @interface MyCustomAnnotation {}_ a simple annotation also named _marker annotation_ because body it's empty 
  - an annotation is applied to the next nonannotation type found on the proceeding lines
  - annotation names are case sensitive 
  - _annotation element_ is created like this : _java type_ + _attributName();_ - similar to an abstract method
  - any element without _default value_ is considered requred and must exists as annotation parameter 
  - the optional value is created using _java type_ + _attributeName() default < value >;_
  - _default value_ must be  a non-null constant expression
  - an annotation element can be delcared with a primitive type a _String_ , a _Class_, an enum, another annotation or an array of these types

```
package com.silviu;

enum Height {SHORT, NORMAL, TALL}

@interface AnotherInterface {
	int anotherValue();
}

public @interface MyAnnotation {
	
	   int someInformation();   // a required element  
	   int defaultInformation() default 6;    // an optional element 
	   String name() default new String("");    // DOES NOT COMPILE - is not a constant value
	   String surname() default "";   // empty string accepted 
	   String title() default null;   // DOES NOT COMPILE - default null value not accepted
	   Integer size();    // DOSE NOT COMPILE - wrapper classes for primitive not supported
	   long [][] matrix();    // DOSE NOT COMPILE - [][] not supported, only single array
	   long [] arraySupported() default {1,2,3};// compiles - array supported
	   Height hight() default Height.SHORT;		// compiles 
	   AnotherInterface complicated() default @AnotherInterface(anotherValue = 5); // compiles
	   AnotherInterface moreComplicated() default AnotherInterface(anotherValue = 5); // DOSE NOT COMPILE - missing @before Anotherinterface
	   AnotherInterface evenMoreComplicated() default @AnotherInterface; // DOSE NOT COMPILE - missing attribute 'anotherValue'
}
// annotation could be in same line, adding any spaces or charachter and still works for next class found
@MyAnnotation(someInformation = 1)        class AppliedOn {  }
@MyAnnotation(someInformation = 0, defaultInformation = 5)  // changing default value 6 with 5 

//could have multiple lines between next element, still applies on it 
class AnotherClass { }

@MyAnnotation			// DOSE NOT COMPILE - missing declaration of required element 'someInformation'
class NotValid1 { }
@MyAnnotation someInformation=3		// DOSE NOT COMPILE - missing brackets ( ) when declaration
class NotValid2 { }
@MyAnnotation(defaultInformation = 10)		// DOSE NOT COMPILE - missing declaration of required element 'someInformation'
class NotValid3 { }
```

  ### Annotation constants and access modifiers
  - annotation elements are implicitly _abstract_ and _public_
  - any of keywords used for annotation elements like _protected_, _private_, _final_ will cause compilation error
  - annotation constant are implicitly _public,static_ and _final_
  - annotation constant are not considered elements so they could exists in marker annotation
  - annotation constant variable can be accessed by other classes without creating annotation	
	
```
@interface Material {}

public @interface Fluffy {
   int cuteness();					// public abstract will be implied
   public boolean washable() default true; 		// abstract will be implied 
   abstract int temperature() default 30;		// public will be implied
   public abstract int softness() default 11;	// 
   
   // constants declaration :
   int MIN_VOLTAGE = 2;					// public,static,final
   public boolean WATER_SUPPLIE = true;			// static, final
   public static int SOFTNER  = 50;			// final
   public static final int MAX_VOLTAGE = 18;		// all in place
   static final int CAPACITY = 8;			// public
   final String DISPLAY = "Hello";			// public, static
   static String ENERGY_EFFICIENCY = "B";		// public, final 
   
   
   protected Material material();  // DOES NOT COMPILE - protected is not compatible with public
   private String friendly();      // DOES NOT COMPILE - private is not compatible with public
   final boolean isBunny();        // DOES NOT COMPILE - final is not compatible with abstract
}
```

### Apply annotations
- could be applied on classes, interfaces, enums, modules, variables, methods and constructors, lambda parameters, cast expressions, other annotations
- _@Target_ is used to specify where annotation could be applied on and if they are applied on other types, compilation error occurs
- _@Target_ is located in _java.lang.annotation.Target_ and _ElementType_ in _java.lang.annotation.ElementType_
- _java.lang_ is automaticaly imported but _java.lang.annotation_ is not, missing instruction results in compilation error in case of annotation usage
- some of the _ElementType_ overlap in function in _@Target_ usage 
- _TYPE_USE_ paramter means annotation can be used anywhere htere is a Java type. They cannot be used on methods that don't return a value - void
- following table aoubt _ElementType_ usage. * - not for exam 
- When _@Target_ is not present, an annotation can be used in any place except TYPE_USE or TYPE_PARAMETER scenarios (cast operations, object creation, generic declarations, etc.)

|ElementType value|Applies to|
|----|-----|
|TYPE|Classes, interfaces, enums, annotations|
|FIELD |	Instance and static variables, enum values|
|METHOD |	Method declarations|
|PARAMETER |	Constructor, method, and lambda parameters|
|CONSTRUCTOR| 	Constructor declarations|
|LOCAL_VARIABLE| 	Local variables|
|ANNOTATION_TYPE| 	Annotations|
|PACKAGE * |	Packages declared in package‐info.java|
|TYPE_PARAMETER * | 	Parameterized types, generic declarations|
|TYPE_USE| 	Able to be applied anywhere there is a Java type declared or used|
|MODULE * | 	Modules|

![Annotation Target Method](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/Annotation-Target-Method.PNG)

### @Retention
- annotation may be discarded by the compiler or at runtime
- _RetentionPolicy_ enum is used to change behaviour 

|RetentionPolicy value|Description|
|----|-----|
|SOURCE| 	Used only in the source file, discarded by the compiler|
|CLASS|Stored in the .class file but not available at runtime (default compiler behavior)|
|RUNTIME|	Stored in the .class file and available at runtime|

### value() Element
- using _value()_ is a shortcut and element name may be omitted under certain conditions
	1) must have an element named _value()_ (optional or required)
	2) must not contain any othr elements that are requred
	3) annotation usage must not provide values for other elements
-	supports shorthand notation for an array that contains a single element

```
public @interface Injured {
   String veterinarian() default "unassigned";
   String value() default "foot";				//declaration of value() option
   int age() default 1;
}

@interface InvalidAnnotation {			// compiles
	   String veterinarian();				
	   String value() default "foot";				//declaration of value() option
	   int age() default 1;
}

abstract class Elephant {
	   @Injured("Legs") public void fallDown() {}			// short declaration
	   @Injured(value="Legs") public abstract int trip();	// long declaration
	   @Injured String injuries[];							// no element declaration
	   
	   @InvalidAnnotation("Ears") public void ears() {}		// DOSE NOT COMPILE - missing attribute veterinarian
	   @InvalidAnnotation("Nose", veterinarian="John") public void nose() {}		// DOSE NOT COMPILE - invalid member pairs (short not available)
	   @InvalidAnnotation(value = "Eyes", veterinarian="John") public void eyes() {}		// compiles ; short
}

@interface Music {
	   String[] genres();
}

@interface Rhythm {
    String[] value();	// combine array with value() declaration shorthand
 }

class Headphones {
	   @Music(genres={"Rock and roll"}) String mostDisliked; // compiles
	   @Music(genres="Classical") String favorite;			// compiles - {} are added automatic by compiler 
	   @Music(genres="Blues","Jazz") String shortList;  // DOES NOT COMPILE - provides more than one value
	   @Music(genres=) String phone;           			// DOES NOT COMPILE - missing {} declaration 
	   @Music(genres=null) String other;               // DOES NOT COMPILE - can't have null values
	   @Music(genres={}) String alternate;			// compiles - array without elements 
	   
	   // all compile:
	   @Rhythm(value={"Swing"}) String favoriteRhythm;
     @Rhythm(value="R&B") String secondFavoriteRhythm;
     @Rhythm({"Classical"}) String mostDislikedRhythm;
     @Rhythm("Country") String lastDislikedRhythm;
}
```

### @Documented
- _Javadoc_ is a standard within Java that generates documentation for a class or an API
- Documentation is generated using the marker annotation _@Documented_
- has it's own annotation that are in the comment starting with /** and ending with */
- @depcrated (Javadoc) is different of @Deprecated annotation (java annotation applied to a class / method)
	
```
public class ZooLightShow {
 
       /**
        * Performs a light show at the zoo.
        * 
        * @param      distance   length the light needs to travel.
        * @return     the result of the light show operation.
        * @author     Grace Hopper
        * @since      1.5
        * @deprecated Use EnhancedZooLightShow.lights() instead.
        */
       @Deprecated() public static String perform(int distance) {
          return "Beginning light show!";
       }
    }
```

### @Inherited
- it's a marker annotation
- if applied to a class, subclasses will inhearit the annotation information found in parent class
- import must be done for _java.lang.annotation.Inherited_ or else compilation error if missing 
- @Target(value={ANNOTATION_TYPE}) - meaning only applied on other annotation and not on classes or other types

```
// Vertebrate.java
import java.lang.annotation.Inherited;

@Inherited public @interface Vertebrate {} 
 
// Mammal.java
@Vertebrate public class Mammal {}
 
// Dolphin.java - @Vertebrate will be applied here also
public class Dolphin extends Mammal {} 

// Whale.java 
@Inherited public class Whale {} // DOSE NOT COMPILE - not allowed at this type, only for other annotation 
```

### @Repeatable
- this allows an annotation to be specified more than once on a type
- requires to create 2 annotations for compleating this task
- A second annotation must be created using as element an array of the 1st annotation 
- @Repeatable must be followed by the annotation that should be repeated _@Repeatable(Risks.class)_

![Repeatable Annotation](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/Repeatable%20Annotations.gif)

### Reviewing Annotation‐Specific Annotations

|Annotation|Marker annotation|Type of value()|Default compiler behavior (if annotation not present) |
|---|----|----|----|
|@Target 	|No|Array of ElementType|	Annotation able to be applied to all locations except TYPE_USE and TYPE_PARAMETER|
|@Retention 	|No|RetentionPolicy 	|RetentionPolicy.CLASS |
|@Documented 	|Yes|—|Annotations are not included in the generated Javadoc.|
|@Inherited 	|Yes|—|Annotations in supertypes are not inherited.|
|@Repeatable 	|No|Annotation|	Annotation cannot be repeated.|

### @Override
- it's a marker annotation
- indicated a method is overriding an inherited method from an interface or parent class
- for corect overriding , method must have same signature and same or broader access modifier and covariant return type and not declare any new or broader checked exception
- if one of the rules above is broken, _@Override_ annotation will fail to compile
```
interface Intelligence {
   int cunning();
}
public class Canine implements Intelligence {
   @Override public int cunning() { return 500; }
   void howl() { System.out.print("Woof!"); }
}
class Wolf extends Canine {
   @Override
   public int cunning() { return Integer.MAX_VALUE; }
   @Override void howl() { System.out.print("Howl!"); }
}

class Dog extends Canine {
	   @Override
	   public boolean playFetch() { return true; }  // DOES NOT COMPILE - no method playFetch() in parent class
	   @Override void howl(int timeOfDay) {}        // DOES NOT COMPILE - not same signature in parent class
	}

class Cat extends Canine {
	   //@Override
	   public boolean playFetch() { return true; }  // compiles - no annotation to check validity of override
	  // @Override 
	   void howl(int timeOfDay) {}        // compiles - no annotation to check validity of override
}
```
### @Deprecated
- notify others of a method that will be removed in the future
- can be applied to nearly any Java declaration _classes, methods_ or _variables_
- it's recomanded to add a Javadoc documentation to explain alternatives befor removal
```
/**
 * Design and plan a zoo.
 * @deprecated Use ParkPlanner instead.
 */
@Deprecated
public class ZooPlanner { … }
```
### @SuppressWarnings
-	requires a _String [] value()_ parameter "deprecation" or "unchecked" 
-	it's applied to class,method to ignore compiler warnings
-	_"deprecation"_ Ignore warnings related to types or methods marked with the @Deprecated annotation
-	_"unchecked"_ Ignore warnings related to the use of raw types, such as List instead of List<String>
	
### @SafeVarargs 
	- it's a marker annotation
	- can be applied only on constructors or method _private,static_ or _final_
	- indicates for other developers when varargs are used in parameter that the method dose not perform any unsafe operations
	- will suppress unchecked compiler warnings for varargs when annotation is used 
	- annotation will only remove compiler warnings and not fix code in case of error as per example : 
	![SafeVarargs](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/SafeVarargs.gif)
	
### Review Annotations
	
|Annotation|Marker annotation|Type of value()|Optional members|
|----|----|----|----|
|@Override 	|Yes| —  |—  |
|@FunctionalInterface 	|Yes| — |  — |
|@Deprecated 	|No| — |String since() ; boolean forRemoval() |
|@SuppressWarnings 	|No|String [ ]  | — |
|@SafeVarargs 	|Yes| — | — |
	
|Annotation|Applies to|Compiler error when|
|---|---|----|
|@Override 	|Methods|Method signature does not match the signature of an inherited method|
|@FunctionalInterface 	|Interfaces|Interface does not contain a single abstract method|
|@Deprecated 	|Most Java declarations|—|
|@SuppressWarnings 	|Most Java declarations|—|
|@SafeVarargs 	|Methods, constructors|Method or constructor does not contain a varargs parameter or is applied to a method not marked private, static, or final|
	
