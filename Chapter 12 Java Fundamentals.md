## Chapter 12 - Java Fundamentals

### Enums

```
public enum Season {
   WINTER, SPRING, SUMMER, FALL
}
```
  - _enum_ keyword is used to create an enum / enumeration 
  - are considered constants and are usually in snake_case all uppercase 
  - has a couple of helper methods : _name()_ , _values()_, _ordinal()_ , _valueOf()_
  -  _toString()_ will print the name of the enum itself ex : System.out.println(Season.SUMMER); // SUMMER
  -  _equals_ or _==_ both can be used to compare enums 
  -  _for-each loop_ can be used on _values()_ method
  -  Season t = Season.valueOf("summer"); // Throws an IllegalArgumentException at runtime
  -  public enum ExtendedSeason extends Season { } // DOES NOT COMPILE as enums can't be extended
  -  usage of enums in _switch_

```
Season summer = Season.SUMMER;
switch (summer) {
   case WINTER:
      System.out.println("It's winter");
      break;
   case 1 : // trick and don't compile even if ordinal() of SPRING = 1 (integer cannot be compared to enums)
      System.out.println("It's spring");
      break;
   case Season.SUMMER: // will not compile as full name is not requried 
      System.out.println("It's summer");
      break;
   default:
      System.out.println("I can't tell what season is.");
}
```

  #### Constructors, fields and methods
    - any enum that has constructor, field or methods, must end with semicolon (;) 
    - constructors are _private_ and will not compile if _public_ or _protected_
    - constructor is called when an enum value is used and it's called only once, any other usage will not call constructor again
 
 ```
 public enum OnlyOne {
   ONCE(true);
   private OnlyOne(boolean b) {
      System.out.print("constructing,");
   }   
}
 
public class PrintTheOne {
   public static void main(String[] args) {
      System.out.print("begin,");
      OnlyOne firstCall = OnlyOne.ONCE;  // prints constructing,
      OnlyOne secondCall = OnlyOne.ONCE; // doesn't print anything
      System.out.print("end");
   }
}
```

   - enums could have _abstract_ methods declaration and could be declared only _public_ or _protected_ ; for _private_ there will be a compilation error 
   - if _abstract_ is declared then all enums must implement it
   - it's possible to collate 2 or more enums with same implementation of by remove abstract method and declare a default implementation:
   - if method is declared _final_ it can't be overriding and compilation error on respective lines if it dose

```
public enum Season {
   WINTER {
      public String getHours() { return "10am-3pm"; } // default implementation overriding example 
   },
   SUMMER {
      public String getHours() { return "9am-7pm"; }
   },
   SPRING, FALL; //mandatory semicolon or compilation error occurs
   public String getHours() { return "9am-5pm"; } // default implmentation example will be returned for SPRING and FALL 
   // public final String getHours() { return "9am-5pm"; } // WINTER , SUMMER getHours() lines will have compilation errors
   // public abstract String getHours(); this is not needed SPRING, FALL lines will have compilation errors 
}
```
   
   ### Inner Class
      - it's also called a _member inner class_ is non-_static_ type that is declared at same level as the methods, instrance variable, constructors
      - can be declared _public,protected_, package-private (default) or _private_
      - can extend any class and implement interfaces
      - can be marked _abastract_ or _final_
      - cannot declare _static_ fields or methods except for _static final_ fields
      - can access memebers of the outer class including _private_ members
      - can only be called from instanced of outer class
   
   ```
public class Outer {
   private String greeting = "Hi";
   
   protected class Inner {
      public int repeat = 3;
      public void go() {
         for (int i = 0; i < repeat; i++)
            System.out.println(greeting); // calls outer private members without problem
      }
   }
   
   public void callInner() {
      Inner inner = new Inner();
      inner.go();                      // without instance of Inner, go method coudn't be called
   }
   public static void main(String[] args) {
      Outer outer = new Outer();
      outer.callInner();
      
      Outer outer = new Outer();
      Inner inner = outer.new Inner(); // another way to create the inner class
      inner.go();
      
     Inner notPossibleDirectly = new Inner(); // DOES NOT COMPILE
} }
   ```
      - examples of what compiles and what dose not compile for inner classes and a 3rd inner class :
   
   ![Inner Class Req an Instance](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/Inner%20Class%20Req%20an%20Instance.gif)
   
   -  inner classes could have the same name variable as outer classes 
   -  using _this_ keyword could say what variable is accesed

