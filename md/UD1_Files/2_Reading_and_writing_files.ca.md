# 1. Lectura i escriptura de fitxers

Java ofereix una gran varietat de maneres de manipular el contingut dels fitxers, aportant gran poder però també complexitat al mateix temps.

La lectura i escriptura de fitxers en Java es fa a través de fluxos de dades o `streams`, que poden ser orientats a bytes o considerats com un conjunt de caràcters. El concepte de fitxer, que és estàtic, ha de diferenciar-se del concepte de flux, que té un caràcter dinàmic: un fitxer es guarda al disc, però des del punt de vista de l'aplicació, ens interessa la transferència d'aquesta informació des del fitxer al nostre programa. Una comparació clàssica: els fluxos serien com canonades d'aigua, mentre que els fitxers serien dipòsits. El concepte de **flux de dades**, a part de l'àmbit dels fitxers, també serà aplicable a la transferència d'informació, per exemple a través de la xarxa o entre processos.

Els fluxes es poden classificar en:

- `flux d'entrada`: Aquells que van des d'una font (per exemple, un fitxer) al programa.
- `flux de sortida`: Aquells que surten del programa cap a una destinació (per exemple, un fitxer).

Quan llegim i emmagatzemem dades, hem de tenir molt de compte amb els tipus de dades amb els quals treballem, ja que la mateixa seqüència de bits al disc representa informació diferent segons el tipus de dades utilitzat. Inclos quan es tracta del mateix tipus de dades, com en el cas dels caràcters, cal tenir cura especial, ja que diferents sistemes poden utilitzar diferents codificacions. Java, per exemple, per al tipus char utilitza Unicode de 16 bits (UTF-16), però podem intentar accedir a fitxers codificats, per exemple, amb UTF-8 o ASCII.

Les superclasses pare per al maneig de corrents orientats a bytes són `InputStream` i `OutputStream`. D'elles en deriven altres, però per a la gestió de fitxers ens interessen dues: `FileInputStream` i `FileOutputStream`.


## 1.1. FileInputStream

La classe `FileInputStream` s'utilitza per accedir a fitxers per a la lectura i té dos constructors principals:

- `FileInputStream (File f)` → Rep un objecte de tipus `File`, que farà referència a l'objecte que llegirem.
- `FileInputStream (String name)` → Rep una cadena amb el nom o la ruta de la ubicació del fitxer a llegir.

Els principals mètodes que tenim per a aquesta classe són:

- `int read()` → Mètode de lectura seqüencial abstracte: Retorna un enter corresponent al següent byte d'un flux d'entrada (fitxer, vector de bytes...). Si arriba al final de la seqüència, retorna `-1`. En cas d'error, llançarà una excepció de tipus `IOException`.
- `int read(byte[] buffer)` → Llegeix un nombre determinat de bytes de l'entrada (tant com el tamany del buffer), els guarda al buffer i retorna el nombre de bytes llegits efectivament, que com a màxim serà igual al tamany del buffer. Si no hi ha bytes disponibles, retorna `-1`.
- `int available()` → Indica els bytes disponibles per a la lectura.
- `long skip(long des)` → Salta tants bytes com indica el paràmetre. El valor de retorn és el nombre de bytes que s'han descartat efectivament (pot ser menor que el que hem indicat si arribem al final, per exemple).
- `int close()` → Tanca el flux de dades.

## 1.2. FileOutputStream

La classe `FileOutputStream` s'utilitza per accedir a fitxers per a l'escriptura. Té els següents constructors:

- `FileOutputStream (File f)` → Rep un objecte de tipus `File` i l'obre en mode escriptura. En cas que no existeixi, es crearà, i si ja existeix, es sobreescriurà.
- `FileOutputStream (String name)` → Rep una cadena amb el nom o la ruta de la ubicació del fitxer a escriure, i l'obre en mode escriptura. En cas que no existeixi, es crearà, i si ja existeix, es sobreescriurà.
- `FileOutputStream (File f, boolean append)` → Rep un objecte de tipus `File` i l'obre en mode afegir, per escriure al final. Si no existeix, es crearà, i si ja existeix, s'escriurà darrere (només si append està establert a `True`).
- `FileOutputStream (String name, boolean append)` → Rep una cadena amb el nom o la ruta de la ubicació del fitxer a escriure i l'obre en mode afegir, per escriure al final. Si no existeix, es crearà, i si ja existeix, s'escriurà darrere (només si append està establert a `True`).

