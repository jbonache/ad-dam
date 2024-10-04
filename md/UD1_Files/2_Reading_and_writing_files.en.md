# 1. Reading and writing files

Java offers a wide variety of ways to manipulate the contents of files, bringing great power but complexity at the same time.

The reading and writing of files in Java is done through data flows or `streams`, which may be either oriented to bytes or considered as a set of characters. The concept of file, which is static, must be differentiated from the concept of stream, which has a dynamic character: a file is stored on disk, but from the point of view of the application, we are interested in the transfer of this information from the file to our program. A classic comparison: streams would be like water pipes, while files would be reservoirs. The concept of **data flow**, apart from the scope of files, will also be applicable to the transfer of information, for example through the network or between processes.

Streams can be classified into:

- `Input Streams`: Those that go from a source (e.g. file) to the program.
- `Output Streams`: Those that exit the program to a destination (e.g. file).
  
When reading and storing data, we must be very careful with the types of data we work with, since the same sequence of bits on disk represents different information depending on the type of data used. Even when dealing with the same type of data, as in the case of characters, special care must be taken, since different systems may use different coding. Java, for example for the char type uses 16-bit Unicode (UTF-16), but we can try to access encoded files, for example with UTF-8 or ASCII.

The parent superclasses for handling byte-oriented streams are `InputStream` and `OutputStream`. Others are derived from these, but for file management we are interested in two: `FileInputStream` and `FileOutputStream`.


## 1.1. FileInputStream

The `FileInputStream` class is used to access files for reading, and has two main constructors:

- `FileInputStream (File f)` → Receives an object of type `File`, which will reference the object we will read from.
- `FileInputStream (String name)` → Receives a string with the name or path of the location of the file to be read.

The main methods we have for this class are:

- `int read()` → Abstract sequential reading method: Returns an integer corresponding to the next byte of an input stream (file, byte array...). If it reaches the end of the sequence, it returns `-1`. On error, it will throw an exception of type `IOException`.
- `int read(byte[] buffer)` → Reads a given number of bytes from the input (as long as the buffer), saving them to the buffer. It will return the number of bytes effectively read, which at most will be as large as the buffer. If no bytes are available return `-1`.
- `int available()` → Indicates the bytes we have available for reading.
- `long skip(long des)` → Skips as many bytes as the parameter indicates. The return value is the number of bytes that have been effectively dumped (they can be less than what we have indicated if we reach the end for example).
- `int close()` → Closes the data stream.

## 1.2. FileOutputStream

The FileOutputStream class is used to access files for writing. It has the following builders:

- `FileOutputStream (File f)` → Receives an object of type `File` and opens it in write mode. In case it does not exist, it will be created, and if it exists, it will be overwritten.
- `FileOutputStream (String name)` → Receives a string with the name or path of the location of the file to be written, and opens it in write mode. In case it does not exist, it will be created, and if it exists, it will be overwritten.
- `FileOutputStream (File f, boolean append)` → Gets an object of type `File` and opens it in append mode, to write to the end. If it doesn't exist, it will be created, and if it exists, it will write behind it (only if append is set to True).
- `FileOutputStream (String , boolean append)` → Receives a string with the name or path of the file location to be written and opens it in append mode, to be written at the end. If it doesn't exist, it will be created, and if it exists, it will write behind it (only if append is set to True).

The main methods of the class are:

- `write(int byte)` → Writes the byte to the output stream. Although this is an integer parameter, only one byte is written. If there is an error, an IOException is thrown.
- `void write(byte[ ] buffer)` → Writes the contents of the buffer (byte vector) to the file. If the buffer is null, it will throw an exception.
- `void write(byte[ ] buffer, int pos, int length)` → Writes the contents of the buffer (byte vector) from position pos, and as many bytes as given in length.
- `void flush()` → Forces the remaining bytes in the write cache to be written to the output file.
- `void close()` →Closes the output stream and frees the resources.

### 1.2.1. Solved Exercise

Create a simple Java program that copies a file to another file byte to byte. Source and destination file will be indicated on the command line.

