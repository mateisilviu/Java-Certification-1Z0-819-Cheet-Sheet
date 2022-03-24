# Exceptions and Localization

## Handeling Exceptions

- *try statment* is used to handle exceptions
- it's composed of *try* + *catch clauses* + optional *finally clause*
- *finally* runs regardless of wheter an exception was thrown or not
- the order of catch clauses are important, compiler error if a more general exception type is upper declared compared to other catch caluses

```Java
  try {
   // Protected code
  }
  catch (IOException e) {
   // Handler IOException 
  }
  catch (ArithmeticException | IllegalArgumentException e) {
   // multi-catch handler for either types of exceptions
  }
  catch (FileNotFoundException e) { // DOSE NOT COMPILE
   // unreachable block due to more general IOException declare on top
  }
  finally {
   // allways runs after try / catch blocks are finished
  }
```

### Exception caterogies

- all exception are inhearit from *Throwable* (java.lang.Throwable)
- there are 2 chatergories of exceptions in Java : checked or unchecked exception
- *Throwable* is a checked exception
- *java.lang.Exception* is inherted from *Throwable* and it's checked exception
- all other clasess that extend *Exception* are checked exception, exception from this rule is *java.lang.RuntimeException*
- *RuntimeException* and all exception that extend it are unchecked exceptions
- *java.lang.Error* is inherted from *Throwable* and it's an unchecked exception
- Some Unchecked exceptions :
  - ArithmeticException
  - ArrayIndexOutOfBoundsException
  - ArrayStoreException
  - ClassCastException
  - IllegalArgumentException
  - IllegalStateException
  - MissingResourceException
  - NullPointerException
  - NumberFormatException
  - UnsupportedOperationException
- Some Checked exceptions :
  - FileNotFoundException
  - IOException
  - NotSerializableException
  - ParseException
  - SQLException
  
### Throw vs Throws

- *throws* keyword is used only in method declaration / signature
- *throws* indicate that a method might throw the declared exception but is not obligated to do so
- *throw* is used inside code block followed by an exception type object
- if a checked exception is thrown using *throw* keyword then the method must declare it using *throws* or catch it (try-catch)

## Custome Exceptions

- programmer should decide if will create a checked or unchecked exception and will extend *Exception* or *RuntimeException*

### Declaration and Constructors

- a custom exception can have multiple constructors
- the optional *super()* is called automatic as per rules of inheritance
- a custom exception can have no constructor and compiler will generate the default no arg constructor
- JVM automatically prints a stack trace if the exception thrown is not handled by the program
- stack trace can be printed programatically

![Custom Exception Constructors](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/Custom%20Exceptions%20Constructors.gif)

## try‐with‐resources

- try‐with‐resources statements require resources that implement the *AutoCloseable* interface
- interface *AutoCloseable* has a single method **close()** that must be implemented (public void close() throws Exception;)
- interface *Closeable* extends *AutoCloseable* and it' used with NIO/IO classes, compatbile with try‐with‐resources but will throw *IOException*
- when multiple resources / objects are used in try‐with‐resources, they are closed in the reverse order that are declared
- resource declaration in try‐with‐resources are separated by semicolon (;), last one is optional
- resource declared in try‐with‐resources are in scope of the try block only, they are not available in catch / finally clauses
- *close()* method is called just after ending the try { } block

```Java
package com.silviu;

import java.util.Scanner;

public class MyFileReader implements AutoCloseable {
 private String tag;

 public MyFileReader(String tag) {
  this.tag = tag;
 }

 @Override
 public void close() {
  System.out.println("Closed: " + tag);
 }

 public static void main(String[] args) {
  try (var bookReader = new MyFileReader("monkey")) {
   System.out.println("Try Block");
  }  // close() method called after this line and before finally 
  finally {
   System.out.println("Finally Block");
  }
  /* Output:
  Try Block
  Closed: monkey
  Finally Block
  */
  try (var bookReader = new MyFileReader("1");
    var movieReader = new MyFileReader("2");
    var tvReader = new MyFileReader("3");) {
   System.out.println("Try Block");
  } finally {
   System.out.println("Finally Block");
  }
  /* Output:
  Try Block
  Closed: 3
  Closed: 2
  Closed: 1
  Finally Block
  */
  try (Scanner s = new Scanner(System.in)) {
      s.nextLine();
   } catch(Exception e) {
      s.nextInt(); // DOES NOT COMPILE - s is out of scope at this point 
   } finally {
      s.nextInt(); // DOES NOT COMPILE - s is out of scope at this point 
   }
 }
}
```