Els principals mètodes de la classe són:

- `write(int byte)` → Escriu el byte al flux de sortida. Encara que aquest paràmetre és un enter, només s'escriu un byte. Si hi ha un error, es llançarà una excepció de tipus `IOException`.
- `void write(byte[] buffer)` → Escriu el contingut del buffer (vector de bytes) al fitxer. Si el buffer és nul, llançarà una excepció.
- `void write(byte[] buffer, int pos, int length)` → Escriu el contingut del `buffer` (vector de bytes) des de la posició `pos`, i tants bytes com s'indiquin en `length`.
- `void flush()` → Força l'escriptura dels bytes restants a la memòria cau al fitxer de sortida.
- `void close()` → Tanca el flux de sortida i allibera els recursos.
  
### 1.2.1. Exercici resolt

Crea un programa senzill en Java que copie un fitxer a un altre fitxer byte a byte. El fitxer origen i el fitxer destí s'indicaran a través de la línia de comandes.


!!! example "Solució"

    ```java
    class FileCopy {
        /*
        Class to test FileInputStream and FileOutputStream. 

        Copy byte to byte of files

        Sintaxi:
            FileCopy sourceFile destinationFile.

        */
        public static void main(String[] args) throws Exception {
            // Byte readed from source
            int bytes;        
            // Bytes (effectively) writen to dest
            long bytesCopied=0; 
            
            // Streams 

            FileInputStream fis= null;
            FileOutputStream fos=null;
            
            // To provide information about source
            File f; 


            // Are the arguments ok?
            if(args.length!=2){
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n FileCopy fitxerOrigen fitxerDesti");
                return;
            }

            try{

                // show source size
                f=new File(args[0]);
                System.out.println("Total: "+f.length()+" bytes");

                // Create streams          
                fis=new FileInputStream(args[0]);
                fos=new FileOutputStream(args[1]);

                do {
                    // read one byte from source
                    bytes=fis.read();                 
                    // write in destination
                    if (bytes!=-1)
                        fos.write(bytes);
                    // Update number of bytes
                    bytesCopied++;                    

                    // Show progress (think alternatives as exercise)
                    System.out.print("\rCopiats "+(bytesCopied-1)+" bytes...");
                }while (bytes!=-1);
                System.out.println("Done it!");


            }catch (IOException exc){
                System.out.println("Error d'entrada i eixida: "+exc);
            }finally {
                // At the end, we have to close the files, either an error exists or not.
                try {
                    if (fis!=null) fis.close();
                }catch (IOException exc){
                    System.out.println("Error en tancar el fitxer d'origen.");
                }
                try {
                    if(fos!=null) fos.close();
                }catch (IOException exc){
                    System.out.println("Error en tancar el fitxer destí.");
                }
            }
        }
    }
    ```


!!! note "Pensa i comprova"

    Amb el programa d'exemple que hem vist, podríem copiar fitxers de tot tipus: text, àudio, vídeo. Prova-ho tu mateix i comprova que el contingut és el mateix.

    **Repte**

    Intenta canviar l'últim programa per llegir les dades en blocs de 32 bytes, utilitzant els mètodes adequats de les classes. Comparteix la teva solució al fòrum.


## 1.3. Fitxers de text

Com s'ha dit, Java permet gestionar fluxos amb orientació a bytes o amb orientació a caràcters. Les classes abstractes per a la gestió de fluxos orientats a caràcters són `Reader` i `Writer`. Aquestes classes deriven d'altres, però per a la gestió de fitxers ens centrem en `FileReader` i `FileWriter`.

### 1.3.1. `FileReader`

La classe `FileReader` serveix per accedir a fitxers per a la lectura i té dos constructors:

- `FileReader (File f)` → Rep un objecte de tipus `File`, que farà referència a l'objecte del qual s'obtindrà la informació.
- `FileReader (String name)` → Rep una cadena amb el nom o la ruta del fitxer.

