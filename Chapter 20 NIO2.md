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
#### NIO 2 Files class
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
  ```
  var path = Path.of("/animals/gopher.txt");
  try (var reader = Files.newBufferedReader(path)) {
   String currentLine = null;
   while((currentLine = reader.readLine()) != null)
      System.out.println(currentLine);
  }
  ```
##### public static BufferedWriter newBufferedWriter (Path path, OpenOption… options) throws IOException
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
