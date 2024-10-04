# Object–relational impedance mismatch

**Database Management Systems** (DBMS - SGBD in spanish) are mostly based in Entity-Relationship model (E/R), where information is stored in several tables **related** to each other. It is a simple and efficient technology, which has endured over the years, and which is still the model used by most databases and DBMSs today. Despite the success, the model has some limitations, such as the representation of poorly structured or complex information.

Conceptual models help us model a complex reality, and are based on a process of reality abstraction. Each model has a way of capturing this reality, but, all of them are closer to the human mentality than to the memory of a computer.

When we model a database, we make use of the **Entity-Relationship** conceptual model, and subsequently, we do a process of transform into tables and normalizing this model, to have a **relational data model**.

In the case of **object-oriented** programming, we try to represent reality through objects and the relationships between them. This is another type of conceptual model, but one that aims to represent the same reality as the relational one.

So, we have two different approaches to represent the reality of a problem: the Relational Model of the database and the Object-Oriented model of our applications.

## Representation of information with the relational model

The relational model is based on tables and the relationship between them:

- Each table has as many columns as attributes we want to represent, and as many rows as records or elements of that type contain.
- The tables have a primary key, which identifies each of the records, and can be formed by one or more attributes.
- The relationship between tables is represented by external keys, which consist of including in a table the primary key of another table, as a _reference_ to it. When is removed a record of a table, whose primary key is referenced by another, must be ensured that we maintain the **referential integrity** of the database. Then, before this deletion of a primary key, we can:
  - Do not allow deletion (**NO ACTION**),
  - Carry out the deletion in cascade, also deleting all the records that referred to the primary key of the deleted record (**CASCADE**),
  - Set to null (**SET NULL**), so that the external key referred to the primary key from the other table takes the value of `NULL`.
- The different fields of the tables can also have certain associated restrictions, as they can to be:
  - **Non-null** value constraint, so the field cannot be null in any case,
  - **Uniqueness** restriction on one or several fields, so that the value must be unique in the whole table. 
  - Domain restriction, or what is the same, can have a set of possible values default

::: note
Primary keys have both properties: non-null value and uniqueness.
:::

#### Sample

A database to store information about players, games etc., can be like this:

\begin{center}
\begin{minipage}{0.9\textwidth}
\includegraphics[width=0.9\columnwidth]{./img/E_R.png}
\end{minipage}
\end{center}

And implementing with `MySQL`:

```sql
CREATE SCHEMA IF NOT EXISTS `BDJocs` DEFAULT CHARACTER SET utf8 ;
USE `BDJocs` ;

CREATE TABLE IF NOT EXISTS `BDJocs`.`jugador` (
  `id` INT NOT NULL,
  `nick` VARCHAR(45) NULL,
  `dataRegistre` DATETIME NULL,
  PRIMARY KEY (`id`))
ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `BDJocs`.`Genere` (
  `id` INT NOT NULL,
  `nom` VARCHAR(45) NULL,
  `descripció` VARCHAR(256) NULL,
  PRIMARY KEY (`id`))
ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `BDJocs`.`Joc` (
  `id` INT NOT NULL,
  `nom` VARCHAR(45) NULL,
  `descripció` VARCHAR(256) NULL,
  `Genere_id` INT NOT NULL,
  PRIMARY KEY (`id`),
  INDEX `fk_Joc_Genere1_idx` (`Genere_id` ASC),
  CONSTRAINT `fk_Joc_Genere1`
    FOREIGN KEY (`Genere_id`)
    REFERENCES `BDJocs`.`Genere` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `BDJocs`.`Puntuacions` (
  `jugador_id` INT NOT NULL,
  `Joc_id` INT NOT NULL,
  `puntuacio` INT NULL,
  PRIMARY KEY (`jugador_id`, `Joc_id`),
  INDEX `fk_jugador_has_Joc_Joc1_idx` (`Joc_id` ASC),
  INDEX `fk_jugador_has_Joc_jugador1_idx` (`jugador_id` ASC),
  CONSTRAINT `fk_jugador_has_Joc_jugador1`
    FOREIGN KEY (`jugador_id`)
    REFERENCES `BDJocs`.`jugador` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_jugador_has_Joc_Joc1`
    FOREIGN KEY (`Joc_id`)
    REFERENCES `BDJocs`.`Joc` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;
```

## Representation of information with the object-oriented model

Like the Entity-Relationship model, the Object-Oriented model is a conceptual data model, but which focuses the importance on the modeling of objects.

An object can represent any conceptual element: entities, processes, actions... An object it does not only represent the characteristics or properties, but also focuses on the processes that they suffer. In terms of the object-oriented model, we say that an object is data
plus operations or behavior.

In the introductory unit we already reviewed object-oriented programming, so we will limit ourselves to a brief review of the main concepts:

- An object is an entity with certain properties and certain behavior.
- In terms of OOP, properties are known as **attributes**, and the set of their values determine the **state** of the object at a given moment.
- Behavior is determined by a series of functions and procedures that we call **methods**, and that modify the state of the object.
- An object will also have a name by which it is identified.
- A **class** is an abstraction of a set of objects, and an object must belong necessarily to some class.
- The classes define the attributes and methods that the objects of this class will possess.
- An object is said to be an **instance** of a class.

The same sample that we have represented lately, with an object-oriented representation could be:

\begin{center}
\begin{minipage}{0.9\textwidth}
\includegraphics[width=0.9\columnwidth]{./img/O_O.png}
\end{minipage}
\end{center}

As we can see, it has a similar structure, to which we have also added some methods such as getters and the setters. In addition, the different classes do not have an identifier attribute, since each object he identifies himself. We see a small approximation of how we would implement this hierarchy in Java.

The `Genere` class is a POJO class, that only stores information (name and description of the game kind)and implements getters and setters.

```java
public class Genere {
    protected String nom;
    protected String descripcio;