Els mètodes que tenim per a aquesta classe són molt semblants als que tenim per a `FileInputStream`, amb la diferència que ara llegim caràcters en comptes de bytes. Un aspecte a tenir en compte és que aquests caràcters es representaran en el format del sistema operatiu amfitrió (UTF-8 en el cas de Linux), a diferència de l'UTF-16 utilitzat per altres classes d'emmagatzematge intern:

- `int read()` → Llegeix el següent caràcter del flux d'entrada i el retorna com a enter. Si no queden caràcters, retorna -1, i si hi ha un error, llança una excepció de tipus IOException.
- `int read(char[] buffer)` → Omple el buffer amb tants caràcters de l'entrada com tingui (com a màxim). Retorna el nombre de caràcters llegits efectivament. Aquest nombre serà la longitud del buffer o menys si no hi ha prou caràcters per llegir. Si no n'hi ha cap, retorna -1.
- `int available()` → Retorna el nombre de caràcters disponibles per a la lectura.
- `long skip(long des)` → Salta tants caràcters com indica el paràmetre. El valor de retorn és el nombre de caràcters que s'han descartat efectivament (pot ser menor que el que hem indicat si arribem al final, per exemple).
- `int close()` → Tanca el flux de dades.

### 1.3.2. `FileWriter`

La classe FileWriter seria l'equivalent de `FileOutputStream` en la versió de fluxes orientats a caràcters. Els constructors de la classe són:

- `FileWriter (File f)` → Obre el fitxer especificat per `File` per a l'escriptura. Si el fitxer no existeix, es crearà, i si ja existeix, es borraran els continguts.
- `FileWriter (String name)` → Obre el fitxer especificat per una cadena amb el nom i la ruta en mode escriptura. Si no existeix, es crearà, i si ja existeix, es borraran els continguts.
- `FileWriter (File f, boolean append)` → Rep un objecte de tipus `File` i l'obre en mode afegir, per escriure al final. Si no existeix, es crearà, i si ja existeix, s'escriurà darrere.
- `FileWriter (String name, boolean append)` → Rep una cadena amb el nom o la ruta de la ubicació del fitxer a escriure i l'obre en mode afegir, per escriure al final. Si no existeix, es crearà, i si ja existeix, s'escriurà darrere.

Els principals mètodes de la classe `FileWriter` són molt similars als de `OutputStream`:

- `write(int character)` → Escriu el caràcter al flux de sortida, amb la codificació pròpia del sistema operatiu. Si hi ha un error, es llança una excepció de tipus `IOException`.
- `void write(char[] buffer)` → Escriu el contingut del buffer (vector de caràcters) al fitxer. Si el buffer és nul, llançarà una excepció.
- `void write(char[] buffer, int pos, int length)` → Escriu el contingut del buffer (array de caràcters) des de la posició pos, i tants bytes com s'indiquin en length.
- `void flush()` → Força l'escriptura dels bytes restants a la memòria cau al fitxer de sortida.
- `void close()` → Tanca el flux de sortida i allibera els recursos.
- `void write(String text)` → Escriu tot el contingut de text al fitxer.

### 1.3.3. Exercici resolt

Crea un programa senzill en Java que fusioni tots els fitxers dins d'una carpeta en un únic fitxer. La carpeta d'origen i el fitxer de destinació s'indicaran en la crida del programa. Suposem que tots els fitxers dins d'aquesta carpeta són fitxers de text.

