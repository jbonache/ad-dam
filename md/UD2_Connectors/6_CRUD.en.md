# Statements and CRUD operations

In this section we are going to study the main goal working with databases: manipulate data. We will always work with the same template:

1. Connect to de database.
2. Prepare the query
3. Execute the query
4. Data processing, if needed.

Point 1 has been studied on previous sections. 

In order to create the queries, we should use the following classes/interfaces:

- `Statement` $\rightarrow$ It is used in a general way, and it is useful when we want to perform **static SQL statements**, since no
accepts parameters. We create `Statement` with `createStatement()` method from `Connection` class.
- `PreparedStatement` $\rightarrow$ It is used when we want to launch several requests, and in addition, it is allowed to perform **dynamic sentences**. We create `PreparedStatement` with `prepareStatement()` method from `Connection` class.
- `CallableStatement` $\rightarrow$ Used to access stored procedures in the database, and also accepts input parameters. We create `CallableStatement` with `prepareCall()` method from `Connection` class.

When the statement is ready, we can execute it with:

- `executeQuery` $\rightarrow$ Executes statements that we **expect to return data** (SELECT queries). The return value from this execution is, as you will guess, a ResultSet.
- `executeUpdate` $\rightarrow$ Executes statements that are not **expected to return data**, but they will be used to modify the database
connected (INSERT, DELETE, UPDATE queries and CREATE TABLE)

## Create (Insert)

Let's go to see a simple `INSERT` statement sample.  

```java
ConnexioDB conDB=new ConnexioDB("BDJocs");
Connection con=conDB.getConnexio();

String SQL="INSERT INTO Joc VALUES (1, 'Double Dragon', 'Dos germans bessons experts en arts marcials s`han de fer camí en un escenari urbà on  membres de bandes rivals volen deixar-los fora de combat.', 1);";

Statement st=con.createStatement();

int affectedRows=st.executeUpdate(SQL);

System.out.println(affectedRows+ "row has been inserted");
```

As you can see, is very easy to understand the code. We create an empty Statement and the run the SQL "per se". The execution returns numbres of rows inserted. We will see better ways to insert data, using scripts.

:::note
This code is a reduced version, because when we are working with databases `SQLException` could appear. We suppose that the connection process is already done.
:::

> Sample in `InsertData` package.

## Read (Select)

Reading is the most important process we will do, because only with the login process inside an App are retrieving information from a database. In all cases we must write the sentence (SQL), execute it and finally process the returned data. Depending on the way we prepare the sentence, we could distinguish between:

1. Fixed sentences
2. Variable sentences
3. Prepared sentences

We will see both ways in details. We will use for this the `Instituto.sql` script and database.

### Fixed sentences

This sentence, as its name says, are fixed or constant sentences. The SQL is fixed, and it has not any variables.

```java
// The query
String SQL="Select * from Persona";
// The statement
Statement st=con.createStatement();
// The execution
ResultSet rst=st.executeQuery(SQL);

// processing
while(rst.next()){
    System.out.print(ConsoleColors.BLUE_BRIGHT+ "Person: "+ ConsoleColors.RESET);
    /*
    System.out.println(
            rst.getString(3)+ ", "+
            rst.getString(2)+ " "+
            rst.getInt(4));
    */
    System.out.println(
            rst.getString("apellidos")+ ", "+
            rst.getString("nombre")+ " "+
            rst.getInt("edad"));
}
        
rst.close();
```

In the processing of the information, ResultSet has:

- `type getType(int columnIndex)` $\rightarrow$ overloaded method, that return the given datatype, using the ResultSet column's index. Remember that first column is 1 instead of 0. The type will be Int, String, Double, etc. if you know the type. For unknown columns you can use `Object` as a generic type.
- `type getType(String columnName)` $\rightarrow$ same as above method but accessing to the column with the name that we have selected in the query or the name in the table.
  
### Variable sentences

Imagine that you want to recover names with `Ma` inside him.

```sql
String SQL="Select * from Persona where nombre like '%Ma%'";
```

In this case, this query is hardcoded, and if you want to change the portion inside the text, you have to edit your code. To avoid hard-coding, we can write:

```java
ConnexioDB conDB=new ConnexioDB("Instituto");
            
