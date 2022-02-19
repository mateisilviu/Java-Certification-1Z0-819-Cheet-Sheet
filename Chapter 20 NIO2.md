## NIO - Non‐blocking Input/Output API

  - a replacement for _java.io_ API
  - packages start with _java.nio_

### Path
  - it's a interface and represents a hierarchical path on the disk to a file or directory
  - it might refer an absolut path or relative path (same as _java.io.File_)
  - it has support for _symbolic link_ (_java.io.File_ dosn't have support for this)
#### How to create a Path
| Ctr | Method | General method signature |Example |
| ----| -------|----|------|
| a) | via Path Interface | **public static Path of(String first, String… more)** | Path path1 = Path.of("bla/image.png"); Path path2 = Path.of("/home/blabla"); |
| a) |  | | Path path1 = Path.of("bla", "image.png"); Path path2 = Path.of("/", "home", "blabla"); |
| b) | via Paths Class | **public static Path get (String first, String… more)** | Path path1 = Paths.get("bla/image.png"); Path path2 = Paths.get("/", "home", "blabla"); |
| c) | via URI Class | **public URI(String str) throws URISyntaxException** | URI a = new URI("file://myfile.txt"); |
| c) | | **public static Path of(URI uri)** | Path b = Path.of(a); |
| c) | | **public static Path get(URI uri)** | Path c = Paths.get(a); | 
| c) | | **public URI toURI() **| URI d = b.toUri(); | 
| d) | via FileSystem Class | _getDefault()_ before and then **public Path getPath (String first, String… more)** | Path path1 = FileSystems.getDefault().getPath("bla/image.png"); |
| d) |  | public static FileSystem getFileSystem (URI uri) | Path path = FileSystems.getFileSystem(new URI("http://www.selikoff.net")).getPath("blabla.txt"); |
| e) | via _java.io.File_ Class | public Path toPath() | File file = new File("image.png"); Path path = file.toPath(); | 
#### Common NIO.2 method arguments
|Enum type|Interface inherited | Enum value | Details |
|-----| ---- | --- | --- | 
|LinkOption | CopyOption OpenOption | NOFOLLOW_LINKS | Do not follow symbolic links. |
| StandardCopyOption | CopyOption | ATOMIC_MOVE  | Move file as atomic file system operation. |
|  | | COPY_ATTRIBUTES | Copy existing attributes to new file. |
|  | | REPLACE_EXISTING | Overwrite file if it already exists. |
| StandardOpenOption  | OpenOption  | APPEND | If file is already open for write, then append to the end. |
| | | CREATE | Create a new file if it does not exist. |
| | | CREATE_NEW | Create a new file only if it does not exist, fail otherwise. |
| | | READ  | Open for read access. | 
| | | TRUNCATE_EXISTING | If file is already open for write, then erase file and append to beginning. | 
| | | WRITE  | Open for write access. | 
| FileVisitOption | N/A | FOLLOW_LINKS  | Follow symbolic links. |
#### Path methods
- Path instances are immutable
- **public String toString()** returns the entire Path as String
- **public int getNameCount()** returns the number of elements in path _Paths.get("/home/folder/file.extension").getNameCount()_ will return 3.
- **public Path getName(int index)** returns Path element at specific index ; applied above _path.getname(0)_ = Path("home") ; _path.getname(1)_ = Path("folder") ; _path.getname(2)_ = Path("file.extension") ; System.out.print(p.getName(0));     // IllegalArgumentException when p is '/' and getNameCount() = 0
- **public Path subpath(int beginIndex, int endIndex)** it may return multiple path components ; _beginIndex_ starts from 0 and _endIndex_ should be < getNameCount() or <= getNameCount() -1 ; any value not in this interval will result in IllegalArgumentException
- **public Path getFileName()** returns the _Path_ element of the current directory
- **public Path getParent()** returns the full path of the containing directory ; returns _null_ if executed on the root path itself or a the top of relative path; dose not traverse relative paths outside the current working directory
- **public Path getRoot()** returns the root element of the file within the file system or _null_ if the path is a relative path.
``` 
public void printPathInformation(Path path) {
   System.out.println("Filename is: " + path.getFileName());
   System.out.println("   Root is: " + path.getRoot());
   Path currentParent = path;
   while((currentParent = currentParent.getParent()) != null) {
      System.out.println("   Current parent is: " + currentParent);
   }
}
```
  | method executed for | Filename is | Root is | Current parent is (1st iteration) |  Current parent is (2nd iteration) |  Current parent is (3rd iteration) |
  | ---- | --- | ---| --- | --- | --- | 
  | printPathInformation(Path.of("zoo")); | zoo | null | | | | 
  | printPathInformation(Path.of("/zoo/armadillo/shells.txt")); | shells.txt | / | /zoo/armadillo | /zoo | / | 
  | printPathInformation(Path.of("./armadillo/../shells.txt")); | shells.txt | null | ./armadillo/.. | ./armadillo | . | 