!!! example "Solució"

    ```java
    class FusionaTextos {
    // Codi del programa
    }
    ```     /*


        Sintax:
            MergeTexts DirectoriOrigen FitxerDestí

        */
        public static void main(String[] args) throws Exception {

            File dir; // Source dir
            // Collection of files from that dir
            File[] files; 

            // readed characters
            int characters;

            // Input and Output Streams 
            FileReader fin=null;
            FileWriter fout=null;

            // Check the args
            if(args.length!=2){
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n mergeTexts DirectoriOrigen fitxerDesti");
                return;
            }

            try{

                // We get the list of Files
                dir=new File(args[0]);
                files=dir.listFiles();


                // Open and close output stream (in order to create the file)
                fout=new FileWriter(args[1]);
                fout.close();
                
                // Re-open it
                fout=new FileWriter(args[1], true);

                // Iterate among the list
                for (int i=0; i<files.length; i++){
                    // open input stream
                    fin=new FileReader(args[0]+"/"+files[i].getName());
                    System.out.println("Merging "+args[0]+"/"+files[i].getName());
                    // and merge to the output one
                    do {
                        characters=fin.read();                 
                        if (characters!=-1)
                            fout.write(characters);
                    }while (characters!=-1);
                    fin.close(); //close the file merged

                }
                fout.close(); //close the output file

            }catch (Exception exc){
                // Catch all the exception (we coud improve it)
                System.out.println("Input/Output error: "+exc);
            }
        }
    }
    ```

!!! note "Millora el teu codi"

    Intenta millorar el codi anterior creant una funció `merge()`. Hauríem de cridar-la dins del bucle principal.



## 1.4. Decoradors

[Patró de disseny decorador](https://refactoring.guru/es/design-patterns/decorator/)

Les classes de decorador són aquelles que hereten d'una certa classe i proporcionen funcionalitats afegides a l'original. En el cas dels fluxos d'entrada i sortida, tenim decoradors que ens permeten llegir o escriure línies completes en comptes de byte a byte, o guardar en un cert format de dades. Simplifica la nostra feina, afegint una manera més natural i amigable per a utilitzar les classes base.

La classe `InputStream` té diversos decoradors, però ens centrarem en els següents:

- `DataInputStream` → Permet llegir dades de qualsevol tipus (enter, lògic, etc.)
- `ObjectInputStream` → Afegeix la opció de llegir un objecte sencer

D'altra banda, la classe `OutputStream` també té diferents decoradors, entre els quals destaquem:

- `DataOutputStream` → Permet escriure dades de qualsevol tipus (enter, lògic, etc.)
- `PrintStream` → Permet escriure dades de qualsevol tipus i també accepta els mètodes `printf` i `println`
- `ObjectOutputStream` → Permet escriure (serialitzar) objectes

Pel que fa a les classes de decorador per a fluxos orientats a caràcters, tenim, d'una banda, els decoradors de `Reader` (més destacats):

- `BufferedReader` → Crea un buffer d'entrada, permetent, per exemple, llegir una línia completa

I per a l'escriptura:

- `BufferedWriter` → Crea un buffer de sortida, permetent, per exemple, escriure una línia completa
- `PrintWriter` → Permet escriure dades de diferents tipus i té mètodes com `printf` i `println`

La classe `BufferedReader`, entre altres, té el mètode `readLine()`, que permet llegir una línia sencera del fitxer fins al final de la línia, molt útil en fitxers de text.

Per la seva banda, la classe `BufferedWriter` proporciona el mètode `newLine()` per introduir el caràcter de retorn de carro i el mètode `write(String cadena, int inici, int longitud)` per escriure una cadena o una part específica.

L'altre decorador per a l'escriptura és el `PrintWriter`, que ens ofereix els mètodes `print(dades)`, `println(dades)` i `printf()` per a la formatació.

!!! note "Alternatives a..."

    Òbviament, podríeu:

    - concatenar `\n` al final de cada cadena en cada mètode `print()` en comptes de cridar `newLine()`.
    - utilitzar `println()` en comptes de cridar `newLine()`.



### 1.4.1. Exercici resolt
Crea un programa per copiar un fitxer de text afegint el número de línia al principi de cada línia.

!!! example "Solució"

    ```java
    public class NumberLines {
        public static void main(String[] args) throws Exception {

            // Input and Output
            BufferedReader fin;
            PrintWriter fout;

            // line counter
            int num_linia;
            // readed line
            String linia;

            // check args
            if (args.length != 2) {
                System.out.println("Nombre d'arguments erroni. Sintaxi:\n numberLines fitxer eixida");
                return;
            }

            // Creare decorators
            fin = new BufferedReader(new FileReader(args[0]));
            fout = new PrintWriter(new FileWriter(args[1]));

            num_linia = 1;
            do {
                // Read the line
                linia = fin.readLine();
                if (linia != null) {
                    fout.println(num_linia + ". " + linia);
                }
                num_linia++;
            } while (linia != null); // until we can't read

            // close all
            fin.close();
            fout.close();

        }
    }
    ```
    
## 1.5. Fitxers Binaris

En aquesta secció i les següents veurem com emmagatzemar diferents tipus de dades i objectes en fitxers de diferents formats: fitxers binaris, fitxers XML o JSON. Ara ens centrarem en els fitxers binaris, tant per emmagatzemar dades estructurades, d'una banda, com objectes, de l'altra, en un procés conegut com a serialització.

### 1.5.1. Emmagatzemar dades estructurades en fitxers binaris

En la secció anterior vam veure com treballar amb fitxers de caràcters i de bytes. En el cas dels fitxers de bytes, hem vist com llegir-los i escriure'ls seqüencialment, byte a byte fins al final del fitxer. Ara veurem com emmagatzemar dades estructurades en fitxers binaris i com poder-les llegir.

Imaginem que volem guardar la següent taula, que combina dades de diferents tipus. Aquest exemple apareixerà a les següents seccions.

| Modul | Hores | Qualificació|
|---|-|---|
|Accés a Dades	| 6	|8.45 |
|Programació de serveis i processos	|3	|9.0|
|Desenvolupament d'interfícies	|6	|8.0|
|Programació Multimèdia i dispositius mòbils	|5	|7.34|
|Sistemes de Gestió Empresarial	|5	|8.2|
|Empresa i iniciativa emprenedora	|3	|7.4|

Com podem veure, tenim dades de text, dades numèriques enteres i reals. Si volem mantenir els tipus, els fluxos orientats a caràcters com `Reader` o `Writer` no seran útils, així que hauríem d'utilitzar `InputStream` i `OutputStream`. El desavantatge és que seria necessari saber exactament quantes dades ocupa cada tipus de dada.

Per tal de desar aquestes estructures eficientment, podem fer servir les classes `DataInputStream` i `DataOutputStream`, que són decoradors de fluxos i que ens ofereixen els següents mètodes per desar o recuperar dades de diferents tipus, sense haver de preocupar-nos de quant ocupen internament. Com podeu veure, hi ha mètodes recíprocs per llegir i escriure cada tipus de dada bàsic.

| `DataInputStream`	| `DataOutputStream`	| Descripció|
|---|---|---|
|`byte readByte()`|`void writeByte(int)` |un byte	|
|`short readShort()`	|`void writeShort(short)`	|enter curt |
|`int readInt()`	|`void writeInt(int)`	| un enter|
|`long readLong()`	|`void writeLong(long)`	| un enter llarg|
|`float readFloat()`	|`void writeFloat(float)`	| real de precisió simple|
|`double readDouble()` |`void writeDouble(double)`	|real doble|
|`char readChar()`	|`void writeChar(int)`	| un caràcter Unicode|
|`String readUTF()`	|`void writeUTF(String)`| un String UTF-8 |

!!! tip

    Una cadena `UTF-8` és diferent d'una String. Quan es guarda una cadena, com és evident, es guarda tots els caràcters. En desar-la com a cadena UTF-8, s'afegeix informació sobre la longitud de la cadena, i aquesta informació és essencial per tallar aquestes cadenes quan es llegeixin en el futur.

    Imagina que guardes dues cadenes, "euro" i "sport". El resultat serà, al final, "eurosport". Quan algú obri aquest fitxer en el futur, com sabrà el nombre de cadenes emmagatzemades ("euro", "sport" o simplement "eurosport" (canal de televisió))?

    En desar-ho com a UTF-8, quan guardes "euro" i "sport", el resultat és "4euro5sport". Quan algú intenti llegir-ho, primer veurà un '4' i llegirà "euro". Després, veurà un '5' i llegirà "sport".
    
    Cal destacar que si es guarda "eurosport", el resultat serà "9eurosport". Prova d'escriure un programa d'exemple amb aquesta cadena, obrint el fitxer resultant amb un editor hexadecimal.


### 1.5.2. Exercici resolt

Escriu un programa que tingui les dades emmagatzemades en tres arrays paral·lels, que emmagatzemen les dades dels mòduls de DAM. Escriu dues funcions, una per escriure aquestes dades en un fitxer i una altra per llegir-les. Considera emmagatzemar les dades agrupades per mòdul, en comptes de nom, hores i qualificació.


!!! example "Solució"

    ```java
    public class Moduls{

        // several arrays with modules data
        String[] moduls={"Accés a Dades", "Programació de serveis i processos", "Desenvolupament d'interfícies", "Programació Multimèdia i dispositiud mòbils", "Sistemes de Gestió Empresarial", "Empresa i iniciativa emprenedora"};
        int[]  hores={6, 3, 6, 5, 5, 3};
        double[] notes={8.45, 9.0, 8.0, 7.34, 8.2, 7.4};

        public void readFiLe(String name) throws IOException {
            // Per lleginr el fitxer binari, creem un DataInputStream
            // a partir del FileInputStream creat a partir del nom
            DataInputStream f = new DataInputStream(new FileInputStream(name));

            // Mentre el DataInputStream tinga dades disponibles
            while (f.available()>0){
                // Llegirem del fitxer cada dada, amb l'ordre corresponent
                // en funció del tipus
                // (per tant, hem de saber l'ordre en què guardem!)
                System.out.println("Mòdul: " + f.readUTF());
                System.out.println("Hores: " + f.readInt());
                System.out.println("Notes: " + f.readDouble());
                System.out.println();
            }
            f.close();
        }

        public void writeFile(String name) throws IOException{
            // Per escriure el fitxer, fem ús de DataOutputStream
            DataOutputStream f = new DataOutputStream(new FileOutputStream(name));

            // Recorrerem qualsevol dels vectors (tots haurien de tindre)
            // la mateixa longitud
            for (int i=0;i<this.moduls.length;i++){
                // I per a cada posició, escriurem en funció del tipus de dada
                f.writeUTF(this.moduls[i]);
                f.writeInt(this.hores[i]);
                f.writeDouble(this.notes[i]);

            }
            f.close();
        }

        public static void main(String[] args) throws IOException {

            // Comprovem els arguments
            if (args.length!=2){
                System.out.println("Nombre d'arguments incorrecte.\n\nSintaxi: \n\t java Moduls [read | write] fitxer.dat");
                System.exit(0);
            }

            // Defining the class
            Moduls moduls=new Moduls();

            // Depending the args, we will proceed
            if (args[0].equals("read")) 
            moduls.readFiLe(args[1]);
            else if (args[0].equals("write")) 
            moduls.writeFile(args[1]);
            else 
            System.out.println("No entenc l'ordre "+args[0]+"\n");
        }
    }

    ```

## 1.6. Serialitzant Objectes

Java proporciona un sistema genèric de serialització d'objectes: un sistema recursiu que itera sobre cada objecte contingut en una instància, fins arribar als tipus primitius, que s'emmagatzemen com un array de bytes. A part d'aquesta informació dels tipus primitius, també s'emmagatzema informació addicional o metadades específiques de cada classe, com ara el nom o els atributs, entre d'altres. Gràcies a aquestes metadades, que descriuen els objectes que guardem, podem automatitzar la serialització de manera genèrica, assegurant-nos que posteriorment podrem llegir els objectes.

El desavantatge d'aquest mètode és que quan canviem la definició de la classe (per exemple, afegint un atribut més o canviant el seu tipus), les metadades es modifiquen i no podríem llegir els objectes serialitzats amb versions anteriors de la classe. A més, també cal tenir en compte que aquest és un mecanisme específic de Java i que no podrem consultar aquests objectes des d'altres llenguatges.

Per tot això, altres tècniques són preferibles per a l'emmagatzematge permanent d'objectes, que veurem més endavant, però la serialització pot ser útil per a l'emmagatzematge temporal, dins de la mateixa execució de l'aplicació.

!!! note "Què és això?"
    **Investigació:** intenta trobar informació sobre ***SerialVersionUID*** i per a què és important.

### 1.6.1. La interfície `Serializable` i els `Decorators`

Si volem que una classe sigui serializable, ha de implementar la interfície `Serializable`, la qual té com a únic propòsit actuar com a marcador per indicar al JVM que la classe pot ser serialitzada, per tant, aquesta classe no tindrà mètodes.

Cal dir que totes les classes corresponents als tipus bàsics ja implementen la interfície Serializable, així com la classe String, contenidors i Arrays. En el cas de les col·leccions, depèn dels seus continguts, si els seus elements són serialitzables, la col·lecció també ho serà. Si l'objecte que volem serialitzar o els seus objectes no implementen la interfície Serializable, es llançarà l'excepció `NotSerializableException`.

Els decoradors `ObjectInputStream` i `ObjectOutputStream` ens ofereixen la capacitat de serialitzar qualsevol objecte deserialitzable. Per escriure un objecte, farem ús del mètode `writeObject` de `ObjectOutputStream`, i per llegir-lo farem ús de `readObject` de `ObjectInputStream`.

!!!note "Ovelles amb ovelles"

    Cal tenir en compte que la lectura d'objectes s'ha de fer en instàncies de la mateixa classe que es va guardar. En cas contrari, es llançarà una excepció `ClassCastException`. A més, cal tenir el codi compilat de la classe per evitar l'excepció `ClassNotFoundException`.

    A més, `readObject` retorna un Object, i necessitem un objecte d'una classe específica. Per aquest motiu, cal fer una conversió de Object a la classe necessària. Els conceptes d'herència són molt importants per garantir programes robustos.


### 1.6.2. Exercici resolt

Començant amb la mateixa base de la classe `Moduls` de l'exercici anterior, crearem una classe `Modul` per emmagatzemar un únic mòdul. Aquest tipus de classes es coneixen com a POJO (Plain Old Java Objects) i estan dissenyades només per emmagatzemar informació. (Apareixerà més endavant, juntament amb les classes BEAN).

Un cop creada la classe `Modul`, escriu un programa per desar objectes directament en un fitxer. Després, escriu la funció complementària per llegir tots els objectes emmagatzemats en aquest fitxer.


!!! example "Solució"

    ```java
    /**
        Class to storage a single module in memory
    */

    class Modul implements Serializable{
        String nom;
        int hores;
        double nota;

        public Modul(){
            // Constructor buit
        }

        public Modul(String nom, int hores, double nota){
            this.nom=nom;
            this.hores=hores;
            this.nota=nota;
        }

        public String getModul() {return this.nom;}
        public int getHores() {return this.hores;}
        public double getNota() {return this.nota;}
    } 

    /**
        Write and Read modules to/from file
    */

    public class Moduls2 {

        // Arrays with source data
        String[] moduls = {"Accés a Dades", "Programació de serveis i processos", "Desenvolupament d'interfícies", "Programació Multimèdia i dispositiud mòbils", "Sistemes de Gestió Empresarial", "Empresa i iniciativa emprenedora"};
        int[] hores = {6, 3, 6, 5, 5, 3};
        double[] notes = {8.45, 9.0, 8.0, 7.34, 8.2, 7.4};

        public void EscriuObjecte(String nom) throws IOException {

            //destination file
            ObjectOutputStream f = new ObjectOutputStream(new FileOutputStream(nom));

            Modul m; // Single object

            // loop along the arrays
            for (int i = 0; i < this.moduls.length; i++) {
                m = new Modul(this.moduls[i], this.hores[i], this.notes[i]);
                f.writeObject(m);
            }

            // close the file
            f.close();

        }

        public void LligObjecte(String nom) throws IOException, ClassNotFoundException {

            // input file
            ObjectInputStream f = new ObjectInputStream(new FileInputStream(nom));

            Modul m;
            // we don't know how many objects exists in the file.
            try {
                while (true) { // forever

                    m = (Modul) f.readObject();

                    // show the module
                    System.out.println("Modul: " + m.getModul());
                    System.out.println("Hores: " + m.getHores());
                    System.out.println("Nota: " + m.getNota());
                    System.out.println();

                }
            } catch (EOFException ex) {
                f.close();
            }

        }

        public static void main(String[] args) throws IOException, ClassNotFoundException {

            // test the args
            if (args.length != 2) {
                System.out.println("Nombre d'arguments incorrecte.\n\nSintaxi: \n\t java Moduls2 [ read | write ] fitxer.obj");
                System.exit(0);
            }

            Moduls2 moduls = new Moduls2();

            // depending the args
            if (args[0].equals("read")) {
                moduls.LligObjecte(args[1]);
            } else if (args[0].equals("write")) {
                moduls.EscriuObjecte(args[1]);
            } else {
                System.out.println("No entenc l'ordre " + args[0] + "\n");
            }

        }

    }
    ```

!!! tip "Less work, but the same in the end"

    You probably will think about it: _If all in Java inherits from Object, an ArrayList is an Object...Can I save or load an entire ArrayList in one unique call?._ Try it as an improvement of the last exercise.

