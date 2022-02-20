## Chapter 17 - Modules
  ### **named module** 
  - is a module that contains _module-info.java_ file at the root of the JAR. Named modules are on the module path and not on classpath.
  ### **automatic module** 
  - is a module that dose not contain _module-info.java_ and exists on module path. It's a regular JAR , it might contain in _MANIFEST.MF_ file the name of the automatic module in the property _Automatic‐Module‐Name_ or will be generated from the name of the JAR itself if this _Automatic‐Module‐Name_ property is missing. 
  #### Renameing rules Automatic Module
    - If the MANIFEST.MF specifies an Automatic‐Module‐Name, use that. Otherwise, proceed with the remaining rules.
    - Remove the file extension from the JAR name. 
    - Remove any version information from the end of the name. A version is digits and dots with possible extra information at the end, for example, ‐1.0.0 or ‐1.0‐RC.
    - Replace any remaining characters other than letters and numbers with dots.
    - Replace any sequences of dots with a single dot.
    - Remove the dot if it is the first or last character of the result.
  ### **unnamed module** 
  -is a module that dose not contain _module-info.java_ or if it has one, will be ignored. It's a regular JAR that is on classpath.
  
### Comparing Module Types
  | Property | Named | Automatic | Unnamed | 
  |---------|---------|---------|--------|
  | A ___ module contains a _module-info.java_ file? | YES | NO | Ignored if present |
  | A ___ module exports which package to other modules? | The one described in _module-info_ | All packages | No packages |
  | A ___ module is reaadable by other modules on the module path? | YES | YES | NO |
  | A ___ module is readable by other JARs on the classpath? | YES | YES | YES |
  
### JDK modules
  - prior of using modules , the entire JDK was available for import into an application
  - with modules, the application specifies which part of JDK uses
  - _java.base_ module is the default module avaiable in any module based application
  - _java.base_ dosen't need explicit _requires_ directive, still compile if specified but redundant 
  - it contains _Collections, IO, Math, NIO.2, Concurrency_, etc
  - modules name begin with _java_ for APIs used and _jdk_ for APIs specific to JDK
  - following modules you should recognize for exam but not memorize 
  - _java.base, java.naming, java.smartcardio, java.compiler, java.net, .http, java.sql, java.datatransfer, java.prefs, java.sql.rowset, java.desktop, java.rmi, java.transaction.xa, java.instrument, java.scripting, java.xml, java.logging, java.se, java.xml.crypto, java.management, java.security.jgss, java.management.rmi, java.security.sasl_
  - _jdk.accessiblity, jdk.jconsole, jdk.naming.dns, jdk.attach, jdk.jdeps, jdk.naming.rmi, jdk.charsets, jdk.jdi, jdk.net, jdk.compiler, jdk.jdwp.agent, jdk.pack, jdk.crypto.cryptoki, jdk.jfr, jdk.rmic, jdk.crypto.ec, jdk.jlink, jdk.scripting.nashorn, jdk.dynalink, jdk.jshell, jdk.sctp, jdk.editpad, jdk.jsobject, jdk.security.auth, jdk.hotspot.agent, jdk.jstatd, jdk.security.jgss, jdk.httpserver, jdk.localdata, jdk.xml.dom, jdk.jartool, jdk.management, jdk.zipfs, jdk.javadoc, jdk.management.agent, jdk.jcmd, jdk.management.jfr_
### jdeps command
  - will show information about dependencies that needs to add _requires_ directive in order to migrate to module system
  - command will output _jdk.unsupported_ if a package like _import sun.misc.Unsafe_ is used in a class
```
jdeps --jdk-internals my.app.jar 
 
my.app.jar -> jdk.unsupported
   my.package.MyClass  -> sun.misc.Unsafe     
      JDK internal API (jdk.unsupported)
 
Warning: <omitted warning>
 
JDK Internal API      Suggested Replacement
________________      _____________________
sun.misc.Unsafe       See http://openjdk.java.net/jeps/260
```
### Bottom‐Up Migration Strategy
- works best when you are the owner of the entire code of any JAR file that is not yet a module
- during the migration there will be a mix of named modules and unamed modules
```
human ----> toolbox
 |          ^
 |          |
 ------> hammer 
```
```
human  
 |  |        
 |  |        
 |  |---> hammer
 |        |
 |--->    -> toolbox
```
1. Order projects based on dependencies , the ones without any dependencies are at the bottom, projects that do have dependencies are at the top
2. Pick the lowes-level project that was not yet migrated (ex _toolbox_)
3. Add _module-info.java_ to the project and _exports_ any package used by higher-level JAR file. Add _requires_ for any modules that it depends on.
4. Move the new created module from classpath to the module path
5. Other projects not yet migrated will remain on classpath
6. Repeat from step 2 

### Top‐Down Migration Strategy
- it's usefull when you don't have control of every JAR file used (ex a 3rd party library) 
- during the migration there will be a mix of named modules and automatic modules
1. Order projects based on dependencies , the ones without any dependencies are at the bottom, projects that do have dependencies are at the top
2. Place all projects on the module path
3. Pick the highes-level project that has not yet been migrated
4. Add _module-info.java_ file into selected project, add _exports_ / _required_ directives ; you may use automatic module name
5. Repeat from step 3 