- **getParent()** and **getRoot()** don't resolve path symbols
- **public boolean isAbsolute()** returns true if the path the object references is absolute and false if the path the object references is relative
- **public Path toAbsolutePath()** converts a relative Path object to an absolute Path object by joining it to the current working directory ; if the Path object is already absolute, then the method just returns the Path object
- **public Path resolve(Path other)** and **public Path resolve(String other)** similar to concatenation in Strings but with the exception if an absolute path is provided as input then that is the value returned ; you can't combine two absolute paths using any of the methods
- **public Path relativize(Path other)** it will create a Path object from the applied Path to the _other_ path using optional path symbols (. ; ..) 
if both path values are absolute, then the method computes the relative path from one absolute location to another, regardless of the current working directory.
if both path values are relative, then the _relativize()_ method computes the paths as if they are in the same current working directory.
it will throw an IllegalArgumentException if absolute and relative paths are mixed 

| path1 object | path2 object | print applied method example 1 |print applied method example 2 | 
| ---- | --- | --- | --- | 
|var path1 = Path.of("fish.txt"); | var path2 = Path.of("friendly/birds.txt"); | System.out.println(path1.relativize(path2)); _"../friendly/birds.txt"_ | System.out.println(path2.relativize(path1)); _"../../fish.txt"_ |
|Path path3 = Paths.get("E:\\habitat"); | Path path4 = Paths.get("E:\\sanctuary\\raven\\poe.txt"); | System.out.println(path3.relativize(path4)); _"..\sanctuary\raven\poe.txt"_ | System.out.println(path4.relativize(path3)); _"..\..\..\habitat"_ | 
| Path path1 = Paths.get("/primate/chimpanzee"); | Path path2 = Paths.get("bananas.txt"); | path1.relativize(path2); _// IllegalArgumentException due to mix relative - absolute_| |
| Path path3 = Paths.get("c:\\primate\\chimpanzee"); | Path path4 = Paths.get("d:\\storage\\bananas.txt"); | path3.relativize(path4); _// IllegalArgumentException on Windows OS due to different root directory / drive letter_ | |

- **public Path normalize()** method eliminates redundant path symbols from a path 
```
var p1 = Path.of("./armadillo/../shells.txt");
System.out.println(p1.normalize()); // shells.txt
 
var p2 = Path.of("/cats/../panther/food");
System.out.println(p2.normalize()); // /panther/food
 
var p3 = Path.of("../../fish.txt");
System.out.println(p3.normalize()); // ../../fish.txt 
```
- **public Path toRealPath(LinkOption… options) throws `IOException`** and **public Path toRealPath() throws `IOException`** this method eliminates any redundant path symbols , will join the path with the current working directory if the path is relative, will throw an expection of path dosen't exists. it will follow symbolic links if not override via LinkOption paramter
```
  // suppose a symbolic link is between /zebra to /horse 
  System.out.println(Paths.get("/zebra/food.txt").toRealPath()); // will print /horse/food.txt
  System.out.println(Paths.get(".././food.txt").toRealPath());   // will print /horse/food.txt
```
#### NIO 2 Files.java class methods
  - it's a helper class used to interact with real files / directories
  - will throw an _IOException_ if path dosen't exists on most of the methods
  - replicate numerous methods functionalities found in _java.io.File_ that are with different name or list of parameter 
