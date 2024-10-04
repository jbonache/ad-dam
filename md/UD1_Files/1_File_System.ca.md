# 1. Sistema de fitxers

## 1.1. Introducció

En els inicis de la informàtica, els fitxers eren l'únic mecanisme per emmagatzemar informació. Avui en dia, els sistemes operatius gestionen els dispositius d'emmagatzematge de manera gairebé transparent per a l'usuari. Sense entrar en detalls de les tecnologies de emmagatzematge subjacents, ens ofereixen una abstracció mitjançant la qual només hem de preocupar-nos de dos conceptes: fitxers i directoris.


- **Fitxer** → contenidors d'informació (de tot tipus i format).
- **Directoris** → organitzadors de fitxers, i poden contenir fitxers i altres directoris.

Seguint amb la informació (el contingut del fitxer), podem trobar una classificació clàssica:

- **Fitxer de text** → la informació s'emmagatzema en fitxers, de manera que es pot veure o obrir amb qualsevol editor de text pla, com ara vi, nano o notepad.
- **Fitxer binari** → la informació s'emmagatzema codificada en format binari. Com ja sap l'estudiant, qualsevol tipus d'informació (text, números, fotos, etc.) es pot traduir a 0 i 1. En el nostre cas (programadors), podem transformar objectes i variables de diferents maneres.

!!!important "Cal tenir en compte que..."

    - Tot i que emmagatzemem informació en fitxers de text, apareix un nou concepte: **estructura**. Si organitzem la informació de diferents maneres, podem parlar de fitxers csv, fitxers xml, fitxers json. Els fitxers csv, xml i json són estructures, perquè al cap i a la fi, tots ells són fitxers de text.
    - La gent pensa que els fitxers de text són febles per emmagatzemar informació, però no és cert. Les tècniques criptogràfiques ens ofereixen mètodes per emmagatzemar informació de manera segura dins dels fitxers de text. Per exemple, `htpasswd` a **apache** o `/etc/shadoW` en sistemes Linux. A sota podeu veure un exemple de clau pública PGP, emmagatzemada en un fitxer de text.



```bash
-----BEGIN PGP PUBLIC KEY BLOCK-----
Comment: Alice's OpenPGP certificate
Comment: https://www.ietf.org/id/draft-bre-openpgp-samples-01.html

mDMEXEcE6RYJKwYBBAHaRw8BAQdArjWwk3FAqyiFbFBKT4TzXcVBqPTB3gmzlC/U
b7O1u120JkFsaWNlIExvdmVsYWNlIDxhbGljZUBvcGVucGdwLmV4YW1wbGU+iJAE
...
DAAKCRDyMVUMT0fjjlnQAQDFHUs6TIcxrNTtEZFjUFm1M0PJ1Dng/cDW4xN80fsn
0QEA22Kr7VkCjeAEC08VSTeV+QFsmz55/lntWkwYWhmvOgE=
=iIGO
-----END PGP PUBLIC KEY BLOCK-----
```

## 1.2. Accedint al sistema de fitxers en Java

`Java` ens ofereix diverses maneres d'accedir al sistema de fitxers. Aquesta manera és independent del dispositiu subjacent que emmagatzema la informació. El dispositiu pot ser un disc dur, un disc ssd, una unitat òptica, etc. La classe que ens ofereix aquesta possibilitat és la classe [File](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/io/File.html). Repassarem els principals aspectes d'aquesta classe i després revisarem alguns exemples.

- El constructor de la classe està sobrecarregat, com la majoria de les classes de Java. Podem utilitzar:
  
    - `File(File parent, String child)` → Crea una nova instància de File a partir d'un directori pare abstracte i una cadena de directori fill.
    - `File(String pathname)` → Crea una nova instància de File convertint la cadena de directori donada en un directori abstracte.
    - `File(String parent, String child)` → Crea una nova instància de File a partir d'una cadena de directori pare i una cadena de directori fill.
    - `File(URI uri)` → Crea una nova instància de File convertint el file: URI donat en un directori abstracte.

- Per accedir al fitxer hem d'utilitzar un _nom de ruta_. Aquesta és la ruta, i tenim dues possibilitats:
  
    - **Ruta absoluta** → la ruta des del directori arrel del sistema fins al fitxer que volem, començant amb `/` en sistemes Linux o `letra:` en sistemes Windows. Recorda utilitzar `/` per separar carpetes en Linux i `\\` en Windows. No és un error, ja que la barra invertida és un símbol amb un significat propi, hem d'escapar-la, duplicant-la per obtenir el significat que volem.
    - **Ruta relativa** → suposem que el fitxer que volem es troba a la mateixa carpeta que el projecte/programa que estem executant. En aquest cas, només proporcionem el nom del fitxer, sense cap carpeta al principi de la ruta.

```java
// absolute paths
File f=new File("/home/joange/texto.md") //  Linux
File f=new File("C:\\Usuarios\\joange\\Dektop\\texto.md") //  Windows
...
// relative paths
File f=new File("texto.md") //  Linux
File f=new File("docs\\texto.md") //  Windows
```