!!! example "Solution"

    ```java
    class FileCopy {
        /*
        Class to test FileInputStream and FileOutputStream. 

        Copy byte to byte of files

        Sintaxi:
            FileCopy sourceFile destinationFile.

        */
        public static void main(String[] args) throws Exception {
            // Byte readed from source
            int bytes;        
            // Bytes (effectively) writen to dest
            long bytesCopied=0; 
            
            // Streams 

            FileInputStream fis= null;
            FileOutputStream fos=null;
            
            // To provide information about source
            File f; 


            // Are the arguments ok?
            if(args.length!=2){
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n FileCopy fitxerOrigen fitxerDesti");
                return;
            }

            try{

                // show source size
                f=new File(args[0]);
                System.out.println("Total: "+f.length()+" bytes");

                // Create streams          
                fis=new FileInputStream(args[0]);
                fos=new FileOutputStream(args[1]);

                do {
                    // read one byte from source
                    bytes=fis.read();                 
                    // write in destination
                    if (bytes!=-1)
                        fos.write(bytes);
                    // Update number of bytes
                    bytesCopied++;                    

                    // Show progress (think alternatives as exercise)
                    System.out.print("\rCopiats "+(bytesCopied-1)+" bytes...");
                }while (bytes!=-1);
                System.out.println("Done it!");


            }catch (IOException exc){
                System.out.println("Error d'entrada i eixida: "+exc);
            }finally {
                // At the end, we have to close the files, either an error exists or not.
                try {
                    if (fis!=null) fis.close();
                }catch (IOException exc){
                    System.out.println("Error en tancar el fitxer d'origen.");
                }
                try {
                    if(fos!=null) fos.close();
                }catch (IOException exc){
                    System.out.println("Error en tancar el fitxer destí.");
                }
            }
        }
    }
    ```

!!! note "Think and try"
    With the sample program we have seen, we could copy files of every kind: text, audio, video. Try it as your own and test the content is the same.

    **Challenge**

    Try to change the last program in order to read data in 32 bytes chunks, using the accurate methods of the classes. Share your solution in the forum.


## 1.3. Text Files

As stated, Java allows streams to be managed with either a byte orientation or a character orientation. The abstract classes for managing character-oriented streams are `Reader` and `Writer`. These are derived from others, but for the management of files we focus on `FileReader` and `FileWriter`.

### 1.3.1. `FileReader`

The FileReader class serves to access files for reading, and has two constructors:

- `FileReader (File f)` → Receives an object of type `File`, which will reference the object from which it is to be obtained.
- `FileReader (String name)` → Receives a String with the name or path to the file.
  
The methods we have for this class are pretty much the same as we have for `FileInputStream`, with the difference that we now read characters instead of bytes. One aspect to consider is that these characters will be represented in the format of the host operating system (UTF-8 in the case of Linux), unlike the UTF-16 used by other internal storage classes:

- `int read()` → Reads the next character from the input stream and returns it as an integer. If there are no characters left, it returns -1, and if there is an error, it throws an exception of type IOException.
- `int read(char[] buffer)` → Fills the buffer with as many characters from the input as it has (at most). It returns the number of characters effectively read. This number will be the length of the buffer or less if there are not enough characters to read. If none, return -1.
- `int available()` → Returns the number of characters available to read.
- `long skip(long des)` → Skips as many characters as the parameter indicates. The return value is the number of characters that have been effectively thrown (they can be less than what we have indicated if we reach the end for example)
- `int close()` → Closes the data stream

### 1.3.2. `FileWriter`

The FileWriter class would be the equivalent of `FileOutputStream` in character-oriented streams version. The class constructors are:

- `FileWriter (File f)` → Opens the file specified by `File` for writing. If the file does not exist, it will be created, and if it exists, it will delete the contents.
- `FileWriter (String name)` → Opens the file specified by a string with name and path in writing mode. If it does not exist, it will be created, and if it exists, it will delete the content
- `FileWriter (File f, boolean append)` → Receives an object of type File and opens it in append mode, to write at the end. If it doesn't exist, it will be created, and if it exists, it will write behind it.
- `FileWriter (String name_f, boolean append)` → Receives a string with the name or path of the location of the file to be written and opens it in append mode, to write at the end. If it doesn't exist, it will be created, and if it exists, it will write behind it.

