# `ResultSet` class. 

Now, we are connected to the database, is time to start asking for information to the DBMS. This information will be in form of queries (SQL) or others methods, but almost 90% of times, as the SQL's, the information returned is in a tabular format. Tabular formats are well known because programs like MySQL Workbench or DBeaver show data in these way.

In Java, the class who allow to works with data in tabular format coming from databases is called `Resultset`. A `Resultset` is composed by a table (rows and columns) and a pointer to a row, called `cursor`.

\begin{center}
\begin{minipage}{0.9\textwidth}
\includegraphics[width=0.9\columnwidth]{./img/ResultSet.png}
\end{minipage}
\end{center}

1. When we retrieve data from databases, the information is stored in the table, and the cursor points to an imaginary void row called `beforeFisrt`. 
2. `Resultset` contains a special method called `next()`, that do two actions:
   1. Returns `true` if exists a data row after the current row pointed by the cursor, and `false` otherwise.
   2. Push forward the cursor, and points to the next row.
3. When a `Resultset` cursor return `false`, is because all rows have been visited, and now, the cursor is pointing to another imaginary row called `afterLast`

When the cursor is pointing a real row, is when we can retrieve data of the columns of the current row. We can use a special and overloaded method, as follows:

- `getXXX(int columnPosition)` $\rightarrow$ return the data, where `XXX` is a datatype (Int, Float, Double, String, etc.) of the position of the column, starting in 1.
- `getXXX(String columnName)` $\rightarrow$ return the data, where `XXX` is a datatype (Int, Float, Double, String, etc.) of the column with the given name.

:::caution
Take into account:

- The columns start in 1 instead of 0, unlike arrays
- If you do not know the data type of a column, you can use `getObject`and it returns a generic `Object`.
- These methods could throw `SQLException`, if the column rank or name is out of rank or not exists.
:::

## ResulSetMetaData

ResultSets from of a query also have a set of metadata. This metadata can be obtained using the `ResultSetMetaData`. Most relevant methods of this class are:

- `int getColumnCount()` $\rightarrow$ Gets the number of columns in the ResultSet.
- `String getColumnName(index)` $\rightarrow$ Gets the name of the column indicated in the index (remember that first is 1).
- `String getColumnTypeName(index)` $\rightarrow$ Gets the type of the column

ResulSetMetaData is obtained from each previously generated ResultSet, with `getMetaData()` method.

## ResultSet algorithm

Regardless the data content of your ResultSet, the way in we should work is as follows:

```java
// Connect to DB
Connection conn = ...;
        
// recover data
ResultSet rst= conn...

// while data available exists
while (rst.next()){
    //process the current row
}
```

Be notice than:

- Inside the while loop you don't need to call `next()` again. You skip one row otherwise.
- When the last row were processed, `next()` return `false`, and the loop finish.

## Sample

In this block of code let's go to see a brief sample to run a query (`Select * from table`). We will see queries in next sections.

```java
//with a previous connection
String taula="Jocs";
ResultSet rst = con.createStatement().executeQuery("SELECT * FROM " +taula);
System.out.println(Colors.Cyan);
System.out.println("");
System.out.println("Contingut de " + taula);
System.out.println("******************************");

ResultSetMetaData rsmdQuery = rst.getMetaData();

// print the columns name
for (int i = 1; i <= rsmdQuery.getColumnCount(); i++)
  System.out.print(String.format("%-25.25s",rsmdQuery.getColumnName(i)));

System.out.println();
System.out.println(Colors.Reset);

// print the values
while (rs.next()) {
  for (int i = 1; i <= rsmdQuery.getColumnCount(); i++)
  System.out.print(String.format("%-25.25s ",rst.getString(i)));
  System.out.println();
}
```