```
public class A {
   private int x = 10;
   class B {
      private int x = 20;
      class C {
         private int x = 30;
         public void allTheX() {
            System.out.println(x);        // 30
            System.out.println(this.x);   // 30
            System.out.println(B.this.x); // 20
            System.out.println(A.this.x); // 10
   } } }
   public static void main(String[] args) {
      A a = new A();
      A.B b = a.new B();
      A.B.C c = b.new C();
      c.allTheX();
}}
```
      
   ### Static Nested Class
      - has the keyword _static_ in declaration of class
      - could be instantiated without an instanced of outer class
      - can't access instanced variables or methods in the outer class directly 
      - outer or enclosing class can access _private_ instance variables
      - java threats the enclosing class as if it were a namespace 
 
 ```
 // Enclosing.java
 package enclosing;
 public class Enclosing {
   public static class Nested {
      private int price = 6;
   }
   public static void main(String[] args) {
      Nested nested = new Nested();       // no need for instance of Enclosing
      System.out.println(nested.price);
} }

   //AnotherClass.java
   package anotherclass;
   
   import enclosing.Enclosing.Nested; // regular import ok 
   //import static enclosing.Enclosing.Nested; // static import example 
   public class AnotherClass {
      Nested nested;
   }
   
 ```
 
   ### Local Class
      - it's a nested class defined within a method
      - class will exist when the method is invoked and will go out of the scope as soon as it ends
      - instanced could be created only from the method where class is defined 
      - can be declared also in constructiors and initializers
      - they don't have an access modifier
      - they cannot be _static_ or declare _static_ fields / methods except for _static final_ fields
      - have access to all fields and methods of the enclosing class
      - can access local variables if they are _final_ or effectively final 
      
```
public class PrintNumbers {
   private int length = 5;
   public void calculate() {
      final int width = 20;
	  int height = 2;
      class MyLocalClass {
         public void multiply() {
            System.out.print(length * width);
         }
		 
		 public void volume() {
			System.out.print(length * width * height); // DOES NOT COMPILE because height is not final / effectively final
		 }
      }
	  height = 3;
	  
      MyLocalClass local = new MyLocalClass();
      local.multiply();
	   local.volume(); // compiles
   }
   public static void main(String[] args) {
      PrintNumbers outer = new PrintNumbers();
      outer.calculate();
   }
}
```
   
   ### Anonymous Class
      - it's a special form of class that dosen't have a name
      - it's declared and instantiated all in one statment using _new_ keyword + type name with () then a set of braces { }
      - they are requred to extend an existing class or implement an existing interface
      - they are used for short implementation on the spot 
      - you can't extend a _class_ and implement an _interface_ with anonymous class
      - can exist inside argument of a method
      - can exists outside method implementation 
      
```
  public class OuterClass {
	
	interface Simple {}
	Simple anonymousOutsideMethod = new Simple() {}; // compiles 
	
   abstract class ExampleAbastract {
      abstract int someNumber();
   }
   
   interface ExampleInterface {
		int someNumber();
   }
   public int calculation(int basePrice) {
      ExampleAbastract saleAbastract = new ExampleAbastract() {
         int someNumber() { return 3; }
      };  // Don't forget the semicolon!
	  
	   ExampleInterface saleInterface = new ExampleInterface() {
         public int someNumber() { return 10; }     
      };  // Don't forget the semicolon!
      
      if (basePrice == 0)
    	  return otherCalculation(new ExampleInterface() { // inside method anonymous class example 
         public int someNumber() { return 100; }
         }); // count {} and () 			  
      return basePrice - saleAbastract.someNumber() + saleInterface.someNumber(); // both interface and abastract class called 
   } 
   
   public int otherCalculation(ExampleInterface example) {
	   return example.someNumber();
   }
   
}
```
   
   #### Review nested classes
   
   |Permitted Modifiers|Inner class|static nested class|Local class|Anonymous class  |
   |---|---|----|----|----|
   |Access modifiers |All|All|None|None |
   |abstract |Yes|Yes|Yes|No |
   |Final |Yes|Yes|Yes|No|
   
   |Permitted Members|Inner class|static nested class|Local class|Anonymous class  |
   |---|---|----|----|----|
   |Instance methods|Yes|Yes|Yes|Yes |
   |Instance variables|Yes|Yes|Yes|Yes |
   |static methods|No|Yes|No|No |
   |static variables|Yes (if final) |Yes|Yes (if final) |Yes (if final)|
   
   | |Inner class|static nested class| Local class|Anonymous class  |
   |----|----|---|---|----|
   |Can extend any class or implement any number of interfaces|Yes|Yes|Yes|No—must have exactly one superclass or one interface |
   |Can access instance members of enclosing class without a reference|Yes|No|Yes (if declared in an instance method)|Yes (if declared in an instance method) |
   |Can access local variables of enclosing method|N/A|N/A|Yes (if final or effectively final)|Yes (if final or effectively final)|
  
   
   ### Understanding interface members
   
   ### Functional programming intorduction 
