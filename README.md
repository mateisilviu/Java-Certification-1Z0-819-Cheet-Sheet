# Java-Certification-1Z0-819-Cheet-Sheet
A summary of relevant information about what you need to know for 1Z0-819 Java Certification

This cheet sheet / resume is based on the book : [OCP Oracle Certified Professional Java SE 11 Developer Complete Study Guide Exam 1Z0-815, Exam 1Z0-816, and Exam 1Z0-817 by Jeanne Boyarsky, Scott Selikoff](https://www.wiley.com/en-gb/OCP+Oracle+Certified+Professional+Java+SE+11+Developer+Complete+Study+Guide%3A+Exam+1Z0+815%2C+Exam+1Z0+816%2C+and+Exam+1Z0+817-p-9781119619130)
## Part I - Exam 1Z0‐815, OCP Java SE 11 Programmer I
### Chapter 1 
  -  JDK contains following programs to help you create Java programms *javac* ; *java* ; *javadoc* 
  -  From Java 11 you cand run a class without *javac* command by type *java MyClass.java parameter* where *parameter* is optional ; no *.class* file is created
  -   | Full command                                  | Single-file source-code command             |
      | --------------------------------------------- |---------------------------------------------|
      | javac HelloWorld.java /java HelloWorld                            |                                             
      | Produces a class file                         | Fully in memory                             |
      | For any program                               | For programs with one file                  |
      | Can import code in any available Java library | Can only import code that came with the JDK |
  -   
### Chapter 11 - Modules
  - JPMS stands for _Java Platform Module System_ and was introduced in Java 9  to group code at a higher level. With this you could expose specific packages outside the module.
  - it needs a special file named _module-info.java_
  - modules are used to encapsulate / hide specific code at the package level
  - modules solve and make a clearer dependency management
  - JPMS could be used to have a smaller JDK emmbeded into your final program / or image (docker) _not for exam - command jlink_
  - modules improve performance
##### module-info.java structure 
  - the file must be in the root directory of your module
  - must use the keyworkd _module_
  - modules name follow same rules as for package (allowed dots (.) not allowed dashes (-)
  - could be empty

##### compiling module
  Having following folder strcuture :
  ```
modulesAreExportedHere
│   
mymodule  
│   module-info.java
└───com
    │
    └───example
        │   MyClass.java 
```
```
module my.module.name {
}
```
  Following 4 examples are equivalent 
``` 
  javac --module-path modulesAreExportedHere 
        -d mymodule
        mymodule/com/example/*.java
        mymodule/*.java
```
``` 
  javac -p modulesAreExportedHere 
        -d mymodule
        mymodule/com/example/*.java
        mymodule/module-info.java
```
``` 
  javac --module-path modulesAreExportedHere 
        -d mymodule
        mymodule/com/example/MyClass.java
        mymodule/module-info.java
```
``` 
  javac -p modulesAreExportedHere 
        -d mymodule
        mymodule/com/example/MyClass.java
        mymodule/*.java
```
  - syntax _--module-path_ and _-p_ are equivalent and can be switched anytime 
  - _-d_ <dir> is the directory for class files ; it dosn't have a longer form
  - _-p_ <path> module path ; it has longer form _--module-path_
#### running a module
  |   | Location of modules | | module name | module separator | package name | class name
  | - | --------------------|-|-------------| ---------------- | -------------| -----------
  | java| --module-path modulesAreExportedHere | --module| my.module.name | / | com.example.|MyClass
  ```
  java --module-path modulesAreExportedHere --module my.module.example/com.example.MyClass
  ```
   - _--module_ keyworkd has a shorter form _-m_
   - _jar -cvf modulesAreExportedHere/com.example.MyClass.jar -C mymodule/ ._ will package everything under the folder _mymodule_ and creates a _.jar_ file into folder _modulesAreExportedHere_ 
  - running the jar is like _java -p modulesAreExportedHere -m my.module.name/com.example.Class_
  - _exports_ keyworkd followed by the package name is in _modul-info.java_ file ; it's used to export a specific **package** to other modules _kind like 'public' keyword_
  - order of the compilation matter when you have multiple classes
  - you could export a **package** to a specific **module** using keyworkd **to** _exports my.package.name **to** other.module.name_
  - _requires_ keyworkd is used to specifies that a **module** is needed
  - if a package is exported then all _public_ classes, interfaces, enums and _public_ _protected_ fields are visible/exported to other classes in other modules
  - let's say _moduleA_ has _requires transitive moduleB_ ; _transitive_ keyword will make any module (let's say _moduleC_) to also requires implicit _moduleB_
  - duplicate _requres_ and/or _requres transitive_ on same module name will result in compilation error
  - _jdeps_ command gives you information about dependencies within a module
```
  jdeps -summery modulesAreExportedHere/com.example.MyClass.jar
  ...
  jdeps -s modulesAreExportedHere/com.example.MyClass.jar
  ...
  jdeps --module-path modulesAreExportedHere modulesAreExportedHere/com.example.MyClass.jar
  ...
  jdeps -p modulesAreExportedHere modulesAreExportedHere/com.example.MyClass.jar
  ...
  jdeps -summary --module-path modulesAreExportedHere modulesAreExportedHere/com.example.MyClass.jar
  ...
  jdeps -s -p modulesAreExportedHere modulesAreExportedHere/com.example.MyClass.jar
```

## Part II - Exam 1Z0-816, OCP Java SE 11 Programmer II

### Chapter 17 - Modules