### Service module
![alt text](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/service.png "Service example")
  - A _service_ is composed of an interface (service provider interface) and any class that the interface reference and a way to looking up implementation of the interface (service locator) 
  - a service provider "interface" can be an _abstract class_ and is not mandatory to be an _interface_ type
  - a _service locator_ is able to find any class that implements a service provider interface (SPI)
  - _ServiceLocator_ class is used to search for classes that implements the SPI
  - implementations are not part of the service
 ```
 // Souvenir.java
package zoo.tours.api;
 
public class Souvenir {
   private String description;
 
   public String getDescription() { 
      return description; 
   }
   public void setDescription(String description) { 
      this.description = description;
   }
}

// Tour.java
package zoo.tours.api;
 
public interface Tour {
   String name();
   int length();
   Souvenir getSouvenir();
}

// module-info.java
module zoo.tours.api {
   exports zoo.tours.api;
}

javac -d serviceProviderInterfaceModule 
   serviceProviderInterfaceModule/zoo/tours/api/*.java 
   serviceProviderInterfaceModule/module-info.java
 
jar -cvf mods/zoo.tours.api.jar -C serviceProviderInterfaceModule/ .
 ```
 
 ```
// TourFinder.java
package zoo.tours.reservations;
 
import java.util.*;
import zoo.tours.api.*;
 
public class TourFinder {
    // to find exaclty only one implementation of Tour class
   public static Tour findSingleTour() {
      ServiceLoader<Tour> loader = ServiceLoader.load(Tour.class);
      for (Tour tour : loader)
         return tour;
      return null;
   }
   // to find all imeplementation of Tour services
   public static List<Tour> findAllTours() {
      List<Tour> tours = new ArrayList<>();
      ServiceLoader<Tour> loader = ServiceLoader.load(Tour.class);
      for (Tour tour : loader)
         tours.add(tour);
      return tours;
   }
}
 ```
 ```
 // module-info.java
module zoo.tours.reservations {
   exports zoo.tours.reservations;
   requires zoo.tours.api;
   uses zoo.tours.api.Tour;
}
 ```
  - Compile everything :
 ```
 javac -p mods -d serviceLocatorModule 
   serviceLocatorModule/zoo/tours/reservations/*.java 
   serviceLocatorModule/module-info.java
 
jar -cvf mods/zoo.tours.reservations.jar -C serviceLocatorModule/ .
 ```
 ### Consumer 
  - a _consumer_/_client_ is a module that optains and uses a service
  - it acquires a service via service locator
  - is able to invoke methods provided by the service provider interface
  - the package that implements the SPI is not exported in _module-info.java_
  - _provides_ directive is used to indicated that a SPI is implemented by a specific class
  - _provides <<interfaceName>> with <<className>>;_ 
```
// Tourist.java
package zoo.visitor;
 
import java.util.*;
import zoo.tours.api.*;
import zoo.tours.reservations.*;
 
public class Tourist {
   public static void main(String[] args) {
      Tour tour = TourFinder.findSingleTour();
      System.out.println("Single tour: " + tour);
 
      List<Tour> tours = TourFinder.findAllTours();
      System.out.println("# tours: " + tours.size());
   }
}
```
```
// module-info.java
module zoo.visitor {
   requires zoo.tours.api;
   requires zoo.tours.reservations;
}
```
- compile and run everything 
```
javac -p mods -d consumerModule 
   consumerModule/zoo/visitor/*.java 
   consumerModule/module-info.java
 
jar -cvf mods/zoo.visitor.jar -C consumerModule/ .
java -p mods -m zoo.visitor/zoo.visitor.Tourist
```
### Service provider
  - is the implementation of a service provider interface
  - at runtime could exists multiple implementation for same SPI
```
// TourImpl.java
package zoo.tours.agency;
 
import zoo.tours.api.*;
 
public class TourImpl implements Tour {
   public String name() {
      return "Behind the Scenes";
   }
   public int length() {
      return 120;
   }
   public Souvenir getSouvenir() {
      Souvenir gift = new Souvenir();
      gift.setDescription("stuffed animal");
      return gift;
   }
}

// module-info.java
module zoo.tours.agency {
   requires zoo.tours.api;
   provides zoo.tours.api.Tour with zoo.tours.agency.TourImpl;
}
```
  
  ```
  javac -p mods -d serviceProviderModule 
  serviceProviderModule/zoo/tours/agency/*.java 
  serviceProviderModule/module-info.java
  jar -cvf mods/zoo.tours.agency.jar -C serviceProviderModule/ .
  // run again the program
  java -p mods -m zoo.visitor/zoo.visitor.Tourist
  
  //This time we see the following output:
  //Single tour: zoo.tours.agency.TourImpl@1936f0f5
    // # tours: 1
  ```
  ### ServiceLoader
    - public static <S> ServiceLoader<S> load(Class<S> service) { … } // used to load the service
    - public Stream<Provider<S>> stream() { … } // operate via Streams
  
10. _OptionalInt max = ServiceLoader.load(Tour.class)_
11. _**.stream()**_         
12. _.map(**Provider::get**)_
13. _.mapToInt(Tour::length)_
14. _.max();_
15. _max.ifPresent(System.out::println);_
  
  
|Artifact|Part of the service|Directives required in module‐info.java  |
| -------| ------------------| ----------------------------------------|
|Service provider interface| Yes| exports  |
|Service provider|No|requires |
| | |  provides  |
|Service locator| Yes | exports | 
| | |  requires | 
| | |  uses   | 
|Consumer | No | requires | 
