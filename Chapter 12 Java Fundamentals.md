## Chapter 12 - Java Fundamentals

### Enums

```
public enum Season {
   WINTER, SPRING, SUMMER, FALL
}
```
  - _enum_ keyword is used to create an enum / enumeration 
  - are considered constants and are usually in snake_case all uppercase 
  - has a couple of halper methods : _name()_ , _values()_, _ordinal()_ , _valueOf()_
  -  _toString()_ will print the name of the enum itself ex : System.out.println(Season.SUMMER); // SUMMER
  -  _equals_ or _==_ both can be used to compare enums 
  -  _for-each loop_ can be used on _values()_ method
  -  Season t = Season.valueOf("summer"); // Throws an IllegalArgumentException at runtime
  -  public enum ExtendedSeason extends Season { } // DOES NOT COMPILE
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
