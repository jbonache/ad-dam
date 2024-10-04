# 1. Arxius JSON

JSON és un altre format de text lleuger per a l'intercanvi de dades. JSON significa _JavaScript Object Notation_ i és un subconjunt de la notació literal d'objectes del llenguatge, que s'ha adoptat juntament amb XML com un dels principals estàndards per a l'intercanvi i emmagatzematge de dades.

Una de les avantatges de JSON respecte a XML és la facilitat d'escriure analitzadors, però el que és més important és que expressa la mateixa cosa que XML però d'una manera molt més concreta i concisa, per la qual cosa s'utilitza habitualment en entorns on el flux de dades és important, com és el cas dels servidors de Google, Yahoo, etc., que atenen milions d'usuaris.

## 1.1. Format JSON

La especificació completa es pot veure [aquí](https://www.json.org/)

Els tipus de dades que podem representar en JSON són:

- **Nombres**, tant enters com decimals.
- **Cadenes de text**, expressades entre cometes i amb la possibilitat d'incloure seqüències d'escapament.
- **Booleans**, per representar els valors `true` i `false`.
- **Null**, per representar el valor `null`.
- **Array**, per representar llistes de zero o més valors, de qualsevol tipus, tancades entre claudàtors i separades per comes.
- **Objectes**, com a col·leccions de parells `<clau>:<valor>`, separats per comes i entre claus, i de qualsevol tipus de valor.

Ho veurem millor amb un exemple ben conegut: el dels mòduls amb els quals estem treballant:

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


Veurem com `curs` és un array o una llista de mòduls (encara que ara no utilitzem l'etiqueta "modul"), que en aquest cas són objectes amb tres elements: el nom, que és una cadena de caràcters, les hores, que és un enter, i la qualificació, que es representa com un nombre decimal. Cal destacar que, com en XML, també necessitem un objecte arrel, en aquest cas l'element "curs".

Internet està ple de serveis que ofereixen informació en format JSON. Per exemple, podeu visitar:


- <https://arkhamdb.com/api/public/card/01001>
- <https://swapi.dev/api/films/1/>
- <http://hp-api.herokuapp.com/api/characters>
  
Hi ha una àmplia gamma de llibreries de Java per manipular documents JSON (GSON, Jackson, JSON.simple...). En el nostre cas, utilitzarem la llibreria `org.json`, que podem consultar al repositori Maven: <https://mvnrepository.com/artifact/org.json/json>

A la següent secció comentarem la funcionalitat de la llibreria, i en un document adjunt, veurem com incorporar-la als nostres projectes mitjançant el gestor de dependències Gradle.

## 1.2. `org.JSON`

La biblioteca proporciona un conjunt de classes per analitzar documents JSON en Java, així com convertidors entre JSON i XML. Entre les classes que ofereix, podríem destacar:

- `JSONObject` → Emmagatzema parells **clau-valor** de forma desordenada. Els valors poden ser Boolean, JSONArray, Number, String, JSONObject i `NULL`. Els constructors prenen diferents representacions (String, mapes, beans) i els emmagatzemen com un conjunt d'elements clau-valor.
- `JSONTokener` → Analitza una cadena JSON i s'utilitza internament per JSONObject i altres classes per analitzar cadenes JSON.
- `JSONArray` → Emmagatzema una seqüència de valors i representa un array JSON.
- `JSONWriter` → Proporciona una forma de produir text JSON. Té un mètode `append(String)` que afegeix més text a un objecte JSON de tipus text, a més dels mètodes `key(String)` i `value(String)` per afegir claus i valors a una cadena JSON. La classe també permet escriure un array.


## 1.3. Creació de fitxers JSON

Els nostres exemples es basen en les dades dels mòduls de DAM vistos a les seccions anteriors. Considera aquest bloc de codi com a punt de partida on es crea un array `Curs` i s'omple amb mòduls:


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


Per crear el fitxer JSON complet, hem de crear JSON individuals que representin cada objecte. A causa que aquest objecte obté la informació dels objectes, és una bona idea crear mètodes dins de l'objecte del mòdul per obtenir la representació JSON de cada objecte:


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


Un cop resolt el JSON individual, hem de crear el JSON principal, aquell que conté tots els mòduls. És fàcil de suposar que l'element principal serà un array, que contindrà tots els objectes de mòdul a l'interior.


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


Finalment, necessitem guardar-ho en un fitxer de text al disc. Aquest bloc de codi emmagatzema un JSONObject utilitzant un FileWriter amb una indentació específica:

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


## 1.4. Lectura de fitxers JSON

Per tal de llegir fitxers JSON, hem de dividir la tasca en dues parts. En primer lloc, i molt senzill, és llegir del fitxer a una cadena de text i cridar al constructor de JSONObject amb aquesta cadena. Després, hem de processar-la.


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


Un cop tenim el principal `JSONObject`, necessitem recuperar el `JSONArray` (anomenat _curs_). Podem utilitzar el mètode length() per recórrer tots els mòduls amb un bucle `for`. Per obtenir els objectes individuals d'un JSONObject, s'utilitza el mètode `get(string label)`, que retorna l'objecte etiquetat. Cal tenir cura del tipus i fer conversions quan sigui necessari. En l'exemple anterior, només mostrem el JSON, en lloc de crear objectes a la memòria.


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