### Effectivly Final variables

- from Java 9, it's possible that resources declared before try-with-resources statment to be used inside it if they are *final* or *effectively final*
- exam trick question could contain usage of resources already closed, incorect usage of effectively final
- resources may never be closed correctly if they are opened/declared before try-with-resources statement

```Java
public void relax() {
      final var bookReader = new MyFileReader("4"); // final example
      MyFileReader movieReader = new MyFileReader("5"); // effectively final example
      try (bookReader;
           var tvReader = new MyFileReader("6");
           movieReader) {
         System.out.println("Try Block");
      } finally {
         System.out.println("Finally Block");
      }
   }
   /* Output:
   Try Block
   Closed: 5
   Closed: 6
   Closed: 4
   Finally Block
   */
public void disturbanceInForce() throws IOException {
   var writer = Files.newBufferedWriter(path);
   try(writer) {  // DOES NOT COMPILE
       writer.append("May the Force be with you!");
    }
   writer = null; // because effectively final was broke here 
  }

public void compilesButThrowsError() throws IOException {
   var writer = Files.newBufferedWriter(path);
   writer.append("This write is permitted but a really bad idea!"); // if here it happens to throw an error, resource will never be closed
   try(writer) {
      writer.append("Welcome to the zoo!");
   }
   writer.append("This write will fail!");  // IOException thrown here, writer is closed at this point
  }
```

## Suppressed exceptions

- when multiple exception are thrown, all except 1st one are called *suppressed exceptions*
- 1st exception is the primary exception and other are added into an array of *Throwable[]* type
- suppressed exceptions are accesible via *getSuppressed()* method
- if more than 2 resources will throw an exception (when closing) then the last one declared will have the primary exception and rest will be suppressed
- suppressed exceptions are applied only in try clause

