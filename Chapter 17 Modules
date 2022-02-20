## Chapter 17 - Modules
  - **named module** is a module that contains _module-info.java_ file at the root of the JAR. Named modules are on the module path and not on classpath.
  - **automatic module** is a module that dose not contain _module-info.java_ and exists on module path. It's a regular JAR , it might contain in _MANIFEST.MF_ file the name of the automatic module in the property _Automatic‐Module‐Name_ or will be generated from the name of the JAR itself if this _Automatic‐Module‐Name_ property is missing. 
  - **unnamed module** is a module that dose not contain _module-info.java_ or if it has one, will be ignored. It's a regular JAR that is on classpath.
  
### Comparing Module Types
  | Property | Named | Automatic | Unnamed | 
  |---------|---------|---------|--------|
  | A ___ module contains a _module-info.java_ file? | YES | NO | Ignored if present |
  | A ___ module exports which package to other modules? | The one described in _module-info_ | All packages | No packages |
  | A ___ module is reaadable by other modules on the module path? | YES | YES | NO |
  | A ___ module is readable by other JARs on the classpath? | YES | YES | YES |
