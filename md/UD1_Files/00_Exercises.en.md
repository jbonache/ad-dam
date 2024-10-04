# File System

## `ls -la`

In this exercise we are going to implement a tuned version of the [`ls`](https://man7.org/linux/man-pages/man1/ls.1.html) program. It will list a _directory_ in a specific _view_ mode. Both directory and view will be indicated when we start the program.

The **view** mode can be list, columns or table:

- **List** $\rightarrow$ The file names one above others.
- **Columns** $\rightarrow$ The same as list but in several columns
- **Table** $\rightarrow$ File information will be showed, like this pattern: `DFRWH name size mod_data`:
  - `D` $\rightarrow$ it's a directory
  - `F` $\rightarrow$ it's a file
  - `R` $\rightarrow$ we can read the file
  - `W` $\rightarrow$ we can write the file
  - `H` $\rightarrow$ the file is hidden

#### Submission

A single `.java` file with the program. To help you with some task here is some code to format a string list in columns. The `MAX_FILES_BY_COLUMN = 4` must be initialized.

```java
public static void ListaColumnas(String[] filenames){
  int columnas = (filenames.length / MAX_FILES_BY_COLUMN)+1; 
  String[][] salida = new String[MAX_FILES_BY_COLUMN][columnas];
  for (int i=0;i<filenames.length;i++){
    salida[i % MAX_FILES_BY_COLUMN][i / MAX_FILES_BY_COLUMN]=
        filenames[i];
  }
  //bucle para mostrar salida ls
  for (int i=0;i<MAX_FILES_BY_COLUMN;i++){ 
    for (int j=0; j<columnas;j++)
      System.out.print(salida[i][j] + " - "); 
    System.out.println(" /n");
  }
}
```

## Hidden files

Write a Java program that find out all the hidden files of a directory and its subdirectories recursively.

## Biggest file

Write a Java program to find the big file of a set of files in a directory.

# Text Files

## How many vowels?

Write a program who receives a name of a text based file, and a vowel. The program will show the number of times that appear that vowel on the file.

## `tail` and `head`

Write your own version of tail and head gnu utils. You can find out information in:

- [tail](https://man7.org/linux/man-pages/man1/tail.1.html)
- [head](https://man7.org/linux/man-pages/man1/head.1.html)

:::note

Remember that these programs show the last and first ten lines by default. If you want to modify it, you can adjust with `-nX` where X is the number of lines you want to show.
:::
  
# Binary Files

## Find size of an image file (**HARD**)

According to the specification of bmp or png pictures, as you can see:

- [BMP specification](https://en.wikipedia.org/wiki/BMP_file_format)
- [PNG specification](https://en.wikipedia.org/wiki/Portable_Network_Graphics)

write a program that recieves a filename of a png or a bmp file and returns the image's width and height

# CSV Files

Make a function to show a CSV file, according to the next requirements:

- We must indicate to the function the separator character.
- We must indicate to the function if the CSV has a header line or not.
- (Improvement) The function must calculate the max column size for each line, and show each column in the size previously calculated, as mysql show its tables. 

On the platform you have a MLB players names CSV files, but you can choose a file by your own.

# XML Files

In the platform you will find a file called `monaco2017.xml` containing a lot of information about the F1 2017 championship race in Monaco. Apart from other information, we could find:

```xml
<Result number="5" position="1" positionText="1" points="25">
    <Driver driverId="vettel" code="VET" url="http://en.wikipedia.org/wiki/Sebastian_Vettel">
        <PermanentNumber>5</PermanentNumber>
        <GivenName>Sebastian</GivenName>
        <FamilyName>Vettel</FamilyName>
        <DateOfBirth>1987-07-03</DateOfBirth>
        <Nationality>German</Nationality>
    </Driver>
    <Constructor constructorId="ferrari" url="http://en.wikipedia.org/wiki/Scuderia_Ferrari">
        <Name>Ferrari</Name>
        <Nationality>Italian</Nationality>
    </Constructor>
    <Grid>2</Grid>
    <Laps>78</Laps>
    <Status statusId="1">Finished</Status>
    <Time millis="6284340">1:44:44.340</Time>
    <FastestLap rank="2" lap="38">
        <Time>1:15.238</Time>
        <AverageSpeed units="kph">159.669</AverageSpeed>
    </FastestLap>
</Result>
```

Within the `Result` we will find as attributes the pilot's number (`number` attribute) and the position in which he finished the race (`position`). In addition, we will find the following elements:

- Driver: information about who the driver is.
- Manufacturer: information about the brand of the car
- Grid: Position in which the driver has started
- Laps: Laps completed
- Status: which tells us with the `statusID=1` attribute that the pilot finished the race.
- Time: which tells us the milliseconds attribute how long it took to complete the race (in ms), and in its value the difference with respect to the first ones.
- FastesLap: which tells us the classification with respect to the fast lap in its rank attribute.

On the platform you will find:

- Driver class: full implemented, who need the driver portion of the XML to create a Driver object.
- ResultadoCarrera: partially implemented, with the following attributes:

```java
private Driver d;
private String constructor;
private int initialPos;
private int finalPos;
private long timeMillis;
private int completedLaps;
private int rankFastesLap;
private boolean finisher;
```

Complete the program, adding:

- `Constructor(Element result)`, that receives a XML Element.
- `public String toCSV()`, who will return all fields sepparated by `;`.
- `public ArrayList<ResultadoCarrera> carregaResultadosXML(String nomXML)` who receive the name of XML file and will load all the results.
- `public void saveAsCSV(String nomFitxer, ArrayList<ResultadoCarrera> elsResultats)` who will save on a csv text file the results loaded previously.

# JSON Files

On the platform you will find a file called `SW.json`. It recovers information about characters of the famous saga. Write a program to obtain:

- Characters that did not drive any vehicle.
- List of characters, ordered by number of films that it appears.
- Create an XML file like this, with a brief summary information:

```xml
<character films="4" vehicles="0">
  <name>Luke Skywalker</name>
  <mass>77</mass>
  <url>https://swapi.dev/api/people/1/</url>
</character>
```

Let's go starting the course playing with databases and programs, to review all the concepts you need to begin with the main topics with a solid base.
<!--

# Links



- $\rightarrow$
- $\rightarrow$
- $\rightarrow$
  
-->