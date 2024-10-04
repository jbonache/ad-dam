# 1. Arxius XML

## 1.1. Perquè XML?

Quan volem desar dades que puguin ser llegides per diferents aplicacions i plataformes, és millor utilitzar formats d'emmagatzematge estàndard que múltiples aplicacions puguin entendre (portabilitat). Un cas molt específic són els llenguatges de marcatge, i el més conegut és l'estàndard XML (**eXtensible Markup Language**).

Amb els documents XML, estructuram la informació inserint marques o etiquetes entre la informació. Aquestes etiquetes tenen un inici i un final, i poden niuar-se dins d'altres, així com contenir informació textual. Com que la informació serà textual, no tenim el problema de la representació de dades diferents, ja que qualsevol dada, de qualsevol tipus, es convertirà en text. Per evitar també el problema dels diferents sistemes d'codificació de text, XML permet incloure a l'encapçalament del document la codificació utilitzada per desar el document.

La manera de desar informació en XML, de manera jeràrquica, és molt similar a la manera com ho fan els objectes en una aplicació, de manera que aquests es poden traduir de manera relativament convenient en un document XML. Comencem amb el nostre exemple anterior:

| Module | Hours | Qualificació|
|---|-|---|
|Accés a Dades	| 6	|8.45 |
|Programació de serveis i processos	|3	|9.0|
|Desenvolupament d'interfícies	|6	|8.0|
|Programació Multimèdia i dispositius mòbils	|5	|7.34|
|Sistemes de Gestió Empresarial	|5	|8.2|
|Empresa i iniciativa emprenedora	|3	|7.4|

pot representar-se en etiquetes `xml`:

```xml
<curs>
    <modul>
      <nom>Accés a Dades</nom>
      <hores>6</hores>
      <qualificacio>8.45</qualificacio>
    </modul>
    <modul>
      <nom>Programació de serveis i processos</nom>
      <hores>3</hores>
      <qualificacio>9.0</qualificacio>
    </modul>
    <modul>
      <nom>Desenvolupament d'interfícies</nom>
      <hores>6</hores>
      <qualificacio>8.0</qualificacio>
    </modul>
    <modul>
      <nom>Programació Multimèdia i dispositiud mòbils</nom>
      <hores>5</hores>
      <qualificacio>7.34</qualificacio>
    </modul>
    <modul>
      <nom>Sistemes de Gestió Empresarial</nom>
      <hores>5</hores>
      <qualificacio>8.2</qualificacio>
    </modul>
    <modul>
      <nom>Empresa i iniciativa emprenedora</nom>
      <hores>3</hores>
      <qualificacio>7.4</qualificacio>
    </modul>
</curs>
```

o pot ser representat amb etiquetes i atributs:

```xml
<curs>
    <modul nom="Accés a Dades" hores="6" qualificacio="8.45" >
    <modul nom="Programació de serveis i processos" "hores"=3 qualificacio="9.0" >
    <modul nom ="Desenvolupament d'interfícies" hores="6" qualificacio="8.0" >
    </modul>
    <modul nom="Programació Multimèdia i dispositiud mòbils" hores="5" qualificacio="7,34">
    <modul nom="Sistemes de Gestió Empresarial" hores="5" "qualificacio"=8.2 />
    <modul nom="Empresa i iniciativa emprenedora"  hores="3" qualificacio="7.4" />
    </modul>
</curs>
```
Un **analitzador XML** és una classe que permet analitzar un fitxer XML i extreure informació d'ell, relacionant-la segons la seva posició en la jerarquia. Els analitzadors, segons la seva forma de funcionar, poden ser:

- Analitzadors **seqüencials** o sintàctics, que extreuen el contingut a mesura que es descobreixen les etiquetes d'obertura i tancament. Són molt ràpids, però tenen el problema que cal llegir tot el document per accedir a una part específica. En Java hi ha el parser **SAX** (Simple API for XML) com a analitzador seqüencial.
- Analitzadors **jeràrquics**, que són els més utilitzats i que guarden totes les dades del document XML a la memòria, en forma d'una estructura jeràrquica (**DOM** o Document Object Model), sent els preferits per a aplicacions que han de llegir les dades de manera més contínua.

## 1.2. El Model d'Objectes de Document (DOM)

El DOM (Document Object Model) és l'estructura especificada per la W3C on s'emmagatzema la informació dels documents XML. El DOM s'ha relacionat principalment amb el món web, amb HTML i Javascript com a principals impulsors. En Java, el DOM s'implementa utilitzant **interfícies**.