##### public static boolean exists(Path path, LinkOption… options)
  - it checks if a file/directory at respective path parameter exists
  - this method dosn't throw any exception 
##### public static boolean isSameFile (Path path, Path path2) throws IOException
  - will resolve all path symbols and follow symbolic links
  - will return true if the 2 Path objects are same file or directory
  - will not throw exception if the 2 path objects are equals using _equals()_ method and will return _true_ without check if files/directory exists
  - will not compare file contents, if same files names , size etc are in different location, will return _false_
##### public static Path createDirectory (Path dir, FileAttribute<?>… attrs) throws IOException ; public static Path createDirectories (Path dir,FileAttribute<?>… attrs) throws IOException
  - _createDirectory()_ will create a directory and throw an exception if it already exists or the paths leading up to the directory do not exist
  - _createDirectories()_ will create all the target directory along the path, if some exists will skip, if all exists then will do nothing
##### public static Path copy (Path source, Path target, CopyOption… options) throws IOException 
  - will copy files/directories from on place to another
  - directories are copied as _shallow copy_ meaning that files and subdirectories within the directory are not copied 
  - oposite of above is _deep copy_ 
  - will throw an exception if the target file already exists unless _StandardCopyOption.REPLACE_EXISTING_ is provided
```
var file = Paths.get("food.txt"); // food.txt exists
var directory = Paths.get("/enclosure"); // dir /enclosure exists
Files.copy(file, directory); // will throw an expection because /enclosure already exists
// if /enclosure dosn't exists , food.txt will be copied into a new file named /enclosure
---
var directory = Paths.get("/enclosure").resolve(file.getFileName()); // a way to corect copy file above
```
##### public static Path move (Path source, Path target,CopyOption… options) throws IOException
  - will move files/directories from on place to another
  - could mean rename if the directory of the source and destination don't change or move and rename
  - if the target exists it will throw an exception unless _REPLACE_EXISTING_ option is set
  - will not put a file into a directory if the source is the directory , but will create a new file with the name of the directory _Files.move(Path.of("c:\\user\\addresses.txt"), Path.of("c:\\user-new)); // will move <<address.txt>> into a file without extension named <<user-new>>_
  - above statment rephrased : if the source path is a file (with an extension) , destination path should be also a file with (same) extension not a directory 
  - **StandardCopyOption.ATOMIC_MOVE** option will do the move within the file system as a single indivisible operation (Concurrency) ; if the system dosn't accept this feature will throw an _AtomicMoveNotSupportedException_ 
##### public static void delete (Path path) throws IOException ; public static boolean deleteIfExists (Path path) throws IOException
  - a directory must be empty in order to be deleted, both members throw exception if the directory is not empty 
  - if the path is a symbolic link, then this will be deleted not the file representing the link to
  - _delete()_ method throws an exception if the path dosn't exists (_NoSuchFileException_)
  - _deleteIfExists()_ will not throw an exception if path dosn't exists, it will return _true_/_false_ if succesfull.
##### public static BufferedReader newBufferedReader (Path path) throws IOException 
  - reads a file specified at _Path_ unsing a _BufferedReader_ object
  ```
  var path = Path.of("/animals/gopher.txt");
  try (var reader = Files.newBufferedReader(path)) {
   String currentLine = null;
   while((currentLine = reader.readLine()) != null)
      System.out.println(currentLine);
  }
  ```
##### public static BufferedWriter newBufferedWriter (Path path, OpenOption… options) throws IOException
    - writes a file specified at _Path_ unsing a _BufferedWriter_ object
  ```
  var list = new ArrayList<String>();
  list.add("Smokey");
  list.add("Yogi");

  var path = Path.of("/animals/bear.txt");
  try (var writer = Files.newBufferedWriter(path)) {
     for(var line : list) {
        writer.write(line);
        writer.newLine();
     }
  }
  ````
##### public static List<String> readAllLines (Path path) throws IOException
  - will read and store all content of specified file into a list
  - if file is large, coult trigger _OutOfMemoryError_
##### public static boolean isDirectory (Path path, LinkOption… options)
  - checks if the file from parameter is a directory
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static boolean isSymbolicLink (Path path)
  - checks if the file from parameter is a symbolic link 
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static boolean isRegularFile (Path path, LinkOption… options)
  - checks if the file from parameter can contain content
  - if the _Path_ is a symbolic link and it resolves to a 'regular file' then this method will return _true_
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static boolean isHidden (Path path) throws IOException
  - checks if file is declared hidden in the operating system
  - it throw _IOException_ if file dosesn't exists
##### public static boolean isReadable (Path path)
  - checks if file is declared readable in the operating system
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static boolean isWritable (Path path)
  - checks if file is declared writable in the operating system
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static boolean isExecutable (Path path)
  - checks if file is declared executable and can be executed in the operating system
  - it doesn't throw _IOException_
  - return _false_ if file dosen't exists
##### public static long size (Path path) throws IOException
  - returns the size of the file in bytes
  - size on disk may differ 
  - calling this method on directories is undefined and result depends on the operating system 
##### public static FileTime getLastModifiedTime (Path path,LinkOption… options) throws IOException
  - get last time a file was modified represented as _FileTime_ timestamp
##### public static <A extends BasicFileAttributes> A readAttributes(Path path,Class<A> type, LinkOption… options) throws IOException
  - a single method to retrive all attributes of a file as oppsite of calling each one of the methods above (isHidden, isDirectory, size, etc) 
  - there are specific view interfaces based on the operating system and a common interface _BasicFileAttributes_
  | Attributes interface | View interface | Description  |
  | ---------------------| ---------------| -------------| 
  |BasicFileAttributes   |BasicFileAttributeView | Basic set of attributes supported by all file systems | 
  |DosFileAttributes |DosFileAttributeView |Basic set of attributes along with those supported by DOS/Windows‐based systems |
  |PosixFileAttributes | PosixFileAttributeView | Basic set of attributes along with those supported by POSIX systems, such as UNIX, Linux, Mac, etc.|
  
  ```
  var path = Paths.get("/folder/file.txt");
  BasicFileAttributes data = Files.readAttributes(path,BasicFileAttributes.class);

  System.out.println("Is a directory? " + data.isDirectory());
  System.out.println("Is a regular file? " + data.isRegularFile());
  System.out.println("Is a symbolic link? " + data.isSymbolicLink());
  System.out.println("Size (in bytes): " + data.size());
  System.out.println("Last modified: " + data.lastModifiedTime());
  ```
##### public static <V extends FileAttributeView> V getFileAttributeView (Path path,Class<V> type,LinkOption… options) 
  - using this method to update (some) file attribute 
  - you can't change a file into a directory or the other way arround using this method
  - you can't change a file size via attribute wihtout modifing the content of it
  ```
  // Read file attributes
  var path = Paths.get("/folder/file.txt");
  BasicFileAttributeView view = Files.getFileAttributeView(path,BasicFileAttributeView.class);
  BasicFileAttributes attributes = view.readAttributes();
  // Modify file last modified time
  FileTime lastModifiedTime = FileTime.fromMillis(attributes.lastModifiedTime().toMillis() + 1000);
  view.setTimes(lastModifiedTime, null, null);
  ```
  - **public void setTimes(FileTime lastModifiedTime,FileTime lastAccessTime, FileTime createTime)** accepts _null_ values for any date/time value that we don't want to modify
##### public static Stream<Path> list (Path dir) throws IOException
  - method is similar to _java.io.File_ _listFiles()_ difference is that this returns a stream of _Path_ and not an array _File[]_
  ``` // deep copy example
  public void copyPath(Path source, Path target) {
   try {
      Files.copy(source, target);
      if(Files.isDirectory(source))
         try (Stream<Path> s = Files.list(source)) {
            s.forEach(p -> copyPath(p, 
               target.resolve(p.getFileName())));
         }
   } catch(IOException e) {
      // Handle exception
   }
  }
  ```
  - method will not follow symbolic links 
  - stream must be closed as per above example with _try-with-resources_ method or otherways is a resource leak
  - **a terminal operation on stream will not close the underlying file resource**
##### public static Stream<Path> walk (Path start, FileVisitOption… options) throws IOException ; public static Stream<Path> walk (Path start, int maxDepth, FileVisitOption…options) throws IOException
  - it's used to visit all paths of a directory tree (_traversing a directory_ ) starting from the parent  and iterate over all descendants until a perticular condition is met (a file is seached, or multiple files with specific extension)
  - it uses _depth‐first search_ algorithm meainng that the traverse will go from root to an arbritary leaf (of the tree directory) then navigates back to the root. Any path is skipped along the way.
  - _maxDepth_ parameter is used in _depth‐first search_ as per the distance from the root to maximum leaf length 
  - when _maxDepth_ parameter is missing, default depth is _Integer.MAX_VALUE_
  - **FileVisitOption.FOLLOW_LINKS** option is required in order to follow symbolic links ; this could lead to a cycle if a path is visited twice and throws _FileSystemLoopException _
##### public static Stream<Path> find (Path start,int maxDepth,BiPredicate<Path, BasicFileAttributes> matcher,FileVisitOption… options) throws IOException
  - similar with method _walk()_ and it takes a _BiPredicated_ filter and _maxDepth_ is mandatory
  - it automatically retrives the basic file information and filter them based on predicate
  ```
  Path path = Paths.get("/bigcats"); // searches all .java files of minimum size 1000 bytes
  long minSize = 1_000;
  try (var s = Files.find(path, 10, 
        (p, a) -> a.isRegularFile()
           && p.toString().endsWith(".java")
           && a.size() > minSize)) {
     s.forEach(System.out::println);
  }
  ```
 ##### public static Stream<String> lines (Path path) throws IOException 
  - a better method compared to _Files.readAllLines()_ because the content of the file are read and processed lazily and only a small portion of the file is stored in memory
  
  #### Legacy vs NIO.2 methods
 |Legacy I/O File method|NIO.2 method| 
  | ---------------| ---------------|
|file.delete() |Files.delete(path) | 
|file.exists() |Files.exists(path) | 
|file.getAbsolutePath() |path.toAbsolutePath() | 
|file.getName() |path.getFileName() | 
|file.getParent() |path.getParent() | 
|file.isDirectory() |Files.isDirectory(path) | 
|file.isFile() |Files.isRegularFile(path) | 
|file.lastModified() |Files.getLastModifiedTime(path) | 
|file.length() |Files.size(path) | 
|file.listFiles() |Files.list(path) | 
|file.mkdir() |Files.createDirectory(path) | 
|file.mkdirs() |Files.createDirectories(path) | 
|file.renameTo(otherFile) |Files.move(path,otherPath) | 

  
  ### Common exam tricks
  
  #### Mix _Files.readAllLines()_ with _Files.lines()_
  ``` 
  Files.readAllLines(Paths.get("birds.txt"))
      .filter(s -> s.length()> 2)
      .forEach(System.out::println);
  ```
 - this will not compile as _readAllLines()_ returns a _List_ not a _Stream_ ; _filter_ method is not available for _List_ 
