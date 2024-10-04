# 1. XML Files

## 1.1. Why XML?

When we want to save data that can be read by different applications and platforms, it is best to use standard storage formats that multiple applications can understand (**portability**). A very specific case is _markup languages_, and the best known is the XML (**eXtensible Markup Language**) standard.

With XML documents we structure the information by inserting marks or tags between the information. These tags have a beginning and an end, and can nest inside others, as well as contain textual information. Since the information will be textual, we don't have the problem of different data representation, since any data, of whatever type, will be passed to text. In order to also avoid the problem of different text encoding systems, XML allows the encoding used to save the document to be included in the header of the document.

The way to store information in XML, in a hierarchical way, is very similar to the way objects in an application do it, so that these can be translated in a relatively convenient way to an XML document. Starting with our previous sample:

| Module | Hours | Qualification|
|---|-|---|
|Accés a Dades	| 6	|8.45 |
|Programació de serveis i processos	|3	|9.0|
|Desenvolupament d'interfícies	|6	|8.0|
|Programació Multimèdia i dispositius mòbils	|5	|7.34|
|Sistemes de Gestió Empresarial	|5	|8.2|
|Empresa i iniciativa emprenedora	|3	|7.4|

can be represented with only tags or labels `xml`:

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

or can be represented with tags and attributes:

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
An **XML parser** or analyzer is a class that allows you to analyze an XML file and extract information from it, relating it according to its position in the hierarchy. The analyzers, according to their way of functioning, can be:

- **Sequential** or syntactic analyzers, which extract the content as the opening and closing tags are discovered. They are very fast, but have the problem that you have to read the whole document in order to access a specific part. In Java there is the **SAX** (Simple API for XML) parser as a sequential parser.
- **Hierarchical** analyzers, which are usually the most used, and which save all the data of the XML document in memory, in the form of a hierarchical structure (**DOM** or Document Object Model, being the preferred ones for applications that have to read the data more continuously.

## 1.2. The Document Object Model (DOM)

The DOM (Document Object Model) is the structure specified by the W3C where the information of XML documents is stored. The DOM has been linked mainly to the web world, with HTML and Javascript as the main drivers. In Java, the DOM is implemented using interfaces.

The main interface of the DOM in Java is `Document`, and it represents the _entire XML document_. Since it is an interface, it can be implemented in several classes.

!!!note
    An interface is a kind of template for building classes, and is generally composed of a set of unimplemented method header declarations that specify how one or more classes behave. In addition, a class can implement one or more interfaces. In this case, the class will have to declare and define all the methods of each of the interfaces, or declare itself as an abstract class.

    An interface should also not be confused with an _abstract class_, as there are some differences, as the interface has all abstract methods; cannot declare instance variables; a class can implement several interfaces, but not inherit from several superclasses; and the interface does not have to belong to any hierarchy, so that classes that do not have any inheritance relationship can implement the same interface.

Apart from `Document`, the W3C also defines the abstract class `DocumentBuilder`, which allows to create the DOM from the XML. In addition, the `DocumentBuilderFactory` class is specified, which allows us to manufacture `DocumentBuilders`, since being abstract it cannot be instantiated directly.

It should be said, as a warning, that Java offers many libraries from which to import Document. The libraries we will use to parse XMLs will be:

- The `java.xml.parsers.*` library, which will offer the `DocumentBuilderFactory` and `DocumentBuilder` classes, and
- The `org.w3c.dom.*` library for the `Document` class.

### 1.2.1. `DocumentBuilder` and `DocumentBuilderFactory`

As discussed, `DocumentBuilder` defines an API for obtaining DOM instances from an XML document. In order to obtain an instance of the class, the `DocumentBuilderFactory` factory must be used, and specifically the `newDocumentBuilder()` method:

On the other hand, in order to read and interpret the XML document, the `DocumentBuilderFactory` class provides the `parse()` method, which parses an XML indicated by a File, and returns a Document object.

Let's see everything with an example. We continue with storing data about course modules, but now with XML. The following method will serve us, in order to open an XML document, parse it and return the DOM generated in a Document. We can use it everywhere in our programs, because the task is always similar:

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

It should be said that the above function could have been simplified without using the intermediate statements, but it is a little offuscated:

```java
public Document OpenXML(String name) throws IOException,SAXException, ParserConfigurationException, FileNotFoundException {
     return DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(name);
}
```

On the other hand, the `DocumentBuilder` class also allows us to create a new DOM, with the `newDocument()` method. This will serve us later in order to store the XML documents. The proceeding way is:

- First thing, we will have to do is create a new DOM with newDocument()
- Add the elements and 
- then store it on a file. 

In later sections, we will see how to do all this. For now we're going to focus on interpreting and reading the DOM.

You can visit this links in order to find more information about the [DocumentBuilder](http://cr.openjdk.java.net/~iris/se/11/latestSpec/api/java.xml/javax/xml/parsers/DocumentBuilder.html) and [DocumentBuilderFactory](http://cr.openjdk.java.net/~iris/se/11/latestSpec/api/java.xml/javax/xml/parsers/DocumentBuilderFactory.html) classes can be found in the OpenJDK API:

## 1.3. Classes and Methods of DOM

So far we've seen how to open and parse an XML document with DocumentBuilder to create an object of type Document. In this section we will see how to work with this document in order to access the different elements. As we know, the DOM has a hierarchical structure, made up of nodes. The different types of nodes we can find are:

- `Document` → which is the main node and represents all the XML.
- `Element` → which represents the different tags (including the root). In other words, all tags are Elements, ones nested inside othres.
- `TextElement` → which represents the content of a text tag.
- `Attribute` → which represents the attributes.
  
All these interfaces derive from the `Node` interface, so they will inherit its attributes and methods, and in addition, they will provide their own attributes and methods.

Let's see the most important methods of each interface:

### 1.3.1. Node interface methods

**Methods related to obtaining information**

- `String getNodeName()` → Gets the name of the current node
- `short getNodeType()` → Gets the node type (ELEMENT_NODE, ATTRIBUTE_NODE, TEXT_NODE...)
- `String getNodeValue()`→ Gets the value of the node
- `NodeList getChildNodes()` → Gets a list with child nodes
- `Node getFirstChild()` → Returns the first child
- `Node getLastChild()` → Returns the last child
- `NamedNodeMap getAttributes()` → Returns a list with the attributes of the node
- `Node getParentNode()` → Returns the parent node
- `String getTextContent()` → Returns the text contained in the element and its descendants
- `boolean hasChildNodes()` → Returns true if the node has any children
- `boolean hasAttributes()` → Returns true if the node has any attributes
  
**Methods related to writing**

- `Node appendChild(Node node)` → Appends a new node as the last child node.
- `void removeChild(Node node)` → Removes the specified node from the child nodes.

### 1.3.2. Element interface methods:

**Methods related to obtaining information**

- `String getAttribute(String name)` → Returns the value of the attribute given by name.
- `NodeList getElementsByTagName(String name)` →  Returns a list of child nodes that match the given name.
- `boolean hasAttribute(String name)` → Returns true if the element has the given attribute.

**Methods related to writing**

- `void setAttribute(String name, String value)` → Adds an attribute to the element, with the given name and value.
- `void removeAttribute(String name)` → Removes the attribute indicated by name.

### 1.3.3. Document interface methods:

**Methods related to obtaining information**

- `Element getDocumentElement()` →  Returns the root element of the document.
- `NodeList getElementsByTagName(String name)` →  Returns a list of child nodes that match the given name

**Methods related to writing**

- `Element createElement(String name)` → Creates a new element with the given name.
- `Text createTextNode(String text)` → Creates a new text element.
- `Node appendChild(Node node)` → Appends a new child node.

Objects of type `NodeList`, which represent a list of nodes, offer the `item(int index)` method to access the different nodes in the list, indicating their order.

## 1.4. Reading XML files

Let's go to review all concepts in this section with a practice. We are going to create a class who includes all the necessary methods to open, read, show and write XML files. We work with the document in section \ref{xmlDoc}. 

In order to start reading the document, the first thing we will have to do is get the root of the document, with `getDocumentElement()`, which returns an object of type `Element`. Remember that _doc_ variable contains the whole DOM, read with method explained previously:
```java
Element root = doc.getDocumentElement();
```

With this root element, we could already display all the content with `getTextContent()`. It will show on screen as text format, only print:

```java
System.out.println(root.getTextContent());
```

But what we're interested in is traversing the entire DOM and accessing its elements. For this, starting from this root element, we will follow the following steps:

1. We will search for all `<modul>` tags with `getElementsByTagName`. This method returns us a list of nodes (object of type `NodeList`). 
2. It will be necessary to traverse the list of nodes (`NodeList`) to access each element. For this, you must use the `item(int index)` method, which will return an element of type `Node`, and which must be converted to `Element` explicitly with a cast operation.
3. For each element, we'll access the node name to display the name and order, using `getNodeName()`
4. We look for the different tags found within each module ('name', 'hours' and 'grade') with `getElementsByTagName()`. This method have given us again a NodeList for each type of tag. Since we will only have one item, we only need to access the unique element, represented by `item(0)`.
5. It should be noted that with the above we will have the first (and only) label 'name', 'hours' or 'grade' of the module, but we are not yet in the content, since this is an element of type `TEXT_NODE`. To access it, we will have to access the first child of the tag (`getFirstChild()`) and get its value with `getNodeValue()`

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

## 1.5. Writing XML Files

Now let's go to the writing part of the XML documents. For this, we will start from a file that already contains the information in binary format of the modules (for example from previous section), we will read it, and we will import its information in XML format.

The first thing we need to do is read the object file using an `ObjectInputStream`:

```java
ObjectInputStream f = new ObjectInputStream(new FileInputStream(file));
```

And create an empty Xml Document, using the `DocumentBuilder` and `DocumentBuilderFactory` classes:

```java
Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument();
```

Once we have the empty document, we create the root element (curso), and add it to the document:

```java
Element root = doc.createElement("curso");
doc.appendChild(root);
```

Remember that we will access the object file, so we will have to know exactly what the class we want to read is like, and access the corresponding methods in order to obtain information about it. For this, first, you need to define an object of type `Modul`, and we will read the object file with the `readObject` method of File. Once we have read an object, we will create the tag that encompasses each of them: the `modul` tag:

```java
Modul m = (Modul) f.readObject();
Element modul = doc.createElement("modul");
```

And inside it, and as we extract the different properties of the Modul object, we will create child nodes and add them to the module. For example, for module name:

```java
Element name = doc.createElement("nom");
name.appendChild(doc.createTextNode(m.getModul()));
module.appendChild(name);
```

As we can see, we have created an object of type Element with the label 'name', and we have added as a child a node of type text (TEXT_NODE), which we have extracted directly from the object `Modul m` with its own function `getModul()`. Additionally, we added this tag to the `<modul>` tag, with appendChild.

We will have to do the same for the hours of each module and the qualification, but for this, we will have to take into account that the getHores and getNota methods do not return a String, but an int and a double, so they will have to be converted to text:

```java
Element hours = doc.createElement("hores");
hours.appendChild(doc.createTextNode(Integer.toString(m.getHores())));
module.appendChild(hours);

Element qualification = doc.createElement("qualificacio");
qualification.appendChild(doc.createTextNode(Double.toString(m.getNote())));
module.appendChild(grade);
```

We'll put this entire procedure inside a loop that slides the entire object file. Once we have read each of the modules, we will have to add them to the root element with:

```java
root.appendChild(module);
```

And we will already have our XML document at the root. Now we would have to convert this object of type `Element` into a text string in order to be able to write it to the disk. For this we will make use of the `Transformer` utility.

### 1.5.1. Transformer

Java offers us the `Transformer` utility to convert information between different hierarchical formats, such as the Document object that contains the DOM of our XML, to an XML text file.

The `Transformer` class, like `DocumentBuilder`, is also an abstract class, so it also requires a factory to be instantiated. The Transformer class works with two types of adapters. Adapters are classes that make different hierarchies compatible. These adapters are `Source` and `Result`. The classes that implement these adapters will be responsible for making the different types of containers compatible with what the Transformer class requires. So, and to clarify, we have the `DOMSource`, `SAXSource` or `StreamSource` classes, which are adapters of the information source container for DOM, SAX or Stream; and of `DOMResult`, `SAXResult`, and `StreamResult` as equivalent adapters to the target container.

For our case, since we have a DOM and want to convert it to a Stream, we will need a `DomSource` and a `StreamResult`. Let's see the code needed to do this:

```java
Transformer trans = TransformerFactory.newInstance().newTransformer();
DOMSource source = new DOMSource(doc);
StreamResult result = new StreamResult(new FileOutputStream(file+".xml"));
```

The first thing we have done is to create an object of type Transformer with the newTransformer() method of an instance (newInstance()) of the Transformers factory TransformerFactory. Then we defined the source (source) and the result (result) for the transformation, the source being a DomSource created from the doc that contains our document, and the result a StreamResult, which will write the stream to disk through a FileOutputStream.

And finally, we do the transformation from one element to another, which will automatically generate the output XML file:

```java
trans.transform(source, result);
```

!!!note "You can get"
    The full sample is available on Aules platform

## 1.6. Advanced techniques: XML binding

The Binding technique consists of generating Java classes with specific formats, such as XML, so that each XML tag or attribute corresponds to a property of a certain class. This correspondence is called a **mapping**.

In Java, there are different libraries for mapping or binding: JAXB, JuBX, XMLBinding, etc. JAXB (Java Architecture for XML Binding) is a powerful library that has been included in the standard since JDK 6, but has been removed in version 11, and is suggested to be included as a third-party package. JAXB makes use of annotations to get the information needed to map the binding. Annotations are special Java indications that allow you to associate information and functionality to objects, without interfering with the structure of the data model. Annotations can be associated with a package, class, attribute, or parameter, and are declared with the `@` symbol in front of the annotation name. When the compiler detects an annotation, it creates an instance and injects it into the affected element, without interfering with the class itself. When an application needs the information from the annotations, they can get the injected instance.

For example, in the `Modul` class we had defined, we would use the annotation `@XmlRootElement` to indicate the root element of the module, and the annotations `@XmlElement`, to indicate that the setters of the class will also write XML elements.

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

With this we would have only the class with annotations ready to save a module as an XML document. To save the entire hierarchy we should create the `Curs` class, which would contain an `ArrayList` of modules.

As for this course, we will not delve further into this technique, since for our purposes, the XML parsing that we have seen in previous sections is sufficient.

!!!note "More and more"
    We are going to use lots of annotations this course, keep calm...