    public Genere(String nom, String descripcio) {
        this.nom = nom;
        this.descripcio = descripcio;
    }

    public String getNom() {return nom;}
    public void setNom(String nom) {this.nom = nom;}

    public String getDescripcio() {return descripcio;}
    public void setDescripcio(String descripcio) {
        this.descripcio = descripcio;
    }
}
```

Class `Joc` stores name, description and genre of the game. Unlike the relational model, where what is stored it would be an external key to genre, since we don't have external keys here, we store one object itself (the reference).

```java
public class Joc {

    protected String nom;
    protected String descripcio;
    protected Genere genere;

    public Joc(String nom, String descripcio, Genere genere) {
        this.nom = nom;
        this.descripcio = descripcio;
        this.genere = genere;

    }

    public String getNom() {return this.nom;}
    public void setNom(String nom) {this.nom = nom;}

    public String getDescripcio() {return this.descripcio;}
    public void setDescripcio(String descripcio) {
        this.descripcio= descripcio;
    }

    public Genere getGenere() {return this.genere;}

    public void setGenere(Genere genere) {this.genere = genere;}
}
```

`Registre` class present the relation between `Jugador` and `Joc` (when a player plays a game), and stores points and a reference to the game.

```java
public class Registre {
    private int puntuacio;
    private Joc joc;

    public Registre(int puntuacio, Joc joc) {
        this.puntuacio = puntuacio;
        this.joc = joc;
    }

    public int getPuntuacio() {return puntuacio;}
    public void setPuntuacio(int puntuacio) {
        this.puntuacio = puntuacio;
    }

    public Joc getJoc() {return joc;}

    public void setJoc(Joc joc) {this.joc = joc;}   
}
```

And finally, the `Jugador` class stores nick and registry date for each player, and then, an array with all the games that the player has played:

```java
public class Jugador {

    private String nick;
    private Date dataRegistre;
    private Set<Registre> puntuacions;

    public Jugador(String nick, Date dataRegistre) {
        this.nick = nick;
        this.dataRegistre = dataRegistre;
    }

    public String getNick() {return nick;}
    public void setNick(String nick) {this.nick = nick;}

    public Date getDataRegistre() {return dataRegistre;}
    public void setDataRegistre(Date dataRegistre) {
        this.dataRegistre = dataRegistre;
    }

    public Set getPuntuacions() {return this.puntuacions;}
    public void setPuntuacio(Joc joc, int puntuacio) {
        Registre registre = new Registre(puntuacio, joc);
        this.puntuacions.add(registre);
    }
}
```

:::tip
The `Set` interface and the `HashSet` class

`Set` is an interface of the `java.util` package that deals with a collection or set of elements uncluttered and without duplicates.

On the other hand, `HashSet` is a class that implements the `Set` interface, and that is based on one hash table, a data structure that allows you to locate objects based on a key that indicates the position in the table, allowing direct access to the element, which makes them ideal for searches, insertions and deletions.
:::

## Relational vs Object-Oriented model

Conceptually, the object-oriented model is a _dynamic model_, which focuses on objects and on the processes that these undergo, but which does not take into account, from the start, their persistence. We have to achieve, therefore, the ability to save the states of the objects permanently, and load them when the application needs them, as well as maintaining consistency between this stored data and the objects that represent them in the application.

One way to offer this persistence to objects would be to use a _Relational DBMS_, but we will encounter some complications. The first, from a conceptual point of view, is that the entity-relationship model focuses on the data, while the object-oriented model focuses on the
objects, understood as groups of data, and the operations performed on them.

Another, quite important difference, is the linking of elements between one model and another. For one other hand, the relational model adds extra information to the tables in the form of a foreign key, while in the object-oriented model, we don't need this external data, but rather the binding between objects is done through references between them. An object, for example, won't need a key either primary, since the object is identified by itself.

As we have seen in the example of the previous sections, the tables in the relational model had a key primary, to identify objects and external keys to express relationships, while in the object-oriented model, these disappear, expressing the relationships between objects through references. In addition, the way these relationships are expressed is also different. In the relational model, for example, the Scores record is a table that links the Player table to the Game table, and add to this the player's score in the game. On the other hand, in the Java implementation we have done, we have objects of type `Registre` that store a score and a reference to Game, however is the Player class, which maintains the set of Records of its scores.

On the other hand, when manipulating the data, it must be taken into account that the relational model has of languages ​​(mainly SQL) designed exclusively for this purpose, while in one object-oriented language works differently, so it will be necessary to incorporate mechanisms which allow these queries to be made from the programming language. Also, when we get the results of the query, we also encounter another problem, and that is the conversion of results. One querying a database always returns a result in the form of a **table**, so it will be necessary to transform these in states of the application objects.

All these differences imply what is known as **object-relational lag**, and that it will force us to make certain conversions between objects and tables when we want to save the information in a DBMS. In this unit and the following ones, we will see how to overcome this lag from different approaches.