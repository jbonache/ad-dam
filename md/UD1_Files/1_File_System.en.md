# File system

## 1. Introduction 
In the beginning of computer science, files were the only mechanism to store information. Nowadays, Operating Systems manage storage devices almost transparently to the user. Without going into details of the underlying storage technologies, they offer us an abstraction by means of which we have only to worry about two concepts: files and directories.

- **File** → information containers (all kind and format of the information).
- **Directories** → file organizers, and may contain files and other directories.

Following with information (the content of the file), we can find a classical classification:

- **Text File** → the information is stored in files, so that can be viewed or opened with any _plain text_ editor, like vi, nano, or notepad. 
- **Binary File** → the information is stored coded in binary format. As the student know, any kind of information (text, numbers, photos, etc.) can be traduced to 0's and 1's. In our way (programmers) we can transform objects and variables in different ways.

!!!important "Notice than..."
    - Regardless that we store information in text file, it appears a new concept: **structure**. If we organize information in several ways, we can speak about csv files, xml files, json files. The csv, xml and json are structures, because at the end, all of them are text files.
    - People think that text files are weak files to store information, but it is not true. Cryptographic technics offers us methods to store information inside text files safety. For example 'htpasswd' in 'apache' or '/etc/shadow' in Linux systems. At the bottom you can view a PGP public key sample, stored in a text file



```bash
-----BEGIN PGP PUBLIC KEY BLOCK-----
Comment: Alice's OpenPGP certificate
Comment: https://www.ietf.org/id/draft-bre-openpgp-samples-01.html

mDMEXEcE6RYJKwYBBAHaRw8BAQdArjWwk3FAqyiFbFBKT4TzXcVBqPTB3gmzlC/U
b7O1u120JkFsaWNlIExvdmVsYWNlIDxhbGljZUBvcGVucGdwLmV4YW1wbGU+iJAE
...
DAAKCRDyMVUMT0fjjlnQAQDFHUs6TIcxrNTtEZFjUFm1M0PJ1Dng/cDW4xN80fsn
0QEA22Kr7VkCjeAEC08VSTeV+QFsmz55/lntWkwYWhmvOgE=
=iIGO
-----END PGP PUBLIC KEY BLOCK-----
```

## 2. Accessing the file system in Java

`Java` offers us several ways to access the file system. This way is independent of the underlying device that stores the information. The device can be a hard disk, ssd disk, optical drive, etc. The class that offers us this possibility is the [File](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/io/File.html) class. We are going to review the main facts of this class, and then revise some samples.

- The constructor of the class is overloaded, as most java classes. We could use:
  - `File(File parent, String child)` → Creates a new File instance from a parent abstract pathname and a child pathname string.
  - `File(String pathname)` → Creates a new File instance by converting the given pathname string into an abstract pathname.
  - `File(String parent, String child)` → Creates a new File instance from a parent pathname string and a child pathname string.
  - `File(URI uri)` → Creates a new File instance by converting the given file: URI into an abstract pathname.


- To access the file we must use a _path name_. This is the path, and we have two possibilities:
  - **Absolute path** → the path from the root directory of the system to the file we would, starting with `/` in Linux systems or `letter:` in Windows systems. Remember to use `/` to separate folders in Linux and `\\` in Windows. It is not an erratum, due to the backslash is a symbol with own meaning, we have to escape it, doubling it to get the meaning we would.
  - **Relative path** → we suppose that the file we would is in the same folder that the project/program we are executing. In this case we only provide the file name, without any folder at the beginning of the path.
  
```java
// absolute paths
File f=new File("/home/joange/texto.md") //  Linux
File f=new File("C:\\Usuarios\\joange\\Dektop\\texto.md") //  Windows
...
// relative paths
File f=new File("texto.md") //  Linux
File f=new File("docs\\texto.md") //  Windows
```
- The same class `File` is used to access either a regular file or a directory. Is task of the programmer to difference between them with the file methods as well as to check if a file exists. To do this, we could use several methods like:
  - `boolean exists()` → returns true if the file object exists, and false otherwise.
  - `boolean isFile()` and `isDirectory()` → returns true either the object is a regular file or a directori. Obviously, these methods are exclusive

