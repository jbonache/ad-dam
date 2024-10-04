# 1. JSON Files

JSON is another lightweight text format for data exchange. JSON stands for _JavaScript Object Notation_, and is a subset of the language's literal object notation, which has been adopted alongside XML as one of the major standards for data exchange and storage.

One of the advantages of JSON over XML is the ease of writing parsers, but more important than that, is that it expresses the same thing as XML but in a much more concrete and concise way, so it is commonly used in environments where the flow of data is important, as is the case with the servers of Google, Yahoo, etc. serving millions of users.

## 1.1. JSON format 

The complete specification can be viewed [here](https://www.json.org/)

The types of data that we can represent in JSON are:

- **Numbers**, both integers and decimals.
- **Strings**, expressed between quotes and with the possibility of including escape sequences.
- **Booleans**, to represent the values `true` and `false`.
- **Null**, to represent the `null` value.
- **Array**, to represent lists of zero or more values, of any type, enclosed in square brackets and separated by commas.
- **Objects**, as collections of `<key>:<value>` pairs, separated by commas and braces, and of any value type.

We will see it better with a well-known example: The one of the modules we are working with:

```json
{
  "curs": [
        {
          "nom": "Accés a Dades",
          "hores": 6,
          "qualificacio":  8.45
        },
        {
          "nom": "Programació de serveis i processos",
          "hores": 3,
          "qualificacio": 9.0
        },
        {
          "nom": "Desenvolupament d'interfícies",
          "hores": 6,
          "qualificacio": 8.0
        },
        {
          "nom": "Programació Multimèdia i dispositius mòbils",
          "hores": 5,
          "qualificacio": 7.34
        },
        {
          "nom": "Sistemes de Gestió Empresarial",
          "hores": 5,
          "qualificacio": 8.2
        },
        {
          "nom": "Empresa i iniciativa emprenedora",
          "hores": 3,
          "qualificacio": 7.4
        }
      ]
}
```
Let's see how `curs` is an array or a list of modules (although we don't use the modul label now), which in this case are objects with three elements: the name which is a string of characters, the hours which is an integer, and the rating, which is represented as a decimal number. Note that, like XML, we also need a root object, in this case the current element.

Internet is full of services that offers information in JSON format as well. For instance, you can visit:

- <https://arkhamdb.com/api/public/card/01001>
- <https://swapi.dev/api/films/1/>
- <http://hp-api.herokuapp.com/api/characters>
  
There is a wide range of Java libraries for manipulating JSON documents (GSON, Jackson, JSON.simple...). In our case, we are going to use the `org.json` library, which we can check in the Maven repository: <https://mvnrepository.com/artifact/org.json/json>

In the next section we will comment on the functionality of the library, and in an attached document, we will see how to incorporate it into our projects through the Gradle dependency manager.

## 1.2. `org.JSON`

The library provides a set of classes for parsing JSON documents for Java, as well as converters between JSON and XML. Among the classes it offers, we could highlight:

- `JSONObject` → Store key-value pairs in unordered form. Values ​​can be Boolean, JSONArray, Number, String, and JSONObject.NULL. Their constructors take as input different representations (String, maps, beans) and store them as a set of key-value elements.
- `JSONTokener` → Parses a JSON string, and is used internally by JSONObject and other classes to parse JSON strings.
- `JSONArray` → Stores a sequence of values, and represents a JSON array.
- `JSONWriter` → Provides a way to produce JSON text. It has an `append(String)` method, which adds more text to a JSON object of text type, in addition to the key(String) and value(String) methods for adding keys and values ​​to a JSON string. The class also allows you to write an array.

## 1.3. Creating JSON files

Our samples are based on the data of DAM modules saw in previous sections. Consider this code block as starting point where an array `Curs` is created and populated with modules :

```java
private void creaCurs() {    
    // Aquest mètode inicializa l'objecte "Curs" de la classe JSONLib
    // que no és més que un vector de mòduls

    // Definim els vectors per inicialitzar dades
    String[] moduls={"Accés a Dades", "Programació de serveis i processos", "Desenvolupament d'interfícies", "Programació Multimèdia i dispositiud mòbils", "Sistemes de Gestió Empresarial", "Empresa i iniciativa emprenedora"};
    int[]  hores={6, 3, 6, 5, 5, 3};
    double[] notes={8.45, 9.0, 8.0, 7.34, 8.2, 7.4};

    // Recorrem els vectors, creant els objectes
    // de tipus Modul i guardant-los en Curs
    for (int i=0;i<moduls.length;i++){
        Modul m = new Modul(moduls[i], hores[i], notes[i]);
        this.Curs.add(m);
    }
}
```