La interfície principal del DOM a Java és `Document`, i representa el _document XML complet_. Com que és una interfície, es pot implementar en diverses classes.

!!! note "Cal tenir en compte que..."
    Una interfície és una mena de plantilla per construir classes i generalment està composta per un conjunt de declaracions de capçalera de mètodes no implementats que especifiquen com es comporten una o més classes. A més, una classe pot implementar una o més interfícies. En aquest cas, la classe haurà de declarar i definir tots els mètodes de cada una de les interfícies o declarar-se com a classe abstracta.

    Cal tenir en compte que una interfície no s'ha de confondre amb una classe abstracta, ja que hi ha algunes diferències. Per exemple, una interfície té tots els mètodes abstractes, no pot declarar variables d'instància, una classe pot implementar diverses interfícies però no heretar de diverses superclasses, i una interfície no ha de pertànyer a cap jerarquia, de manera que les classes que no tenen cap relació d'herència poden implementar la mateixa interfície.


A part de `Document`, la W3C també defineix la classe abstracta `DocumentBuilder`, que permet crear el DOM a partir de l'XML. A més, s'especifica la classe `DocumentBuilderFactory`, que ens permet fabricar `DocumentBuilders`, ja que, al ser abstracta, no es pot instanciar directament.

Cal dir, com a advertència, que Java ofereix moltes llibreries des de les quals importar Document. Les llibreries que utilitzarem per analitzar XML seran:

- La llibreria `java.xml.parsers.*`, que oferirà les classes `DocumentBuilderFactory` i `DocumentBuilder`, i
- La llibreria `org.w3c.dom.*` per a la classe `Document`.


### 1.2.1. `DocumentBuilder` i `DocumentBuilderFactory`

Com s'ha dit, `DocumentBuilder` defineix una API per obtenir instàncies DOM d'un document XML. Per obtenir una instància de la classe, s'ha d'utilitzar la classe `DocumentBuilderFactory`, i concretament el mètode `newDocumentBuilder()`:

D'altra banda, per llegir i interpretar el document XML, la classe `DocumentBuilderFactory` proporciona el mètode `parse()`, que analitza un XML indicat per un fitxer i retorna un objecte `Document`.

Veurem tot això amb un exemple. Continuem emmagatzemant dades sobre els mòduls del curs, però ara amb XML. El següent mètode ens servirà per obrir un document XML, analitzar-lo i retornar el DOM generat en un `Document`. El podem utilitzar en qualsevol lloc dels nostres programes, ja que la tasca és sempre similar:

```java
public Document OpenXML(String name) throws IOException,SAXException, ParserConfigurationException, FileNotFoundException {

    // Create an instance of DocumentBuilderFactory
    DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
    // Using the DocumentBuilderFactory instance we create a DocumentBuilder
    DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();
    //And we use DocumentBuilder's "parse" method to get the document
    Document doc = dBuilder.parse(new File(name));

    return document;
}
```

S'ha de dir que la funció anterior es podria simplificar sense utilitzar les declaracions intermèdies, però està una mica ofuscada.


```java
public Document OpenXML(String name) throws IOException,SAXException, ParserConfigurationException, FileNotFoundException {
     return DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(name);
}
```

D'altra banda, la classe `DocumentBuilder` també ens permet crear un nou DOM amb el mètode `newDocument()`. Això ens servirà més endavant per emmagatzemar els nous documents XML. El procediment és el següent:

- Primer de tot, hem de crear un nou DOM amb `newDocument()`.
- Afegir els elements i,
- Després emmagatzemar-ho en un fitxer.

En seccions posteriors, veurem com fer tot això. Per ara, ens centrarem en interpretar i llegir el DOM.

