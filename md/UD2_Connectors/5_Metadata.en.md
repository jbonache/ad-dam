# Database Metadata

Before retrieving data, we are going to study how to get information about the database we are connected. The metadata of a database describe the structure it has: tables of which it is composed the database, the fields that make up these tables, the types of these fields, etc. Even though we usually know this structure beforehand, it is possible that we need it on occasion of her for this we have the `DatabaseMetaData` and `ResultsetMetaData` interfaces.

`DatabaseMetaData` interface provides us with information about the tables and views of the database, as well as its structure. In the following table we have some of the most relevant methods of this interface.

- `String getDatabaseProductName()` $\rightarrow$ Gets the name of the DBMS.
- `String getDriverName()` $\rightarrow$ Gets the name of the JDBC driver in use.
- `String getURL()` $\rightarrow$ Gets the URL of the connection.
- `String getUserName()` $\rightarrow$ Gets the name of the user connected to the DB.
- `ResultSet getTables(String catalog, String schema, String patternTableName, String[] type)` $\rightarrow$ Get information from the tables available in the catalog indicated.
- `ResultSet getColumns(String catalog, String schema, String patternNameTable, String patternColumnName)` $\rightarrow$ Gets information from the columns of the table specified in the catalog and diagram indicated 
- `ResultSet getPrimaryKeys(String catalog, String schema, String patternNameTable)` $\rightarrow$ Gets the list of fields that make up the primary key.
- `ResultSet getImportedKeys(String catalog, String schema, String patternNameTable)` $\rightarrow$ Gets a list with the foreign keys defined in the table.
- `ResultSet getExportedKeys(String catalog, String schema, String patternNameTable)` $\rightarrow$ Gets a list with the foreign keys that point to this table
  
:::note
At this point, it is necessary to point out that the terms **catalog** and **scheme** tend to be confused. By standards, a catalog contains several schematics, with detailed system information, from the form of internal storage to the conceptual schemes. In a catalog, there seems to be one schema called **INFORMATION_SCHEMA**, with the views and domains of the information schema of the system.

In any case, most DBMS match the catalog with the database. Moreover, in this query we specify the database name as catalog, while if open MySQLWorkbench, the database is represented as a **schema**. We can find more information about it in these links:

- <https://stackoverflow.com/questions/7022755/whats-the-difference-between-a-catalog-and-a-schema-in-a-relational-database>
- <https://www.quora.com/What-is-the-difference-between-system-catalog-and-database-schemain-a-Database>
:::

## Solved Exercise

Let's go to create a Java program that shows internal information of a database `BDJocs`, through `DataBaseMetaData`. Let's view the program step by step.

:::tip
You can view all the information of the method and how the data is stored in each method's ResultSet in this [link](https://docs.oracle.com/javase/7/docs/api/java/sql/DatabaseMetaData.html).
:::

#### Create the connection

Remember how to connect to a DBMS in a easy way:

```java
// load JDBC driver
2 Class.forName("com.mysql.cj.jdbc.Driver");
3 // Connecto to DBMS and DB BDJosc, with user and pass
4 Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3308/BDJocs", "root", "root");
```

Very simple, load the driver and connect to DB, in the way we have studied

#### Retrieve metadata from DBMS, and show in a friendly format

We will use `Color` class to show data to paint text in console. You have this class in CEPA 1, inside the project.

```java
// get the metadata
DatabaseMetaData dbmd = con.getMetaData();

System.out.println(Colors.Blue+"\nDBMS information--------"+Colors.Reset);
System.out.println(Colors.Bright_White+"SGBD:\t"+Colors.Reset + dbmd.getDatabaseProductName());
System.out.println(Colors.Bright_White+"SGBD:\t"+Colors.Reset + dbmd.getDriverName());
System.out.println(Colors.Bright_White+"SGBD:\t"+Colors.Reset + dbmd.getURL());
System.out.println(Colors.Bright_White+"SGBD:\t"+Colors.Reset + dbmd.getUserName());
```

As you can see, we get the name of the DBMS, driver, URL a user we are using. Obviously, is the same that we put when we created Connection object, but it's a good example to show information.

#### Retrieve tables in a schema/database

Using `getTables()` method we could recover the tables and more information. We suppose `BDJocs` exists in our DBMS:

```java
System.out.println(Colors.Bright_White+String.format("%-15s %-15s %-15s","Database","Table","Type"));
System.out.println("-------------------------------------------------------"+Colors.Reset);
ResultSet rsmd = dbmd.getTables("BDJocs", null, null, null);
while (rsmd.next()) {
  System.out.println(String.format("%-15s %-15s %-15s",rsmd.getString(1),rsmd.getString(3),rsmd.getString(4)));
}
```

Comments:

- Revise `String.format()` to set a specific column length.
- From javadoc, we get than the ResultSet returned by `getTables` has the following columns:
  1. **TABLE_CAT** String => table catalog (may be null)
  2. **TABLE_SCHEM** String => table schema (may be null)
  3. **TABLE_NAME** String => table name
  4. **TABLE_TYPE** String => table type. Typical types are "TABLE", "VIEW", "SYSTEM TABLE", "GLOBAL TEMPORARY", "LOCAL TEMPORARY", "ALIAS", "SYNONYM".
  5. **REMARKS** String => explanatory comment on the table
  6. **TYPE_CAT** String => the types catalog (may be null)
  7. **TYPE_SCHEM** String => the types schema (may be null)
  8. **TYPE_NAME** String => type name (may be null)
  9. **SELF_REFERENCING_COL_NAME** String => name of the designated "identifier" column of a typed table (may be null)
  10. **REF_GENERATION** String => specifies how values in SELF_REFERENCING_COL_NAME are created. Values are "SYSTEM", "USER", "DERIVED". (may be null)
- We get columns 1, 3 and 4.
  
#### Get the table's columns

Is the moment to get the columns of a table, using `getColumns()` method:

```java
String table=...; // we set the name of an existing table
ResultSet columnes = dbmd.getColumns("BDJocs",null , taula, null);
System.out.println(Colors.Bright_White+String.format("%-25s %-15 s%-15s","Atribut/Claus","Tipus","Pot ser nul?"+Colors.reset));

while (columnes.next()){
    String columnName=columnes.getString(4);
    String tipus=columnes.getString(6);
    String nullable=columnes.getString(18);

    System.out.println(String.format("%-25s %-15s %15s",columnName,tipus,nullable));
}
```

Comments:

- `getColumns()` returns 24 columns ResultSet, with a lot of table information. We get only columns 4, 6 and 18 with the name, type and if can be null. You can view javadoc for more information.
- In the same way, to get information about keys, we can use:
  - `getPrimaryKeys()` returns a ResultSet with the tables' primary keys.
  - `getExportedKeys()` returns a ResultSet with the columns that point to the current table primary key. It means all the fields in other table that point to current table primary key.
  - `getImportedKeys()` returns a ResultSet with the columns that are imported primary keys to the current table. It means the columns that are foreign key (and point to a primary key in other tables).
  
  > You have the whole sample in package `DatabaseMeta` in sample application.