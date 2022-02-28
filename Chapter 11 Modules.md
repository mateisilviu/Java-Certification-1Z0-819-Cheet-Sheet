## Chapter 11 - Modules
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
  - duplicate _requires_ and/or _requires transitive_ on same module name will result in compilation error
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
  **Notice dashes in following possible commands. Some of them have up to 3 forms for same command**
```
      javac -cp <classpath>           |
            -classpath <classpath>    | + Location of the JAR in a nonmodular program
            --class-path <classpath>  | 
  -------------------------------------------------------------------------------------
            -d <dir>                  | + Directory to place generated .class files
  -------------------------------------------------------------------------------------
            -p <path>                 | + Location of JARs in a modular program
            --module-path<path>       | 
```
  ---
```
      java  -p <path>                 | + Location of JARs in a modular program
            --module-path<path>       | 
  -------------------------------------------------------------------------------------
            -m <name>                 | + Module name to run
            --module <name>           | 
  -------------------------------------------------------------------------------------
            -d                        | Describes the details of a module
            --describe-module         |
  -------------------------------------------------------------------------------------
            --list-modules            | List observable modules without running a program
  -------------------------------------------------------------------------------------
            --show-module-resolution  | Shows modules when running program
```
  ---
```
       jar  -c                  | Create a new .jar file
            --create            |
  -------------------------------------------------------------------------------------
            -v                  | Prints details when working with JAR files (log)
            --verbose           |
  -------------------------------------------------------------------------------------
            -f                  | Name of JAR file
            --file              |
  -------------------------------------------------------------------------------------
            -C                  | Directory containing files to be used to create the JAR
  -------------------------------------------------------------------------------------
            -d                  | Describes the details of a module
            --describe-module   |
```
  ---
```
          jdeps --module-path          | Location of JARs in a modular program
  -------------------------------------------------------------------------------------
                -s                     | Shows modules dependencies 
                -summary               |
```
