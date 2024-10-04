# Connecting to databases

## MySQL JDBC controller

In order to create Java programs using MySQL driver, we need to load in our program and previously in our development environment. As we will use `Gradle` let's go how to use it:

1. We need to find our server JDBC version, and we can find it in maven central repository <https://mvnrepository.com>.
2. We will search for `mysql jdbc`, and it will get as first result [MySQL Connector/J](https://mvnrepository.com/artifact/mysql/mysql-connector-java).
3. Be notice to select the correct version, and then select the package manager (gradle, maven, etc.) and you will get:
   
```bash
// https://mvnrepository.com/artifact/mysql/mysql-connector-java
implementation group: 'mysql', name: 'mysql-connector-java', version: '8.0.30'
```

4. When you save or build your project, you will be able to import your drivers properly

\begin{center}
\begin{minipage}{0.9\textwidth}
\includegraphics[width=0.9\columnwidth]{./img/mysql-gradle.png}
\end{minipage}
\end{center}

## Connection URL

Once you have imported your driver, you need to tell your Java program how to connect to your database and finally connect to it. For this task we should create a `Connection` using a special URL called _connection URL_. As others URL, a connection URL may contain:

- **Protocol** $\rightarrow$ we will use `jdbc`.
- **DBMS** $\rightarrow$ we could use `mysql`, `postgres`, `sqlite`, `sqlserver` or any DBMS with a JDBC connector.
- **Server address** $\rightarrow$ we could use either a full name or the server IP.
- [optional]**Port** $\rightarrow$ the port where the server is listening. If none is used, the driver will try to connect to the default one. Remember to separate port from address with `:`.
- **Other parameters**, separating it with `?` at the beginning and with `&` between parameters. For exemple:
  - **user**=`username`
  - **pass**=`password`
  - **useUnicode**=`true`
  - **characterEncoding**=`UTF-8`

A sample connection URL can be:

```java
String connectionUrl = "jdbc:mysql://localhost:3308/Cycling?useUnicode=true&characterEncoding=UTF-8&user=root&password=root";
```

A better option will be:

```java
String server="localhost";
int port=3308;
String user="root";
String pass="root";
String DBName="Cycling";
String connectionUrl = "jdbc:mysql://"+server+":"+port;
connectionUrl+="/" + DBName;
connectionUrl+="?useUnicode=true&characterEncoding=UTF-8";
connectionUrl+="&user="+user;
connectionUrl+="&password="+pass;
```

:::warning
Notice that both samples are **hard-coded** code, because you have values of the server (user and password) written inside Strings. Is a better option to store this values inside variables or in properties files.
:::

## `Connection` class

In Java the class needed to manage the driver is `java.sql.DriverManager`. It tries to load the drivers from the system when reading the JDBC drivers property, but we can indicate that it is loaded using the instruction:

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

The class that will centralize all operations with the database is `java.sql.Connection`, and we must obtain it from the `DriverManager` with any of the 3 static methods it has:

- `static Connection getConnection(String url)` $\rightarrow$ Returns a connection, if it is possible, to the database whose parameters are specified in the connection URL. Remember from last part how to create a connection URL.
- `static Connection getConnection(String url, Properties info)` $\rightarrow$ Returns a connection, if it is possible, to the database, with some parameters specified in the URL and others in a properties object (`Properties` class studied in unit 1). We will see samples later.
- `static Connection getConnection(String url, String user, String pass)` $\rightarrow$ Returns a connection, if it is possible, to the database whose parameters are specified in the URL. User and password data are provided in two additional parameters, so you do not need to write in your connection URL.

A first sample will be:

```java
public static void main (String [] args ) 
  throws ClassNotFoundException, SQLException{

    Class.forName("com.mysql.cj.jdbc.Driver");
    String server="localhost";
    int port=3308;
    String user="root";
    String pass="root";
    String DBName="Cycling";
    String connectionUrl = "jdbc:mysql://"+server+":"+port;
    connectionUrl+="/" + DBName;
    connectionUrl+="?useUnicode=true&characterEncoding=UTF-8";

   Connection conn = DriverManager.getConnection(connectionUrl,user,pass);

   // if no exception is catched, you are connected to your DBMS
```

With the `Connection` object that we have now connected, we will send our queries and ask for information, as we will see later.

## Organizing our connection

Our application is going to connect to one (or more) databases. We can make many requests to said database, and if we are implementing a multithreaded application, the number of requests can increase a lot. That is why we have to control **where** and **when** connections are created and closed. A good idea is to create a class that encapsulates all these processes. The skeleton of such a class would be as follows:

```java
public class ConnexioBD {

    private Connection laConnexio = null;

    // write here access variables, like user, server or whatever

    private void connect() {
        // do the connetion (look the sample). 
        // Take care. private method. It will be called inside the class
    }

    // close connection, if it's opened
    public void disConnect() {
        if (laConnexio != null) {
            laConnexio.close();
        }
    }

    // returns the connection. 
    // It will be create first time of after closed
    public Connection getConexio(){
        if (laConnexio == null) {
            this.connect();
        }
        return this.laConnexio;
    } 
}
```
### Solved exercise

Create a single project with two classes. One for connecting to your database, as the last sample, and a main class that connect and show if an error have happened.

> This exercise is solved in package `Utils`, file `ConnexioDB`.