Connection con=conDB.getConnexio();

// hardcoded String
// String SQL="Select * from Persona where nombre like '%Ma%'";

String nombre=Utilitats.leerTextoC("Give me part of the name: ");
// The query
String SQL="Select * from Persona where nombre like '%" + nombre + "%'";
// The statement
Statement st=con.createStatement();
// The execution
ResultSet rst=st.executeQuery(SQL);

// processing
while(rst.next()){
    System.out.print(ConsoleColors.BLUE_BRIGHT+ "People with " +nombre+": "+ ConsoleColors.RESET);
    System.out.println(
            rst.getString("apellidos")+ ", "+
            rst.getString("nombre")+ " "+
            rst.getInt("edad"));
}
        
rst.close();
```

As we can see, the data is now in variables, but the construction of the SQL is more complex. Note that the texts must be between quotation marks and the numbers must not, which makes very easy to do mistakes. But it can be worse, this type of code can incur SQL injection problems, as we see in the example that follows:

```java
String idPersona=Read.readText("Tell me the id to consult: ");
String SQL = "Select * from Person where idPersona="+idPersona;
```

- If the user enters `4` $\rightarrow$ It will show the person of ID equal to 4
- If the user enters `4 or 1=1` $\rightarrow$ It will show all the people

We must avoid this type of queries in user validation statements, for which we will use the prepared statements and, obviously to be very careful checking the inputs.

### Prepared sentences

To avoid the SQL injection problem, as long as we have parameters in our query, we will make use of prepared statements. In the prepared statements, where we have to make use of a **marker**, instead of composing it with concatenations within the String, we will indicate it with a question mark (`?`), a character called **placeholder**.

Next, we must assign values to these placeholders, using `setType(int pos)` methods where `Type` is the data type that we are going to assign and `pos` is the position of the placeholder, starting with 1. Let's see the example:

```java
ConnexioDB conDB=new ConnexioDB("Instituto");
            
Connection con=conDB.getConnexio();

String ID=Utilitats.leerTextoC("Give me an id: ");
// The query
String SQL="Select * from Persona where idPersona = ?" ;
// The statement
PreparedStatement pst=con.prepareStatement(SQL);
// fill placeholders

pst.setString(1, ID);

// The execution
ResultSet rst=pst.executeQuery();

// processing
while(rst.next()){
    System.out.print(ConsoleColors.BLUE_BRIGHT+ "People with " +ID+": "+ ConsoleColors.RESET);
    System.out.println(
            rst.getString("apellidos")+ ", "+
            rst.getString("nombre")+ " "+
            rst.getInt("edad"));
}
        
rst.close();
```

:::note
You can combine ResultSet with ResultSetMetaData to get the columns name and data types stored in the database. Yoy will find a sample in the platform.
:::

> Sample in `Reading` package.
> 
## Update (Update) and Delete (Delete)

Update and delete a row, are both considered as updates, because they modify the database status. In fact, we also consider inserting as database update too. Both tasks will be inclosed in a `executeUpdate()` method. Let's go to study through samples:

### Update sample

Let's go to delete rows from a table of people between given ages:

```java
ConnexioDB conDB = new ConnexioDB("Instituto");
Connection con = conDB.getConnexio();

// give the age's bounds
int minAge = Utilitats.leerEnteroC("Give me minimal age: ");
int maxAge = Utilitats.leerEnteroC("Give me maximun age: ");

// The query
String SQL = "Delete from Persona where edad between ? and ?";

// The statement
PreparedStatement pst = con.prepareStatement(SQL);

// fill placeholders            
pst.setInt(1, minAge);
pst.setInt(2, maxAge);

// show the query after resolve placeholders
System.out.println(pst);

// The execution
int deletedtedRows = pst.executeUpdate();

