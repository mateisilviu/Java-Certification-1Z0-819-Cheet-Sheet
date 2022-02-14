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
- **public Path getName(int index)** returns Path element at specific index ; applied above _path.getname(0)_ = Path("home") ; _path.getname(1)_ = Path("folder") ; _path.getname(2)_ = Path("file.extension") ; 