## 3. Creating files

If we want to create a folder or a regular file, we can use this methods

- `boolean createNewFile()` → Atomically creates a new, **empty file** named by this abstract pathname if and only if a file with this name does not yet exist.
- `static FilecreateTempFile(String prefix, String suffix)` → Creates an empty file in the default temporary-file directory, using the given prefix and suffix to generate its name.
- `boolean mkdir()` → Creates the directory named by this abstract pathname.
- `boolean mkdirs()` → Creates the directory named by this abstract pathname, including any necessary but nonexistent parent directories.
- `boolean renameTo(File dest)` → Renames the file denoted by this abstract pathname.
- `boolean delete()`→ Deletes the file or directory denoted by this abstract pathname.

!!!note
    - In next sections we will study how to create files when we save content inside them. In other words, we do not need to create the file _adhoc_ and then populate. There are mechanisms to create the file in an automatized way.
    - The difference between mkdir and mkdirs is that the second option will create all directories between the root file system and the current directory meanwhile the first need that the path exists.


## 4. Asking for file properties

Normally we open files to write or read their content, but sometimes we need to ask for file properties, like size, permits and so. File class will help us again.

General information:

- `boolean exists()` → Tests whether the file or directory denoted by this abstract pathname exists.
- `long lastModified()` → Returns the time that the file denoted by this abstract pathname was last modified.
- `long length()` → Returns the length of the file denoted by this abstract pathname.

Information about permits. This information is the information than the user can recover with `ls -la` in the `chmod` style:

- `boolean canExecute()` → Tests whether the application can execute the file denoted by this abstract pathname.
- `boolean canRead()` → Tests whether the application can read the file denoted by this abstract pathname.
- `boolean canWrite()` → Tests whether the application can modify the file denoted by this abstract pathname.

Content of a directory:

- `String[] list()` → Returns an **array of strings** naming the files and directories in the directory denoted by this abstract pathname.
- `File[] listFiles()` → Returns an array of abstract pathnames denoting the files in the directory denoted by this abstract pathname.
- `String[] list(FilenameFilter filter)` → Returns an array of strings naming the files and directories in the directory denoted by this abstract pathname that satisfy the specified filter.
- `File[] listFiles(FilenameFilter filter)` → Returns an array of abstract pathnames denoting the files and directories in the directory denoted by this abstract pathname that satisfy the specified filter.

!!!tip
    We suggest the student to find out information about the class [FilenameFilter](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/io/FilenameFilter.html)


### 4.1. Solved Exercise

Analyze and explain what this little program do:

```java
public static void main(String[] args) {
        String ruta = args[0];
        File f = new File(ruta);

        if (f.exists()) {
            if (f.isFile()) {
                System.out.println("El tamaño es de " + f.length());
                System.out.println("Puede ejecturase: " + f.canExecute());
                System.out.println("Puede leerse: " + f.canRead());
                System.out.println("Puede escribirse: " + f.canWrite());
            } else {
                String[] losArchivos = f.list();
                System.out.println("El directorio " + ruta + " contiene:");
                for (String archivo : losArchivos) {
                    System.out.println("\t" + archivo);
                }
            }

        } else {
            System.out.println("El fichero o ruta no existe");
        }
}
```

**Solution**

1. This program read from the command line a path, and create a File object.
2. Then, check if the path exist or no over the file system. In case it doesn't exist, the program finish.
3. If the path exists, next test is to verify if it is a file:
   1. We read some trivial properties like size and permits 
4. If the path pointed by the file is a directory, we get the content of this directory, and show on the screen

<!-- 
Buscar y reemplaçar $\rightarrow$ per  → (simbol del sistema)
Canviar els awesomebox per !!! a l'inici, llevar els de darrere i tabular els continguts. Podem donar-li titol
-->