![Suppressed Exceptions example](https://github.com/mateisilviu/Java-Certification-1Z0-819-Cheet-Sheet/blob/main/images/Suppressed%20Exceptions.gif)

## Assertions - not for exam anymore

## Dates and Times

| Class                   | Description                             | Example                 |
| ----------------------- | --------------------------------------- | ----------------------- |
| java.time.LocalDate     | Date with day, month, year              | Birth date              |
| java.time.LocalTime     | Time of day                             | Midnight                |
| java.time.LocalDateTime | Day and time with no time zone          | 10 a.m. next Monday     |
| java.time.ZonedDateTime | Date and time with a specific time zone | 9 a.m. EST on 2/20/2021 |

- static method *now()* can be called from all 4 classes above
- as per name *LocalDate.now()* has only a date
- *LocalTime.now()* will print the current time (hours, minutes, seconds and fractional seconds) of the machine where code is run
- *LocalDateTime.now()* will print both a *LocalDate* and *LocalTime* separated by charater 'T' when *toString()* is called
- *ZonedDateTime.now()* will use same output as *LocalDateTime* and adds after fractional seconds, the timezone locale **(+02:00[Europe/Bucharest])** as reference of Greenwich mean time (GMT)

```Java
System.out.println(LocalDate.now()); //2022-03-21
System.out.println(LocalTime.now()); //12:03:17.741
System.out.println(LocalDateTime.now()); //2022-03-21T12:03:17.741
System.out.println(ZonedDateTime.now()); //2022-03-21T12:03:17.741957400+02:00[Europe/Bucharest]
```

### of() Methods

- all constructors of LocalDate, LocalTime etc classes are private so *new LocalDate()* will not compile
- *of()* methods are using Factory Patter to return instance of the respective class
- 1st paramter is the year, next the month, next the day (*LocalDate,LocalDateTime,ZonedDateTime*)
- 1st paramter is the hour, the minute, seconds (optional), nanoseconds (optional) (*LocalTime*)
- a combination of above with date first then informations about time (*LocalDateTime,ZonedDateTime*)

```Java
  LocalDate date1 = LocalDate.of(2022, Month.MARCH, 20);
  LocalDate date2 = LocalDate.of(2022, 03, 20);
  LocalDate date3 = LocalTime.of(0, 0); // DOSE NOT COMPILE - cannot convert LocalTime to LocalDate
  LocalDate date4 = LocalDateTime.of(2022, Month.APRIL, 10, 0, 0); // DOSE NOT COMPILE - cannot convert LocalDateTime to LocalDate
  LocalDate date5 = LocalDate.of(0, 0, 0); // java.time.DateTimeException - Invalid value for MonthOfYear (valid values 1 - 12): 0
  LocalDate date6 = LocalDate.of(0, 1, 0);// java.time.DateTimeException: Invalid value for DayOfMonth (valid values 1 - 28/31): 0
  LocalDate date7 = LocalDate.of(2022, 2, 29);// java.time.DateTimeException: Invalid date 'February 29' as '2022' is not a leap year
  
  LocalTime time1 = LocalTime.of(6, 15); // hour minute
  LocalTime time2 = LocalTime.of(6, 15, 10); // hour minute second
  LocalTime time3 = LocalTime.of(6, 15, 10, 500 ); // hour minute second nanosecond
  LocalTime time4 = LocalTime.of(25, 15, 10, 500 ); // java.time.DateTimeException: Invalid value for HourOfDay (valid values 0 - 23): 25
  
  LocalDateTime localdatetime1 = LocalDateTime.of(2022,3,20,6,15); // minumum number of params (date + hour minute)
  LocalDateTime localdatetime2 = LocalDateTime.of(2022,3,20); // less than minumum is compilation error
  LocalDateTime localdatetime3 = LocalDateTime.of(2022,3,20,6,15,10,500); // maximum params including second , nanosecond
  LocalDateTime localdatetime4 = LocalDateTime.of(date1, time1); // can be composed of LocalDate Object + LocalTime Object
  
  ZonedDateTime localzoneddatetime1 = ZonedDateTime.of(localdatetime4, ZoneId.systemDefault());
  ZonedDateTime localzoneddatetime2 = ZonedDateTime.of(2022,3,20,6,15,10,500,ZoneId.of("Europe/Bucharest"));
  ZonedDateTime localzoneddatetime3 = localdatetime4.atOffset(ZoneOffset.of("-05:00")).toZonedDateTime(); // conversion to -5 GMT 

```

### Formatting Dates and Times

- Java provides *DateTimeFormatter* class as support to format a date / datetime to a specific format
- *DateTimeFormatter* will throw an exception if incompatible format is used
- has method *ofPattern()* where a *String* can be used with specific keywords/symbol respreseting date/time parts
- *DateTimeFormatter* is in package *java.time.format*, prior to Java 8, there was used *java.text.SimpleDateFormat* and there might be question with old format

```Java
  LocalDate date = LocalDate.of(2022, Month.MARCH, 20);
  LocalTime time = LocalTime.of(11, 12, 34);
  LocalDateTime dt = LocalDateTime.of(date, time);
  
  System.out.println(date.getDayOfWeek());  // SUNDAY
  System.out.println(date.getMonth());      // MARCH
  System.out.println(date.getYear());       // 2022
  System.out.println(date.getDayOfYear());  // 79
  
  System.out.println(date.format(DateTimeFormatter.ISO_LOCAL_DATE)); // 2022-03-20
  System.out.println(time.format(DateTimeFormatter.ISO_LOCAL_TIME)); // 11:12:34
  System.out.println(dt.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME)); // 2022-03-20T11:12:34
  
  var f = DateTimeFormatter.ofPattern("MMMM dd, yyyy 'at' hh:mm");
  System.out.println(dt.format(f));  // March 20, 2022 at 11:12
  
  DateFormat s = new SimpleDateFormat("MMMM dd, yyyy 'at' hh:mm"); // before Java 8 but still available in Java 11
  
  System.out.println(s.format(new Date()));  // October 20, 2020 at 06:15
```

#### Standard Date/Time Symbols

#### format() Method

#### Custom Text Values

## Internationalization and Localization

### Locale

### Localizing Numbers

#### Formatting Numbers

#### Parsing Numbers

#### Writing a Custom Number Formatter

### Localizing Dates

### Locale Category

### Resource Bundles

#### Create and usage of Resource Bundle

#### Selecting Resource Bundle Values

#### Formatting Messages

#### Properties Class