- La mateixa classe `File` s'utilitza per accedir tant a un fitxer regular com a un directori. És tasca del programador diferenciar-los mitjançant els mètodes de fitxer, així com comprovar si un fitxer existeix. Per fer això, podem utilitzar diversos mètodes com:
    - `boolean exists()` → retorna true si l'objecte de fitxer existeix i false en cas contrari.
    - `boolean isFile()` i `isDirectory()` → retorna true si l'objecte és un fitxer regular o un directori. Òbviament, aquests mètodes són exclusius.


## 1.3. Creant fitxers

Si volem crear una carpeta o un fitxer regular, podem utilitzar aquests mètodes:

- `boolean createNewFile()` → Crea de manera atòmica un nou fitxer buit amb el nom especificat si i només si un fitxer amb aquest nom encara no existeix.
- `static FilecreateTempFile(String prefix, String suffix)` → Crea un fitxer buit al directori temporal per defecte, utilitzant el prefix i el sufix especificats per generar el seu nom.
- `boolean mkdir()` → Crea el directori amb el nom especificat.
- `boolean mkdirs()` → Crea el directori amb el nom especificat, incloent qualsevol directori pare necessari però que no existeixi.
- `boolean renameTo(File dest)` → Canvia el nom del fitxer especificat.
- `boolean delete()` → Elimina el fitxer o directori especificat.

!!! note "Atenció"
    - En les seccions següents estudiarem com crear fitxers quan hi guardem contingut. En altres paraules, no cal crear el fitxer _ad hoc_ i després omplir-lo. Hi ha mecanismes per crear el fitxer de manera automatitzada.
    - La diferència entre mkdir i mkdirs és que la segona opció crearà tots els directoris entre el sistema de fitxers arrel i el directori actual, mentre que la primera requereix que la ruta ja existeixi.


## 1.4. Demanant propietats del fitxer

Normalment obrim fitxers per escriure o llegir el seu contingut, però a vegades necessitem demanar les propietats del fitxer, com ara la mida, els permisos, etc. La classe File ens ajudarà de nou.

Informació general:

- `boolean exists()` → Comprova si el fitxer o directori indicat per aquesta ruta abstracta existeix.
- `long lastModified()` → Retorna el temps en què es va modificar per última vegada el fitxer indicat per aquesta ruta abstracta.
- `long length()` → Retorna la mida del fitxer indicat per aquesta ruta abstracta.

Informació sobre els permisos. Aquesta informació és la mateixa que l'usuari pot obtenir amb `ls -la` en el format de `chmod`:

- `boolean canExecute()` → Comprova si l'aplicació pot executar el fitxer indicat per aquesta ruta abstracta.
- `boolean canRead()` → Comprova si l'aplicació pot llegir el fitxer indicat per aquesta ruta abstracta.
- `boolean canWrite()` → Comprova si l'aplicació pot modificar el fitxer indicat per aquesta ruta abstracta.

Contingut d'un directori:

- `String[] list()` → Retorna un **array de cadenes** amb els noms dels fitxers i directoris del directori indicat per aquesta ruta abstracta.
- `File[] listFiles()` → Retorna un array de rutes abstractes que indiquen els fitxers del directori indicat per aquesta ruta abstracta.
- `String[] list(FilenameFilter filter)` → Retorna un array de cadenes amb els noms dels fitxers i directoris del directori indicat per aquesta ruta abstracta que compleixen el filtre especificat.
- `File[] listFiles(FilenameFilter filter)` → Retorna un array de rutes abstractes que indiquen els fitxers i directoris del directori indicat per aquesta ruta abstracta que compleixen el filtre especificat.

!!! tip "Consell"
    Suggerim a l'estudiant que busqui informació sobre la classe [FilenameFilter](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/io/FilenameFilter.html).


### 1.4.1. Exercici resolt

Analitza i explica el següent bloc de codi
```java
public static void main(String[] args) {
        String ruta = args[0];
        File f = new File(ruta);

        if (f.exists()) {
            if (f.isFile()) {
                System.out.println("El tamaño es de " + f.length());
                System.out.println("Puede ejecturase: " + f.canExecute());
                System.out.println("Puede leerse: " + f.canRead());
                System.out.println("Puede escribirse: " + f.canWrite());
            } else {
                String[] losArchivos = f.list();
                System.out.println("El directorio " + ruta + " contiene:");
                for (String archivo : losArchivos) {
                    System.out.println("\t" + archivo);
                }
            }

        } else {
            System.out.println("El fichero o ruta no existe");
        }
}
```

**Solució**

1. Aquest programa llegeix des de la línia de comandes una ruta i crea un objecte File.
2. A continuació, comprova si la ruta existeix o no al sistema de fitxers. En cas que no existeixi, el programa finalitza.
3. Si la ruta existeix, el següent pas és verificar si és un fitxer:
    1. Llegim algunes propietats trivials com la mida i els permisos.
4. Si la ruta apuntada pel fitxer és un directori, obtenim el contingut d'aquest directori i el mostrem a la pantalla.


<!-- 
Buscar y reemplaçar $\rightarrow$ per  → (simbol del sistema)
Canviar els awesomebox per !!! a l'inici, llevar els de darrere i tabular els continguts. Podem donar-li titol
-->