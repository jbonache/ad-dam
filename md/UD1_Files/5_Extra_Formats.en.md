
# 1. Extra formats


In this section we will study two type of text files that are used widely in computer science, and the way to work with it.

## 1.1. CSV files

A **comma-separated values** (CSV) file is a standard text file which uses a comma (`,`) to separate value. Each line of the file consists of one or more fields, separated by commas. Each field may or may not be enclosed in double-quotes. In addition, several formats use different characters as separator, as semicolon (`;`) or hash (`#`). The [RFC 4180](https://tools.ietf.org/html/rfc4180) defines the format or definitions of a CSV file or text/csv file.

In case that the symbol that is used as separator appears inside the values, enclose the content between quotation marks it is a good idea.

A sample of csv can be viewed here:

```csv
Chevrolet Chevelle Concours (sw);0;8;350.0;165.0;4142.;11.5;70;US
Ford Torino (sw);0;8;351.0;153.0;4034.;11.0;70;US
Plymouth Satellite (sw);0;8;383.0;175.0;4166.;10.5;70;US
AMC Rebel SST (sw);0;8;360.0;175.0;3850.;11.0;70;US
Dodge Challenger SE;15.0;8;383.0;170.0;3563.;10.0;70;US
Plymouth Cuda 340;14.0;8;340.0;160.0;3609.;8.0;70;US
Ford Mustang Boss 302;0;8;302.0;140.0;3353.;8.0;70;US
```

The way to process a csv in Java is:

1. Open as a text file for reading. We need to read line by line, because every line is a register. Read one line into a String variable.
2. Process individual records:
   1. We could separate every field. A good option is use `split(char)` method from string. We will obtain an array of string with the individual fields values.
   2. Process each field value by your own

!!! note "Attention" 

    It is a good idea to start using `Files` and `Paths` **abstract classes**. These classes improve the use of File and it inherited classes, offering useful methods that allow us to do quick operations with less code lines. For example, the following line, starting from the `filename` open it and then read the whole file, returning a `List` with lines separated on each item of the collection.

    ```java
    List<String> lines=Files.readAllLines(Paths.get(filename));
    ```

    More info in:

    - [Files](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/nio/file/Files.html)
    - [Paths](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/nio/file/Paths.html)


## 1.2. Properties files

### 1.2.1. Properties file

At last, but not least, we are going to show you an important kind of text file, who is properties files. This files stores, as you know, several properties that are used during program execution. The program, when starting, load these properties and do a tuning of several option.  Example of these files are `my.conf` in MySQL, `php.ini` in PHP, etc.

The aspect of this file is several lines (one by property) and every line `attribute=value`. For example, a supposed file:

```conf
# properties of my program
port=1234
volume=90
bright=56
load_on_start=true
```

The way to process a properties file in Java is similar to a CSV:

1. Open as a text file for reading. We need to read line by line, because every line is a different property. Read one line into a String variable.
2. Process individual records:
   1. We could separate every field. A good option is use `split(char)` method from string. We will obtain an array of string with the individual fields values. The separators are normally `=`, `:`.
   2. Left of the separator is the property name
   3. Right of the separator is the property's value
   4. Notice that if a line starts with slash (`#`) should be a comment, and will be ignored

### 1.2.2. Java Properties object

Java has an object very useful to manage this kind of information. With [properties](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Properties.html), we can store a set of properties with a hash table (basically a pair key-value). It has methods prepared to load and save from and to streams of text or, moreover XML files. Interesting method for properties are:

**Reading from file (text or XML)**

- `void load(InputStream inStream)` → Reads a property list (key and element pairs) from the input byte stream.
- `void load(Reader reader)` → Reads a property list (key and element pairs) from the input character stream in a simple line-oriented format.
- `void loadFromXML(InputStream in)` → Loads all of the properties represented by the XML document on the specified input stream into this properties table.

**Writing to file (text or XML)**

- `void store(OutputStream out, String comments)` → Writes this property list (key and element pairs) in this Properties table to the output stream in a format suitable for loading into a Properties table using the load(InputStream) method.
- `void store(Writer writer, String comments)` → Writes this property list (key and element pairs) in this Properties table to the output character stream in a format suitable for using the load(Reader) method.
- `void	storeToXML(OutputStream os, String comment)` → Emits an XML document representing all the properties contained in this table.

**Working with properties (inherited from HashTable)**

- `Set<K> keySet()`	→ Returns a Set view of the keys contained in this map.
- `V get(Object key)` →` Returns the value (`V`)to which the specified `key` is mapped, or `null` if this map contains no mapping for the key.
- `boolean	containsKey(Object key)` Tests if the specified object is a key in this hashtable. 
- `V put(K key, V value)` → Maps the specified key to the specified value in this hashtable.
- `V remove(Object key)` → Removes the key (and its corresponding value is returned) from this hashtable.

### 1.2.3. Sample program

In the next program you can view sample of reading and creating properties files in Java

```java

    /**
     * Load the file specified and show its properties in different ways
     * @param filename 
     */
    private void loadAndShowProperties(String filename) {

        Properties properties = new Properties();

        try {
            properties.load(new FileInputStream(new File(filename)));

            System.out.println("Whole set: " + properties);

            properties.list(System.out);

            Set<Object> keys = properties.keySet( );

            System.out.println("My listing: ");
            for (Object key : keys) {
                System.out.println(key + " - " + properties.getProperty((String) key));
            }

        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    }

    /**
     * Create a properties object, populated with samples and stores into a 
     * text file and a XML file
     * @throws IOException 
     */
    private void writeProperties() throws IOException {
        Properties props = new Properties();

        props.put("Color", "Green");
        props.put("Range", "123");
        props.put("Visible", "false");
        props.put("Size", "Big");
        props.put("Status", "functional");
        props.put("Value", "345.24");

        props.store(new FileWriter(new File("propis.properties")), "Sample props file");

        props.storeToXML(new FileOutputStream(new File("propis.xml")), "Sample XML Props");
    }
```



## 1.3. Environment, `.env` files and `dot_env`

### 1.3.1. Why using environment?

Environment variables are a crucial aspect of configuring and managing applications across different environments, such as **development, testing, and production**. They provide a way to dynamically pass configuration data to applications without hardcoding values directly in the source code. This approach enhances the **flexibility**, **portability**, and **security** of applications.

In Java, accessing environment variables is straightforward, thanks to the `System` class, which provides methods to retrieve environment variables. This capability is especially useful for:

- Configuring application settings like database URLs, API keys, and file paths.
- Managing environment-specific configurations without altering the codebase.
- Keeping sensitive information, such as passwords and tokens, out of the source code.
  
By understanding how to effectively utilize environment variables in Java, developers can create more adaptable and secure applications. 

### 1.3.2. Getting environment variables

To access environment variables in Java, you can use the `System.getenv()` method. This method is overloaded, and coud be:

- `System.getenv()` → returns a `Map` object containing all the environment variables and their corresponding values.
- `System.getenv(String)` → returns a `String` with the value of the specific environment variable or null if it does not exists.

!!! example "How to retrieve environment variables in Java:"

    ```java
    Map<String, String> env = System.getenv();

    // Access a specific environment variable
    String value = env.get("VARIABLE_NAME");

    // Print all environment variables
    for (Map.Entry<String, String> entry : env.entrySet()) {
        System.out.println(entry.getKey() + " = " + entry.getValue());
    }
    ```

    > Notice that:
    > 
    > - The Map class returns a collection of entries with pairs key-value
    > - The environment variables are case-sensitive, so make sure to use the correct casing when accessing them.


### 1.3.3. The `dot-env` approach

Storing configuration in the environment is one of the tenets of a [twelve-factor app](https://12factor.net/config). Anything that is likely to change between deployment environments–such as resource handles for databases or credentials for external services–should be extracted from the code into environment variables.

But it is not always practical to set environment variables on development machines or continuous integration servers where multiple projects are run. Dotenv load variables from a .env file into ENV when the environment is bootstrapped.

Unfortunately, loading `.env` files in Java is not natively supported by the language itself, but you can use external libraries to accomplish this task. One of the popular libraries for this purpose is `dotenv-java`. This library allows you to easily load environment variables from a .env file into your Java application. <https://github.com/cdimascio/dotenv-java>

**Add the Dependency**

First, you need to add the dotenv-java library to your project. If you are using Maven, add the following dependency to your pom.xml file:

```xml
<dependency>
    <groupId>io.github.cdimascio</groupId>
    <artifactId>java-dotenv</artifactId>
    <version>5.2.2</version>
</dependency>
```

If you are using Gradle, add the following to your build.gradle file:

```
implementation 'io.github.cdimascio:java-dotenv:5.2.2'
```


**Create `.env` file**

The `.env` file is like config files. You can store it on the root path of your application

```conf
DATABASE_URL=jdbc:mysql://localhost:3306/testdb
DATABASE_USER=root
DATABASE_PASSWORD=password
```

**Load the configuration**

```java
import io.github.cdimascio.dotenv.Dotenv;

public class Main {
    public static void main(String[] args) {
        // Load the .env file
        Dotenv dotenv = Dotenv.load();

        // Retrieve environment variables
        String databaseUrl = dotenv.get("DATABASE_URL");
        String databaseUser = dotenv.get("DATABASE_USER");
        String databasePassword = dotenv.get("DATABASE_PASSWORD");

        // Print the values
        System.out.println("Database URL: " + databaseUrl);
        System.out.println("Database User: " + databaseUser);
        System.out.println("Database Password: " + databasePassword);

        // Use the variables as needed
        // For example, establish a database connection using these variables
    }
}

```

!!! info "Attention"
    Notice that with java dotenv library, you will use `dotenv.get()` instead of `System.getenv()` method, due to you are accessing local file.

     