The main methods of the FileWriter class are very similar to those of `OutputStream`:

- `write(int character)` → Writes the character to the output stream, with the operating system's own encoding. If there is an error, an IOException is thrown.
- `void write(char[] buffer)` → Writes the contents of the buffer (character vector) to the file. If the buffer is null, it will throw an exception.
- `void write(char[] buffer, int pos, int length)` → Writes the contents of the buffer (character array) from position pos, and as many bom bytes are given in length.
- `void flush()` → Forces the remaining bytes in the write cache to be written to the output file.
- `void close()` → Closes the output stream and frees the resources.
- `void write(String text)` → Writes the entire text content to the file.

### 1.3.3. Solved Exercise

Create a simple Java program that merge all the files inside a folder in a unique file. The source folder and destination file will be indicated in program's call. We suppose all the files inside that folder are text files.

!!! example "Solution"

    ```java
    class MergeTexts {
        /*


        Sintax:
            MergeTexts DirectoriOrigen FitxerDestí

        */
        public static void main(String[] args) throws Exception {

            File dir; // Source dir
            // Collection of files from that dir
            File[] files; 

            // readed characters
            int characters;

            // Input and Output Streams 
            FileReader fin=null;
            FileWriter fout=null;

            // Check the args
            if(args.length!=2){
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n mergeTexts DirectoriOrigen fitxerDesti");
                return;
            }

            try{

                // We get the list of Files
                dir=new File(args[0]);
                files=dir.listFiles();


                // Open and close output stream (in order to create the file)
                fout=new FileWriter(args[1]);
                fout.close();
                
                // Re-open it
                fout=new FileWriter(args[1], true);

                // Iterate among the list
                for (int i=0; i<files.length; i++){
                    // open input stream
                    fin=new FileReader(args[0]+"/"+files[i].getName());
                    System.out.println("Merging "+args[0]+"/"+files[i].getName());
                    // and merge to the output one
                    do {
                        characters=fin.read();                 
                        if (characters!=-1)
                            fout.write(characters);
                    }while (characters!=-1);
                    fin.close(); //close the file merged

                }
                fout.close(); //close the output file

            }catch (Exception exc){
                // Catch all the exception (we coud improve it)
                System.out.println("Input/Output error: "+exc);
            }
        }
    }
    ```

!!!note "Improve your code"
    Try to improve the last code creating a `merge()` function. We should call it inside the main loop


## 1.4. Decorators

