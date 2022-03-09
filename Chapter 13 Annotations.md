## Annotations 
  - are used to assign metadata attributes to classes, methods, variables and other Java types
  - establish relationships that make it easier to manage data about our application
  - ascribes custom information on the declaration where it is defined
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
  - any of keywords used for annotation elements like _protected_, _private_, _final_
  - annotation constant are implicitly _public,static_ and _final_
  - annotation are not considered elements so they could exists in marker annotation
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
