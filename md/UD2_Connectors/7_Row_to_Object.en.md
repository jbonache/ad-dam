# From rows to Objects

Finally, to close the unit, we are going to work in the natural way: recover data from database, create objects from ResultSet, and save data into database.

We will study a new way to create ours POJO's (and BEAN's), using a modern library called Lombok.

## POJO's, BEANS's and Project Lombok

We must create classes according to our object-oriented model. Depending on how we made classes, could be POJO's or BEAN's or another kind of classes. Let's go to understand it (extracted from [here](https://www.geeksforgeeks.org/pojo-vs-java-beans/)).

### POJO

 It means Plain Old Java Object. It is a class who:

- It should not extend any class.
- It should not implement any interface.
- It should not contain specific annotations.

There is no restriction on access-modifiers of fields. They can be private, default, protected, or public. It is also not necessary to include any constructor in it.

A sample of POJO is, for instance:

```java
// AS POJO
public class Modul {
  // attributes
  String nom;
  int hores;
  double nota;
  // constructor
  public Modul(String nom, int hores, double nota) {
      this.nom = nom;
      this.hores = hores;
      this.nota = nota;
  }
  // getters
  public String getModul(){return this.nom;}
  public int getHores() {return this.hores;}
  public double getNota() {return this.nota;}

  @Override
  public String toString() {
      return "Modul{" + "nom=" + nom + ", hores=" + hores + ", nota=" + nota + '}';
  } 
}
```

As you can see, a POJO is the minimalis class that we can do.

### BEAN

Beans are special type of POJO's. There are some restrictions on POJO to be a BEAN.

- All JavaBeans are POJOs but not all POJOs are JavaBeans.
- `Serializable`, they should implement Serializable interface. Still, some POJOs who don’t implement a Serializable interface are called POJOs because Serializable is a marker interface and therefore not of many burdens.
- Fields should be private. This is to provide complete control on fields.
- Fields should have getters or setters or both.
- A no arguments constructor should be there in a bean.
- Fields are accessed only by constructor or getter setters.

```java
// AS POJO
public class Modul {
  // attributes
  private String nom;
  private int hores;
  private double nota;
  // constructor
  public Modul() { }
  // getters
  public String getModul(){return this.nom;}
  public int getHores() {return this.hores;}
  public double getNota() {return this.nota;}
// setters
  public void getModul(){return this.nom;}
  public void getHores() {return this.hores;}
  public void getNota() {return this.nota;}

  @Override
  public String toString() {
      return "Modul{" + "nom=" + nom + ", hores=" + hores + ", nota=" + nota + '}';
  } 
}
```

To sum up, POJO classes and Beans both are used to define java objects to increase their readability and reusability. POJOs don’t have other restrictions while beans are special POJOs with some restrictions.

### Lombok

Regardless using POJO or BEAN, there are some repetitive tasks that we have to do to create our classes. Most of this task are implemented by IDE's, as well as create getters, setters, encapsulate fields, create constructors, etc. Project Lombok is a library that avoid doing this repetitive tasks, and saying what we want and the library and compiler do the rest.

In this link [setup Lombok](https://projectlombok.org/setup/) you will find how to set up the library, in each project manager (gradle, maven, etc.) and in each IDE (Netbeans, Eclipse, IntelliJ, etc.). 

Once you have installed, you only need to say through annotations, what do you want:

- `@Getter` $\rightarrow$ will generate all Getters.
- `@Setter` $\rightarrow$ will generate all Setters.
- `@Data` $\rightarrow$ all methods that needs a POJO, including `ToString`
- `@AllArgsConstructor` ou `@NoArgsConstructor` $\rightarrow$ generate the constructor you want

In the platform you have a video explaining how to add gradle dependency and using Lombok.

## Loading objects

Let's go to finish with a sample:

#### Class Persona 

This class is the POJO of one person. It will create getters, setter, toString and the main methods with only a few lines and some annotations.

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Persona {
    private int idPersona;
    private String nombre;
    private String apellidos;
    private int edad;
}
```

Notice that with Lombok work is made easy.

#### Filling an Array

To fill a data structure from database, you have to get data first. You must create the Statement or PreparedStatement, run the query. 

The main task is transforming the ResultSet into a List, but it's a easy task:

- Loop over the ResultSet (with next), and for each row:
  - Create an object with the values stored in the columns
  - Add this object to the List


```java
ArrayList<Persona> lasPersonas= new ArrayList();

ConnexioDB conDB=new ConnexioDB("Instituto");

Connection con=conDB.getConnexio();

String SQL="Select * from Persona" ;
// The statement
Statement st=con.createStatement(
        ResultSet.TYPE_SCROLL_INSENSITIVE,
        ResultSet.CONCUR_READ_ONLY);

// The execution
ResultSet rst=st.executeQuery(SQL);

if (!rst.next()){
    System.out.println("No people in DB");
}
else{
    rst.beforeFirst();
    while(rst.next()){
        Persona p= new Persona(
                rst.getInt(1),
                rst.getString(2),
                rst.getString(3),
                rst.getInt(4));
        System.out.println("Adding " +p);
        lasPersonas.add(p);
    }
}

System.out.println("Added " + lasPersonas.size() + " people");
rst.close();
```

Now you could change the information in the objects, and finally, if any modification has been done, you should save on database. The questions are:

- How do I know if an object has been update?
- How do I know what field should save?

As you can see, there are several tasks that require a control of data modification and how to save it. This task will be easy with ORM, and we will study them.

> Sample in `RowToObject` package

© Joan Gerard Camarena, October 2022