[Decorator Design Pattern](https://refactoring.guru/es/design-patterns/decorator/)

Decorator classes are those that inherit from a certain class, and they provide added functionality to the original. In the case of input and output streams, we have decorators that allow us to read or write complete lines, instead of byte by byte, or save certain data format. It simplifies our work, adding a more natural and human-friendly way to use the base classes.

The `InputStream` class has several decorators, but we'll stick with the following:

- `DataInputStream` → Allows reading data of any type (integer, logical, etc.)
- `ObjectInputStream` → Add the option to reads an entire object


The `OutputStream` class, on the other hand, also has different decorators, among which we highlight:

- `DataOutputStream` → Allows writing data of any type (integer, logical, etc.)
- `PrintStream` → Allows writing data of any type, and also accepts `printf` and `println` methods
- `ObjectOutputStream` → Allows writing (serializing) objects

Regarding the decorator classes for character-oriented streams, we have, on the one hand, the (most prominent) `Reader` decorators:

- `BufferedReader` → Creates an input buffer, allowing for example to read a complete line

And for writer:

- `BufferedWriter` → Creates an output buffer, allowing for example to write a full line
- `PrintWriter` Allows the writing of data of different types, and has methods such as printf and println

The `BufferedReader` class, among others, has the `readLine()` method, which allows reading an entire line of the file up to the end of the line, very useful in text files.

On their behalf, the BufferedWriter class provides the `newLine()` method to enter the carriage return character, and the `write(String chain, int start, int length)` method to write a string or a specific part.

The other decorator for writing is the `PrintWriter`, which offers us the methods `print(data)`, `println(data)` and `printf()`, for formatting.

!!! note "Alternatives to..."
    Obviously, you could :

    - concatenate `\n` at the end of every String on every `print()` method instead calling ` newLine()`.
    - use `println()` instead calling ` newLine()`.


### 1.4.1. Solved Exercise

Create a program to copy a text file adding the number lines at the beginning of each line.

!!! example "Solution"
    ```java
    public class NumberLines {
        public static void main(String[] args) throws Exception {

            // Input and Output
            BufferedReader fin;
            PrintWriter fout;

            // line counter
            int num_linia;
            // readed line
            String linia;

            // check args
            if (args.length != 2) {
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n numberLines fitxer eixida");
                return;
            }

            // Creare decorators
            fin = new BufferedReader(new FileReader(args[0]));
            fout = new PrintWriter(new FileWriter(args[1]));

            num_linia = 1;
            do {
                // Read the line
                linia = fin.readLine();
                if (linia != null) {
                    fout.println(num_linia + ". " + linia);
                }
                num_linia++;
            } while (linia != null); // until we can't read

            // close all
            fin.close();
            fout.close();

        }
    }
    ```
## 1.5. Binary Files

In this section and the following we will see how to storage different types of data and objects in files of different formats: binary files, XML or JSON files. Now, we will focus on binary files, both for storing structured data on the one hand, and objects, on the other, a process known as serialization.

### 1.5.1. Storing structured data in binary files

In the previous section we saw how to work with character and byte files. In the case of byte files, we have seen how to read and write them sequentially, byte by byte to the end of the file. Let's now see how to store structured data in binary files and how to be able to read them.

Let's imagine that we want to save the following table, which combines data of different types. This sample will appear in next sections.

| Module | Hours | Qualification|
|---|-|---|
|Accés a Dades	| 6	|8.45 |
|Programació de serveis i processos	|3	|9.0|
|Desenvolupament d'interfícies	|6	|8.0|
|Programació Multimèdia i dispositius mòbils	|5	|7.34|
|Sistemes de Gestió Empresarial	|5	|8.2|
|Empresa i iniciativa emprenedora	|3	|7.4|

As we can see, we have text data, integer and floating point numeric data. If we want to maintain the types, character-oriented streams like `Reader` or `Writer` will not be useful, so we should use `InputStream` and `OutputStream`. The disadvantage is that it would be necessary to know exactly how many bytes each type of data occupies.

In order to efficiently save these structures, we can make use of the `DataInputStream` and `DataOutputStream` classes, which are stream decorators and which offer us the following methods to save or retrieve data of different types, without worrying about what they occupy internally each. As you can see, there are reciprocal methods to read and write every base types.

| `DataInputStream`	| `DataOutputStream`	| Description|
|---|---|---|
|`byte readByte()`|`void writeByte(int)` |a byte	|
|`short readShort()`	|`void writeShort(short)`	|short int |
|`int readInt()`	|`void writeInt(int)`	|an int|
|`long readLong()`	|`void writeLong(long)`	|a long int|
|`float readFloat()`	|`void writeFloat(float)`	| single precision|
|`double readDouble()` |`void writeDouble(double)`	|double precision|
|`char readChar()`	|`void writeChar(int)`	|a Unicode char|
|`String readUTF()`	|`void writeUTF(String)`|	a UTF-8 string|

!!!warning
    A UTF-8 string is different of a single String. When a String is written, as is evident, the whole characters are storage. Saving it as UTF-8 string, add information about the string's length, and this information is essential to cut this strings when you will read in a future.

    Imagine you save two strings, "euro" and "sport". The result will be, at the end "eurosport". When somebody open this file in the future, how do he or she know the number of string stored ("euro", "sport" or simply "eurosport" (TV channel))

    Saving as UTF-8, when you save "euro" and "sport", the result is "4euro5sport". When somebody try to read it, first of all see a '4', and read "euro". Then, see a '5', and then read "sport". Notice that if "eurosport" is stored, the result is "9eurosport". Try to write a sample program with this string, opening the resulting file with amn hexadecimal editor.


### 1.5.2. Solved exercise

Write a program who has data stored in three parallel arrays, storing modules data of DAM. Write two functions, one for write this data on a file and another to read it. Consider to storage data group by module, instead name, hours and grade.

!!! example "Solució"

    ```java
    public class Moduls{

        // several arrays with modules data
        String[] moduls={"Accés a Dades", "Programació de serveis i processos", "Desenvolupament d'interfícies", "Programació Multimèdia i dispositiud mòbils", "Sistemes de Gestió Empresarial", "Empresa i iniciativa emprenedora"};
        int[]  hores={6, 3, 6, 5, 5, 3};
        double[] notes={8.45, 9.0, 8.0, 7.34, 8.2, 7.4};

        public void readFiLe(String name) throws IOException {
            // Per lleginr el fitxer binari, creem un DataInputStream
            // a partir del FileInputStream creat a partir del nom
            DataInputStream f = new DataInputStream(new FileInputStream(name));

            // Mentre el DataInputStream tinga dades disponibles
            while (f.available()>0){
                // Llegirem del fitxer cada dada, amb l'ordre corresponent
                // en funció del tipus
                // (per tant, hem de saber l'ordre en què guardem!)
                System.out.println("Mòdul: " + f.readUTF());
                System.out.println("Hores: " + f.readInt());
                System.out.println("Notes: " + f.readDouble());
                System.out.println();
            }
            f.close();
        }

        public void writeFile(String name) throws IOException{
            // Per escriure el fitxer, fem ús de DataOutputStream
            DataOutputStream f = new DataOutputStream(new FileOutputStream(name));

            // Recorrerem qualsevol dels vectors (tots haurien de tindre)
            // la mateixa longitud
            for (int i=0;i<this.moduls.length;i++){
                // I per a cada posició, escriurem en funció del tipus de dada
                f.writeUTF(this.moduls[i]);
                f.writeInt(this.hores[i]);
                f.writeDouble(this.notes[i]);

            }
            f.close();
        }

        public static void main(String[] args) throws IOException {

            // Comprovem els arguments
            if (args.length!=2){
                System.out.println("Nombre d'arguments incorrecte.\n\nSintaxi: \n\t java Moduls [read | write] fitxer.dat");
                System.exit(0);
            }

            // Defining the class
            Moduls moduls=new Moduls();

            // Depending the args, we will proceed
            if (args[0].equals("read")) 
            moduls.readFiLe(args[1]);
            else if (args[0].equals("write")) 
            moduls.writeFile(args[1]);
            else 
            System.out.println("No entenc l'ordre "+args[0]+"\n");
        }
    }

    ```

## 1.6. Object serialization

Java provides a generic object serialization system: a recursive system that iterates over each object contained in an instance, until it reaches the primitive types, which it stores as an array of bytes. Apart from this information of the primitive types, additional information is also stored, or metadata specific to each class, such as the name or attributes among others. Thanks to this metadata, which describes the objects we save, we can automate serialization in a generic way, ensuring that we can read the objects later.

The disadvantage of this method is that when we change the definition of the class (for example adding one more attribute, or changing its type), the metadata is modified, and we would not be able to read serialized objects with previous versions of the class In addition, it is also necessary to take into account that this is a specific mechanism of Java, and that we will not be able to consult these objects from other languages.

For all of this, other techniques are preferable for the permanent storage of objects, which we will see later, but serialization can be useful for temporary storage, within the same execution of the application.

!!! note "What are this?"
        **Research:** try to find information about ***SerialVersionUID***, and what it use is important for.


### 1.6.1. The `Serializable` interface and `Decorators`

If we want a class to be serializable, it must implement the Serializable interface, the purpose of which is simply to act as a marker to indicate to the JVM that the class can be serialized, so this class will have no methods.

It should be said that all the classes corresponding to the basic types already implement the Serializable interface, as well as the String class, containers and Arrays. In the case of collections, it will depend on its contents, if its elements are serializable, the collection will be as well. If the object we want to serialize or those of any of its objects do not implement the Serializable interface, the exception `NotSerializableException` is thrown.

The `ObjectInputStream` and `ObjectOutputStream` decorators offer us the ability to serialize any de-serializable object. In order to write an object, we will make use of the `writeObject` method of `ObjectOutputStream`, and to read it we will make use of `readObject`, of `ObjectInputStream`. 

!!!note "Sheeps with sheeps"
    Note that reading objects must be done on instances of the same class that was saved. Otherwise, a `ClassCastException` is thrown. Also, you must have the compiled code of the class, to avoid the `ClassNotFoundException` exception. 

    In addition, `readObject` return an Object, and we need an object of a specific class. For this reason you have to cast from Object to the needed class. Inheritance concepts are very important to guarantee robust programs. 


### 1.6.2. Solved exercise

Starting with the same base of the `Moduls` class in the previous exercise, we are going to create a `Modul` class, to store a single module. This kind of classes are called POJO's (Plain Old Java Objects), and are designed only to storage information. (It will appear later, togethre with BEAN's classes).