Per a més informació, pots consultar les classes [DocumentBuilder](http://cr.openjdk.java.net/~iris/se/11/latestSpec/api/java.xml/javax/xml/parsers/DocumentBuilder.html) i [DocumentBuilderFactory](http://cr.openjdk.java.net/~iris/se/11/latestSpec/api/java.xml/javax/xml/parsers/DocumentBuilderFactory.html) al API de OpenJDK.

## 1.3. Classes i Mètodes de DOM

Fins ara hem vist com obrir i analitzar un document XML amb `DocumentBuilder` per crear un objecte de tipus `Document`. En aquesta secció veurem com treballar amb aquest document per accedir als diferents elements. Com sabem, el DOM té una estructura jeràrquica, formada per nodes. Els diferents tipus de nodes que podem trobar són:

- `Document` → que és el node principal i representa tot l'XML.
- `Element` → que representa les diferents etiquetes (incloent l'etiqueta arrel). En altres paraules, totes les etiquetes són Elements, unes dins d'altres.
- `TextElement` → que representa el contingut d'una etiqueta de text.
- `Attribute` → que representa els atributs.

Totes aquestes interfícies deriven de la interfície `Node`, per tant, heretaran els seus atributs i mètodes, i a més, proporcionaran els seus propis atributs i mètodes.

A continuació, veurem els mètodes més importants de cada interfície:

### 1.3.1. Mètodes de Node

**Mètodes relacionats amb l'obtenció d'informació**

- `String getNodeName()` → Obté el nom del node actual
- `short getNodeType()` → Obté el tipus de node (ELEMENT_NODE, ATTRIBUTE_NODE, TEXT_NODE...)
- `String getNodeValue()`→ Obté el valor del node
- `NodeList getChildNodes()` → Obté una llista amb els nodes fills
- `Node getFirstChild()` → Retorna el primer fill
- `Node getLastChild()` → Retorna l'últim fill
- `NamedNodeMap getAttributes()` → Retorna una llista amb els atributs del node
- `Node getParentNode()` → Retorna el node pare
- `String getTextContent()` → Retorna el text contingut en l'element i els seus descendents
- `boolean hasChildNodes()` → Retorna `true` si el node té algun fill
- `boolean hasAttributes()` → Retorna `true` si el node té algun atribut
  
**Mètodes relacionats amb l'escriptura**

- `Node appendChild(Node node)` → Afegeix un nou node com a últim fill.
- `void removeChild(Node node)` → Elimina el node especificat dels nodes fills.


### 1.3.2. Mètodes de la interfícies Element:

**Mètodes relacionats amb l'obtenció d'informació**

- `String getAttribute(String name)` → Retorna el valor de l'atribut donat pel nom.
- `NodeList getElementsByTagName(String name)` → Retorna una llista de nodes fills que coincideixen amb el nom donat.
- `boolean hasAttribute(String name)` → Retorna true si l'element té l'atribut donat.

**Mètodes relacionats amb l'escriptura**

- `void setAttribute(String name, String value)` → Afegeix un atribut a l'element, amb el nom i valor donats.
- `void removeAttribute(String name)` → Elimina l'atribut indicat pel nom.

### 1.3.3. Mètodes de la interfície Document:

**Mètodes relacionats amb l'obtenció d'informació**

- `Element getDocumentElement()` → Retorna l'element arrel del document.
- `NodeList getElementsByTagName(String name)` → Retorna una llista de nodes fills que coincideixen amb el nom donat.

**Mètodes relacionats amb l'escriptura**

- `Element createElement(String name)` → Crea un nou element amb el nom donat.
- `Text createTextNode(String text)` → Crea un nou element de text.
- `Node appendChild(Node node)` → Afegeix un nou node fill.

Els objectes de tipus `NodeList`, que representen una llista de nodes, ofereixen el mètode `item(int index)` per accedir als diferents nodes de la llista, indicant el seu ordre.


## 1.4. Lectura de fitxers XML

Anem a repassar tots els conceptes d'aquesta secció amb una pràctica. Crearem una classe que inclogui tots els mètodes necessaris per obrir, llegir, mostrar i escriure fitxers XML. Treballarem amb el document de la secció anterior.

Per començar a llegir el document, el primer que haurem de fer és obtenir l'element arrel del document amb `getDocumentElement()`, que retorna un objecte de tipus `Element`. Recorda que la variable _doc_ conté tot el DOM, llegit amb el mètode explicat anteriorment:

```java
Element root = doc.getDocumentElement();
```

Amb aquest element arrel, ja podem mostrar tot el contingut amb `getTextContent()`. Es mostrarà a la pantalla en format de text, només s'imprimirà:

```java
System.out.println(root.getTextContent());
```

Però el que ens interessa és recórrer tot el DOM i accedir als seus elements. Per fer-ho, a partir d'aquest element arrel, seguirem els següents passos:

1. Cerquem tots els tags `<modul>` amb `getElementsByTagName`. Aquest mètode ens retorna una llista de nodes (objecte de tipus `NodeList`).
2. Serà necessari recórrer la llista de nodes (`NodeList`) per accedir a cada element. Per fer-ho, cal utilitzar el mètode `item(int index)`, que retornarà un element de tipus `Node`, i que s'ha de convertir explícitament a `Element` amb una operació de cast.
3. Per a cada element, accedirem al nom del node per mostrar el nom i l'ordre, utilitzant `getNodeName()`.
4. Cerquem les diferents etiquetes trobades dins de cada mòdul ('nom', 'hores' i 'qualificacio') amb `getElementsByTagName()`. Aquest mètode ens tornarà una NodeList per a cada tipus d'etiqueta. Com només tindrem un element, només cal accedir a l'element únic, representat per `item(0)`.
5. Cal destacar que amb el que hem vist fins ara tindrem la primera (i única) etiqueta 'nom', 'hores' o 'qualificacio' del mòdul, però encara no estem en el contingut, ja que això és un element de tipus `TEXT_NODE`. Per accedir-hi, haurem d'accedir al primer fill de l'etiqueta (`getFirstChild()`) i obtenir el seu valor amb `getNodeValue()`.

```java
    // We will get a list of nodes (Step 1)
    NodeList modules = root.getElementsByTagName("modul");

    // For each node (Step 2)
    for (int i = 0; i < modules.getLength(); i++) {
        Element el = (Element) modules.item(i);

        // Display the node name (Step 3)
        System.out.println(el.getNodeName() + " " + (i + 1));

        // And we show the value of the different tags 
        System.out.println("Nom: " + el.getElementsByTagName("nom").item(0).getFirstChild().getNodeValue());
        System.out.println("Hores: " + el.getElementsByTagName("hores").item(0).getFirstChild().getNodeValue());
        System.out.println("Qualificació: " + el.getElementsByTagName("qualificacio").item(0).getFirstChild().getNodeValue());
        System.out.println();
    }
}
```

## 1.5. Escriure fitxers XML

Ara anem a la part d'escriptura dels documents XML. Per això, partirem d'un fitxer que ja conté la informació en format binari dels mòduls (per exemple, de la secció anterior), el llegirem i importarem la seva informació en format XML.

El primer que hem de fer és llegir el fitxer d'objectes utilitzant un `ObjectInputStream`:


```java
ObjectInputStream f = new ObjectInputStream(new FileInputStream(file));
```

I crearem un document buit amb les classes `DocumentBuilder` i `DocumentBuilderFactory`:

```java
Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument();
```

Un cop tenim el document buit, creem l'element arrel (curs) i l'afegim al document:

```java
Element root = doc.createElement("curso");
doc.appendChild(root);
```

Recorda que accedirem al fitxer d'objectes, així que haurem de saber exactament com és la classe que volem llegir i accedir als mètodes corresponents per obtenir informació sobre ella. Per això, primer, has de definir un objecte de tipus `Modul`, i llegirem el fitxer d'objectes amb el mètode `readObject` de `File`. Un cop hàgim llegit un objecte, crearem l'etiqueta que engloba cadascun d'ells: l'etiqueta `modul`:


```java
Modul m = (Modul) f.readObject();
Element modul = doc.createElement("modul");
```

And inside it, as we extract the different properties of the Modul object, we will create child nodes and add them to the module. For example, for the module name:

```java
Element name = doc.createElement("nom");
name.appendChild(doc.createTextNode(m.getNom()));
module.appendChild(name);
```

Com podem veure, hem creat un objecte de tipus Element amb l'etiqueta 'nom', i hem afegit com a fill un node de tipus text (TEXT_NODE), que hem extret directament de l'objecte `Modul m` amb la seva pròpia funció `getModul()`. A més, hem afegit aquesta etiqueta a l'etiqueta `<modul>`, amb `appendChild`.

Haurem de fer el mateix per a les hores de cada mòdul i la qualificació, però per això, haurem de tenir en compte que els mètodes getHores i getNota no retornen una cadena de text, sinó un enter i un decimal, de manera que hauran de ser convertits a text:


```java
Element hours = doc.createElement("hores");
hours.appendChild(doc.createTextNode(Integer.toString(m.getHores())));
module.appendChild(hours);

Element qualification = doc.createElement("qualificacio");
qualification.appendChild(doc.createTextNode(Double.toString(m.getNote())));
module.appendChild(grade);
```

Posarem tot aquest procediment dins d'un bucle que recorrerà tot el fitxer d'objectes. Un cop hàgim llegit cada un dels mòduls, haurem d'afegir-los a l'element arrel amb:

```java
root.appendChild(module);
```


And we will already have our XML document at the root. Now we would have to convert this object of type `Element` into a text string in order to be able to write it to the disk. For this, we will make use of the `Transformer` utility.


### 1.5.1. Transformer

Java ens ofereix l'utilitat `Transformer` per convertir informació entre diferents formats jeràrquics, com ara l'objecte Document que conté el DOM del nostre XML, a un fitxer de text XML.

La classe `Transformer`, com `DocumentBuilder`, també és una classe abstracta, per la qual cosa també requereix una fàbrica per ser instanciada. La classe Transformer treballa amb dos tipus d'adaptadors. Els adaptadors són classes que fan compatibles diferents jerarquies. Aquests adaptadors són `Source` i `Result`. Les classes que implementen aquests adaptadors seran les encarregades de fer compatibles els diferents tipus de contenidors amb el que requereix la classe Transformer. Així, i per aclarir-ho, tenim les classes `DOMSource`, `SAXSource` o `StreamSource`, que són adaptadors del contenidor d'origen de la informació per a DOM, SAX o Stream; i `DOMResult`, `SAXResult` i `StreamResult` com a adaptadors equivalents per al contenidor de destinació.

En el nostre cas, ja que tenim un DOM i volem convertir-lo en un Stream, necessitarem un `DomSource` i un `StreamResult`. Veurem el codi necessari per fer-ho:

```java
Transformer trans = TransformerFactory.newInstance().newTransformer();
DOMSource source = new DOMSource(doc);
StreamResult result = new StreamResult(new FileOutputStream(file+".xml"));
```

El primer que hem fet és crear un objecte de tipus `Transformer` amb el mètode `newTransformer()` d'una instància (newInstance()) de la fàbrica de Transformers TransformerFactory. A continuació, hem definit la font (source) i el resultat (resultat) per a la transformació, sent la font un DomSource creat a partir del doc que conté el nostre document, i el resultat un StreamResult, que escriurà el flux al disc a través d'un FileOutputStream.

Finalment, fem la transformació d'un element a un altre, que generarà automàticament el fitxer XML de sortida:

```java
trans.transform(source, result);
```

## 1.6. Tècniques avançades: vinculació XML

La tècnica de vinculació consisteix en generar classes Java amb formats específics, com ara XML, de manera que cada etiqueta o atribut XML correspongui a una propietat d'una determinada classe. Aquesta correspondència s'anomena **mapeig**.

En Java, hi ha diferents llibreries per a la vinculació o mapeig: JAXB, JuBX, XMLBinding, etc. JAXB (Java Architecture for XML Binding) és una potent llibreria que s'ha inclòs en l'estàndard des de JDK 6, però s'ha eliminat a la versió 11 i es suggereix incloure-la com a paquet extern. JAXB fa servir anotacions per obtenir la informació necessària per al mapeig de la vinculació. Les anotacions són indicacions especials de Java que permeten associar informació i funcionalitat als objectes, sense interferir en l'estructura del model de dades. Les anotacions es poden associar amb un paquet, una classe, un atribut o un paràmetre, i es declaren amb el símbol `@` davant del nom de l'anotació. Quan el compilador detecta una anotació, crea una instància i l'injecta a l'element afectat, sense interferir en la classe mateixa. Quan una aplicació necessita la informació de les anotacions, pot obtenir la instància injectada.

Per exemple, a la classe `Modul` que havíem definit, faríem servir l'anotació `@XmlRootElement` per indicar l'element arrel del mòdul, i les anotacions `@XmlElement` per indicar que els setters de la classe també escriuran elements XML.

```java
@XmlRootElement
class Modul {

    String nom;
    int hores;
    double nota;

    public String getNom() { return nom; }
    @XmlElement
    public void setNom(String nom) { this.nom = nom; }


    public int getHores() { return hores; }
    @XmlElement
    public void setHores(int hores) { this.hores = hores; }

    public double getNota() { return nota; }
    @XmlElement
    public void setNota(double nota) { this.nota = nota;}


}
```

Amb això tindríem només la classe amb les anotacions preparades per desar un mòdul com a document XML. Per desar tota la jerarquia hauríem de crear la classe `Curs`, que contindria un `ArrayList` de mòduls.

Pel que fa a aquest curs, no aprofundirem més en aquesta tècnica, ja que per als nostres propòsits, l'anàlisi XML que hem vist a les seccions anteriors és suficient.

!!! note "Cada vegada més"
    Utilitzarem moltes anotacions en aquest curs, mantingueu la calma...