To create the whole JSON file, we need to create individual JSON's, representing every object. Due to this object gets the information from the objects, it is a good idea to create methods inside the module object to get the JSON representation of every object:

```java
public JSONObject getModulJSON(){
    JSONObject modul = new JSONObject();

    modul.put("nom", this.nom);        
    modul.put("hores", this.hores);
    modul.put("nota",  this.nota);

    // Si volguerem afegir un element nul, 
    // hauriem de fer:
    // modul.put("atribut", JSONObject.NULL);

    return modul;

};
```

Once the individual JSON is solved, we need to create the main JSON, the one who contains all the modules. It is easy to guess that the main element will be an array, containing all the module objects inside it.

```java
private JSONObject creaJSON() {

        // root element "Curs"
        JSONObject curs = new JSONObject();
        
        // who is a JSONArray
        JSONArray jsarray = new JSONArray();

        // we populate the array with individual modules
        for (Modul m : this.Curs) {
            JSONObject modul_json = m.getModulJSON();
            jsarray.put(modul_json);
        }

        // Create the curs element with the array
        curs.put("curs", jsarray);

        return (curs);
    }
```

Finally, we need to save it to disk in a text file. This code block stores a JSONObject, using a FileWriter with an specific indentation:

```java
private void escriuJSON(String filename, JSONObject jso){

    try {
        FileWriter file = new FileWriter(filename);
        file.write(jso.toString(4)); // 4 són els espais d'indentació
        file.close();

    } catch (IOException e) {
        System.out.println("Error, no es pot crear el fitxer "+filename);
    }
}
```
## 1.4. Reading JSON files

In order to read JSON files, we have to split the task in two parts. Firstly, and very simple, is to read from the file to a String, and call to the JSONObject constructor with this string. Then, we need to process it.

```java

private JSONObject LligJSON(String filename){
    try {
        // Amb FileReader llegirem caràcter a 
        // caràcter el fitxer i l'afegim al string myJson
        FileReader file = new FileReader(filename); 
        String myJson="";

        int i;
        while ((i=file.read()) != -1) 
            myJson=myJson+((char) i);

        //System.out.println(myJson);
        file.close();

        // I fem ús del constructor de JSONObject
        // al que li passem un string amb el JSON:
        return (new JSONObject(myJson));


    } catch (Exception e)
    {
        System.out.println("Error llegint el fitxer");
        return null;
    }

}
```

Once we have the main `JSONObject`, we need to recover the `JSONArray` (called _curs_). We could use the length() method to go over all modules with a `for` loop. To obtain the single objects of a JSONObject, the `get(string label)` method is provided, returning the object labeled. We need to take care of the type, and do conversions when we need it. In the sample above we only show the JSON, instead of create objects in memory.

```java
private void MostraJson(JSONObject json){

    // amb el mètode getJSONArray obtenim el primer
    // element "curs", que era una llista
    JSONArray jsa=json.getJSONArray("curs");

    // I ara recorrem aquesta llista:
    for (int i = 0; i < jsa.length(); i++) {
        // Agafem cada element de l'array amb "get"
        JSONObject modul=(JSONObject)jsa.get(i);
        // Amb el get anterior tindrem objectes JSON 
        // de mòduls, tipus:
        // {"nom": "Modul", "hores": hores, "nota": nota }
        // Als valors d'aquests parells també accedirem amb get:
        System.out.println("Modul: "+ modul.get("nom"));
        System.out.println("Hores: "+ modul.get("hores"));

        System.out.println("Nota: "+modul.get("nota"));

        /*
        En esta funció escrivim els objectes JSON. Si volguérem
        crear de nou l'estructura d'objectes, crearíem cadascun 
        dels mòduls amb:
            Modul m=new Modul(modul.get("nom"), modul.get("hores"), modul.get("nota"));
        */
    }
}
```