Once the `Modul` class is created, write a program to save on a file objects directly. Later, write the complementary function to read all the stored objects from that file. 


!!! example "Solution"

    ```java
    /**
        Class to storage a single module in memory
    */

    class Modul implements Serializable{
        String nom;
        int hores;
        double nota;

        public Modul(){
            // Constructor buit
        }

        public Modul(String nom, int hores, double nota){
            this.nom=nom;
            this.hores=hores;
            this.nota=nota;
        }

        public String getModul() {return this.nom;}
        public int getHores() {return this.hores;}
        public double getNota() {return this.nota;}
    } 

    /**
        Write and Read modules to/from file
    */

    public class Moduls2 {

        // Arrays with source data
        String[] moduls = {"Accés a Dades", "Programació de serveis i processos", "Desenvolupament d'interfícies", "Programació Multimèdia i dispositiud mòbils", "Sistemes de Gestió Empresarial", "Empresa i iniciativa emprenedora"};
        int[] hores = {6, 3, 6, 5, 5, 3};
        double[] notes = {8.45, 9.0, 8.0, 7.34, 8.2, 7.4};

        public void EscriuObjecte(String nom) throws IOException {

            //destination file
            ObjectOutputStream f = new ObjectOutputStream(new FileOutputStream(nom));

            Modul m; // Single object

            // loop along the arrays
            for (int i = 0; i < this.moduls.length; i++) {
                m = new Modul(this.moduls[i], this.hores[i], this.notes[i]);
                f.writeObject(m);
            }

            // close the file
            f.close();

        }

        public void LligObjecte(String nom) throws IOException, ClassNotFoundException {

            // input file
            ObjectInputStream f = new ObjectInputStream(new FileInputStream(nom));

            Modul m;
            // we don't know how many objects exists in the file.
            try {
                while (true) { // forever

                    m = (Modul) f.readObject();

                    // show the module
                    System.out.println("Modul: " + m.getModul());
                    System.out.println("Hores: " + m.getHores());
                    System.out.println("Nota: " + m.getNota());
                    System.out.println();

                }
            } catch (EOFException ex) {
                f.close();
            }

        }

        public static void main(String[] args) throws IOException, ClassNotFoundException {

            // test the args
            if (args.length != 2) {
                System.out.println("Nombre d'arguments incorrecte.\n\nSintaxi: \n\t java Moduls2 [ read | write ] fitxer.obj");
                System.exit(0);
            }

            Moduls2 moduls = new Moduls2();

            // depending the args
            if (args[0].equals("read")) {
                moduls.LligObjecte(args[1]);
            } else if (args[0].equals("write")) {
                moduls.EscriuObjecte(args[1]);
            } else {
                System.out.println("No entenc l'ordre " + args[0] + "\n");
            }

        }

    }
    ```

!!! tip "Less work, but the same in the end"

    You probably will think about it: _If all in Java inherits from Object, an ArrayList is an Object...Can I save or load an entire ArrayList in one unique call?._ Try it as an improvement of the last exercise.