// how many roas affecte
System.out.println(deletedtedRows + " has been deleted.");
```

:::warning

Deleting data is a very sensitive operation. Be careful to:

- Don't forget `WHERE` clause when deleting, because you will delete all data from the table.
- If you want to delete all the table, including the structure (definition + table), you must use `DROP TABLE` instead of `DELETE`.
- If you try to delete a row pointed by a foreign key, you will get the `SQLIntegrityConstraintViolationException` and a message like _Cannot delete or update a parent row: a foreign key constraint fails_.
:::

### Update sample

Let's go to add years to given id people:

```java
ConnexioDB conDB = new ConnexioDB("Instituto");

Connection con = conDB.getConnexio();

// give the age's bounds
int difAge = Utilitats.leerEnteroC("Give me number oy years: ");
int idMin = Utilitats.leerEnteroC("Give me minimum id: ");

// The query
String SQL = "Update Persona set edad=edad+ ? where idPersona > ?";

// The statement
PreparedStatement pst = con.prepareStatement(SQL);

// fill placeholders            
pst.setInt(1, difAge);
pst.setInt(2, idMin);

System.out.println(pst);
// The execution
int updatedRows = pst.executeUpdate();

System.out.println(updatedRows + " has been updated.");
```

:::warning 

Remember to: 

- `UPDATE` clause don't need `FROM` because we say the table name directly.
- It appears `SET` clause, with pairs `field=updatet_value`, to assign the new value to selected fields.
- If we forget `FROM` clause, all rows will be updated.
:::

> Sample in `DeleteUpdate` package.

## Scripts

A script, which we usually have created in an external file, is a set of SQL statements executed in order from top to bottom. We could take as a strategy to read the file line by line and execute it one by one, but JDBC allows executing a set of instructions in block. To do this, the first thing we need to do is enable multiple execution by adding a parameter to the connection, which is `allowMultiQueries=true`.

Next, we must load the file and compose a String with the entire script. To normalize it and make it totally portable, we must be careful with the line breaks, since depending on the system it is a `\n` or a `\r\n` combination. We can read line by line and save it in a `StringBuilder`, adding `System.getProperty("line.separator")` as separators.

Then we will only need to create a statement with said String and execute it with `executeUpdate()`. Let's see it:

```java
ConnexioDB conDB = new ConnexioDB("Instituto");

Connection con = conDB.getConnexio();

File script = new File("sql/EsquemaCine.sql");

BufferedReader bfr = bfr = new BufferedReader(new FileReader(script));

String line = null;
StringBuilder sb = new StringBuilder();

// Obtenemos el salto de linea del sistema subyacente
String breakLine = System.getProperty("line.separator");

while ((line = bfr.readLine()) != null) {
    sb.append(line);
    sb.append(breakLine);
}

String query = sb.toString();   // generemos el Script en un String
Statement stm = con.createStatement();
int result = stm.executeUpdate(query);
System.out.println("Script ejecutado con salida " + result);
```

:::tip
Do you remember `Files` and `Paths` classes? Rewrite last sample to get cleaner code.
:::

> Sample in `Scripts` package.
> 
## Transactions

If we want to protect data integrity, as well as avoid unexpected blocking situations in multithreaded applications, we must protect our operations, especially those that modify data through the use of transactions.

A transaction defines an execution environment in which save operations remain stored in memory until the transaction is completed. If at a certain moment something fails, the state is returned to the initial point of the same (initial point), or some intermediate marking point. By default, opening a **connection starts a transaction**.

- Each execution on the connection generates a transaction on itself. 
- If we want to disable this option so that the transaction encompasses several executions, we must mark it through `Connection.setAutoCommit(false);`.
- To definitively accept the transaction we will do it through `Connection.commit();` 
- To cancel the transaction `Connection.rollback();`

## Updatable ResultSets

The working methods revised in previous sections, specially when updating or deleting rows, works directly on the database. It means that you delete or update a row without loading previously in memory. What happens if you want to show data to user and then the user decide to delete or update the row. In this time, is better to use updatable ResultSet.

We have studied ResultSet as a collection of rows, and used it only for reading. We can use it to edit and delete data too. To do this, we need to open the ResultSet in different way as we have seen. For last, but not least, it will depend on the database the availability to crete this kind of ResultSets.

To get it, not depends on ResultSet even how Statement was created. We must use this constructor:

```java
public abstract Statement createStatement(
    int arg0,   // resultSetType           
    int arg1,   // resultSetConcurrency
    int arg2)   // resultSetHoldability
    throws SQLException
