# 1. Extra formats

En aquesta secció estudiarem dos tipus de fitxers de text que s'utilitzen àmpliament en informàtica i la forma de treballar amb ells.

## 1.1. Fitxers CSV

Un fitxer de **valors separats per comes** (CSV) és un fitxer de text estàndard que utilitza una coma (`,`) per separar els valors. Cada línia del fitxer consisteix en un o més camps separats per comes. Cada camp pot o no estar tancat entre cometes dobles. A més, diversos formats utilitzen diferents caràcters com a separador, com el punt i coma (`;`) o el símbol de numeral (`#`). El [RFC 4180](https://tools.ietf.org/html/rfc4180) defineix el format o les definicions d'un fitxer CSV o fitxer de text/csv.

En cas que el símbol utilitzat com a separador aparegui dins dels valors, és una bona idea tancar el contingut entre cometes. Es pot veure un exemple de fitxer CSV aquí:

```csv
Chevrolet Chevelle Concours (sw);0;8;350.0;165.0;4142.;11.5;70;US
Ford Torino (sw);0;8;351.0;153.0;4034.;11.0;70;US
Plymouth Satellite (sw);0;8;383.0;175.0;4166.;10.5;70;US
AMC Rebel SST (sw);0;8;360.0;175.0;3850.;11.0;70;US
Dodge Challenger SE;15.0;8;383.0;170.0;3563.;10.0;70;US
Plymouth Cuda 340;14.0;8;340.0;160.0;3609.;8.0;70;US
Ford Mustang Boss 302;0;8;302.0;140.0;3353.;8.0;70;US
```

La manera de processar un fitxer CSV en Java és:

1. Obrir el fitxer com a fitxer de text per a la lectura. Hem de llegir línia per línia, ja que cada línia és un registre. Llegir una línia en una variable de tipus `String`.
2. Processar els registres individuals:
    1. Podem separar cada camp. Una bona opció és utilitzar el mètode `split(char)` de la classe String. Obtinguem un array de cadenes amb els valors dels camps individuals.
    2. Processar cada valor de camp segons les necessitats.


!!! note "Atenció" 

    És una bona idea començar a utilitzar les classes abstractes `Files` i `Paths`. Aquestes classes milloren l'ús de la classe File i les seves classes derivades, oferint mètodes útils que ens permeten realitzar operacions ràpides amb menys línies de codi. Per exemple, la següent línia, a partir del `filename`, l'obre i després llegeix tot el fitxer, retornant una llista amb les línies separades en cada element de la col·lecció.

    ```java
    List<String> lines=Files.readAllLines(Paths.get(filename));
    ```

    More info in:

    - [Files](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/nio/file/Files.html)
    - [Paths](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/nio/file/Paths.html)

## 1.2. Fitxers de propietats

### 1.2.1. Fitxer de propietats

Finalment, però no menys important, us mostrarem un tipus de fitxer de text important, que són els fitxers de propietats. Aquests fitxers emmagatzemen, com ja sabeu, diverses propietats que s'utilitzen durant l'execució del programa. Quan el programa s'inicia, carrega aquestes propietats i ajusta diverses opcions. Exemples d'aquests fitxers són `my.conf` a MySQL, `php.ini` a PHP, etc.

L'aspecte d'aquest fitxer consisteix en diverses línies (una per propietat) i cada línia té el format `atribut=valor`. Per exemple, un suposat fitxer:


```conf
# properties of my program
port=1234
volume=90
bright=56
load_on_start=true
```


La manera de processar un fitxer de propietats en Java és similar a un CSV:

1. Obrir com a fitxer de text per a la lectura. Hem de llegir línia per línia, ja que cada línia és una propietat diferent. Llegir una línia en una variable de tipus `String`.
2. Processar els registres individuals:
    1. Podem separar cada camp. Una bona opció és utilitzar el mètode `split(char)` de la classe String. Obtinguem un array de cadenes amb els valors dels camps individuals. Els separadors normalment són `=`, `:`.
    2. L'esquerra del separador és el nom de la propietat.
    3. La dreta del separador és el valor de la propietat.
    4. Cal tenir en compte que si una línia comença amb barra (`#`), és un comentari i s'ignorarà.


### 1.2.2. Propietats de Java

Java té un objecte molt útil per gestionar aquest tipus d'informació. Amb els objectes [preperties](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Properties.html) de Java, podem emmagatzemar un conjunt de propietats amb una taula de hash (bàsicament una parella clau-valor). Té mètodes preparats per carregar i desar des de i cap a fluxos de text o, fins i tot, fitxers XML. Alguns mètodes interessants per a les propietats són:

**Lectura des de fitxer (text o XML)**

- `void load(InputStream inStream)` → Llegeix un conjunt de propietats (parelles clau-element) des del flux d'entrada de bytes.
- `void load(Reader reader)` → Llegeix un conjunt de propietats (parelles clau-element) des del flux d'entrada de caràcters en un format senzill orientat a línies.
- `void loadFromXML(InputStream in)` → Carrega totes les propietats representades pel document XML en el flux d'entrada especificat en aquesta taula de propietats.

**Escriptura en fitxer (text o XML)**

- `void store(OutputStream out, String comments)` → Escriu aquest conjunt de propietats (parelles clau-element) en aquesta taula de propietats al flux de sortida en un format adequat per carregar-lo en una taula de propietats utilitzant el mètode load(InputStream).
- `void store(Writer writer, String comments)` → Escriu aquest conjunt de propietats (parelles clau-element) en aquesta taula de propietats al flux de caràcters de sortida en un format adequat per utilitzar el mètode load(Reader).
- `void	storeToXML(OutputStream os, String comment)` → Emmet un document XML que representa totes les propietats contingudes en aquesta taula.

**Treball amb propietats (heretat de HashTable)**

- `Set<K> keySet()`	→ Retorna una vista de conjunt de les claus contingudes en aquest mapa.
- `V get(Object key)` → Retorna el valor (`V`) al qual s'assigna la clau especificada, o `null` si aquest mapa no conté cap assignació per a la clau.
- `boolean	containsKey(Object key)` Comprova si l'objecte especificat és una clau d'aquesta taula de hash.
- `V put(K key, V value)` → Assigna la clau especificada al valor especificat en aquesta taula de hash.
- `V remove(Object key)` → Elimina la clau (i es retorna el valor corresponent) d'aquesta taula de hash.

### 1.2.3. Programa d'exemple

En el següent programa, podeu veure un exemple de lectura i creació de fitxers de propietats en Java.

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

## 1.3. Entorn, arxius `.env` i `dot_env`

### 1.3.1. Per què utilitzar variables d'entorn?

Les variables d'entorn són un aspecte crucial per configurar i gestionar aplicacions en diferents entorns, com ara **desenvolupament, proves i producció**. Proporcionen una manera de passar dinàmicament dades de configuració a les aplicacions sense codificar valors directament al codi font. Aquest enfocament millora la **flexibilitat**, **portabilitat** i **seguretat** de les aplicacions.

En Java, accedir a les variables d'entorn és senzill, gràcies a la classe `System`, que proporciona mètodes per obtenir les variables d'entorn. Aquesta capacitat és especialment útil per a:

- Configurar paràmetres de l'aplicació com ara URL de bases de dades, claus d'API i rutes de fitxers.
- Gestionar configuracions específiques de l'entorn sense alterar el codi font.
- Mantenir informació sensible, com ara contrasenyes i tokens, fora del codi font.

Entenent com utilitzar de manera efectiva les variables d'entorn en Java, els desenvolupadors poden crear aplicacions més adaptables i segures.

### 1.3.2. Obtenir variables d'entorn

Per accedir a les variables d'entorn en Java, podeu utilitzar el mètode `System.getenv()`. Aquest mètode està sobrecarregat i pot ser:

- `System.getenv()` → retorna un objecte `Map` que conté totes les variables d'entorn i els seus valors corresponents.
- `System.getenv(String)` → retorna una `String` amb el valor de la variable d'entorn específica o null si no existeix.

!!! example "Com recuperar les variables d'entorn en Java:"

    ```java
    Map<String, String> env = System.getenv();

    // Accedir a una variable d'entorn específica
    String value = env.get("NOM_VARIABLE");

    // Imprimir totes les variables d'entorn
    for (Map.Entry<String, String> entry : env.entrySet()) {
        System.out.println(entry.getKey() + " = " + entry.getValue());
    }
    ```

    > Tingueu en compte que:
    > 
    > - La classe Map retorna una col·lecció d'entrades amb parelles clau-valor.
    > - Les variables d'entorn distingeixen entre majúscules i minúscules, així que assegureu-vos d'utilitzar les majúscules de manera correcta en accedir-hi.


### 1.3.3. L'alternativa `dot-env`

Emmagatzemar la configuració a l'entorn és un dels principis d'una aplicació de dotze factors. Qualsevol cosa que pugui canviar entre els entorns de desplegament, com ara els recursos de les bases de dades o les credencials dels serveis externs, s'ha d'extreure del codi i emmagatzemar en variables d'entorn.

Però no sempre és pràctic establir variables d'entorn en màquines de desenvolupament o servidors d'integració contínua on s'executen diversos projectes. `Dotenv` carrega les variables d'un fitxer `.env` a l'entorn quan s'inicia.

Desafortunadament, Java no admet de forma nativa la càrrega de fitxers `.env`, però podeu utilitzar llibreries externes per aconseguir aquesta tasca. Una de les llibreries populars per a aquest propòsit és `dotenv-java`. Aquesta llibreria us permet carregar fàcilment les variables d'entorn d'un fitxer .env a la vostra aplicació Java. <https://github.com/cdimascio/dotenv-java>

**Afegiu la dependència**

Primer, heu d'afegir la llibreria `dotenv-java` al vostre projecte. Si feu servir Maven, afegiu la següent dependència al fitxer pom.xml:

```xml
<dependency>
    <groupId>io.github.cdimascio</groupId>
    <artifactId>java-dotenv</artifactId>
    <version>5.2.2</version>
</dependency>
```

Si feu servir Gradle, afegiu el següent al fitxer build.gradle:

```
implementation 'io.github.cdimascio:java-dotenv:5.2.2'
```


**Creeu el fitxer `.env`**

El fitxer .env és com un fitxer de configuració. Podeu emmagatzemar-lo a la ruta arrel de la vostra aplicació

```conf
DATABASE_URL=jdbc:mysql://localhost:3306/testdb
DATABASE_USER=root
DATABASE_PASSWORD=password
```

**Carregueu la configuració**


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


!!! info "Atenció"
    Tingueu en compte que amb la llibreria java dotenv, utilitzareu `dotenv.get()` en comptes del mètode `System.getenv()`, ja que esteu accedint a un fitxer local.


     