```

As you can see, there are 3 arguments to tell what kind or ResultSet we give in the end. These three arguments can be:

- `resultSetType` $\rightarrow$ these are options about moving and passing through rows in the ResultSet:
  - `TYPE_FORWARD_ONLY` $\rightarrow$ **default option**. ResultSet can be passed one time only.
  - `TYPE_SCROLL_INSENSITIVE` $\rightarrow$ Allow rewinding and skipping to an absolute or relative position.
  - `TYPE_SCROLL_SENSITIVE` $\rightarrow$ Same as before but allow seeing changes done in the database.
- `ResultSetConcurrency` $\rightarrow$ these are options about if ResultSet can be updated or no:
  - `CONCUR_READ_ONLY`	$\rightarrow$ **default option**. Read only. If we want to update something, only cas use `DELETE` or `UPDATE` clauses.
  - `CONCUR_UPDATABLE`	$\rightarrow$ ResultSet's rows can be updated or deleted.
- `ResultSetHoldability` these options are about behavior closing ResultSet:
  - `HOLD_CURSORS_OVER_COMMIT` $\rightarrow$ ResultSet remains opened after commit the transaction.
  - `CLOSE_CURSORS_AT_COMMIT`	$\rightarrow$ ResultSet is closed after commit the transaction. It improves performance.

In next sample we could ask our DBMS if these kinds of ResultSet are supported:

```java
ConnexioDB conDB = new ConnexioDB("Instituto");
            
Connection con = conDB.getConnexio();
DatabaseMetaData dbmd = con.getMetaData();

System.out.println("TYPE_FORWARD_ONLY: " + dbmd.supportsResultSetType(ResultSet.TYPE_FORWARD_ONLY));
System.out.println("TYPE_SCROLL_INSENSITIVE: " + dbmd.supportsResultSetType(ResultSet.TYPE_SCROLL_INSENSITIVE));
System.out.println("TYPE_SCROLL_SENSITIVE: " + dbmd.supportsResultSetType(ResultSet.TYPE_SCROLL_SENSITIVE));
System.out.println("CONCUR_READ_ONLY: " + dbmd.supportsResultSetConcurrency(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY));
System.out.println("CONCUR_UPDATABLE: " + dbmd.supportsResultSetConcurrency(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_UPDATABLE));
```

### ResultSet movement

As we can see, not only could move cursor forward:

- `next`, `previous`, `first` and `last` $\rightarrow$ as the name says, move forward, backward, beginning and last row. It returns true if the cursor is pointing over a row and false otherwise.
- `beforeFirst` and `afterLast`$\rightarrow$ the cursor will point out of the rows, before the first or after the last row.
- `relative(int n)` $\rightarrow$ the cursor moves _n_ rows (forward or backward, depending on the sign).
- `absolute(int n)` $\rightarrow$ the cursor is pointed on row _n_.

### Deleting rows in a ResultSet

After placing the cursor on the row to be deleted we can delete it from the ResultSet (and from the database) with the `deleteRow()` method. When deleting a row, the cursor will be pointing to the row before the one deleted.

### Updating rows in a ResultSet

After placing the cursor on the desired row, we must:

1. Update the desired columns, using the `updateType(int column, type newValue)` method, where the i-th column (or with its name) is assigned the new value of the given type.
2. Once all the desired columns have been modified, the changes are saved with `updateRow()`.

:::warning

We can't update an entire row, we should update column by column and then update the row.
:::

### Inserting rows in a ResultSet

If we want to insert a new row in a ResultSet, we must:

1. Generate a new empty row, and this is achieved with the `moveToInsertRow()`. 
2. On this row we apply the `updateType(int column, type newValue)` method to all the columns that we must set a value, and finally
3. We will proceed to insert the new row with `insertRow()`.

::: warning
Important:

- These Update, Delete and Insert operations can only be performed on queries that originate in a table without groupings.
- To avoid complexity in our programs, it is worth assessing the convenience of translating the ResultSet updates to pure SQL and executing it natively in the databases through new statements.
:::

> Sample in `UpdatableResultSet` package.
