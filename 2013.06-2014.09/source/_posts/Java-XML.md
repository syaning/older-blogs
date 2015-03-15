title: Java解析XML
date: 2014-04-16 16:55:58
categories: Java
tags:
---
应用程序经常会与xml文档进行交互，因此需要对xml文档进行解析，因此需要用到解析器。解析器是用来分析xml文档，并对信息做一些特定处理的应用程序，通常我们使用已有的解析器而不必自行编写解析器。至于DOM和SAX，则是解析器常用的两种解析API。

DOM 是用与平台和语言无关的方式表示 XML 文档的官方 W3C 标准。DOM 是以层次结构组织的节点或信息片断的集合。这个层次结构允许开发人员在树中寻找特定信息。分析该结构通常需要加载整个文档和构造层次结构，然后才能做任何工作。由于它是基于信息层次的，因而DOM被认为是基于树或基于对象的。而SAX是一个事件驱动的API。SAX分析经过其的XML流，并产生相应的事件。SAX API 允许开发者捕获这些事件，并对它们进行操作。应用程序通过注册自身，以接受XML解析器遇到文档不同部分而发出的信息。

<!-- more -->
下面通过例子来进行分析。首先看一个xml文档bookstore.xml：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
    <book category="children">
        <title lang="en">Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="cooking">
        <title lang="en">Everyday Italian</title>
        <author>Giada De Laurentiis</author>
        <year>2005</year>
        <price>30.00</price>
    </book>
    <book category="web">
        <title lang="en">Learning XML</title>
        <author>Erik T. Ray</author>
        <year>2003</year>
        <price>39.95</price>
    </book>
    <book category="web">
        <title lang="en">XQuery Kick Start</title>
        <author>James McGovern</author>
        <author>Per Bothner</author>
        <author>Kurt Cagle</author>
        <author>James Linn</author>
        <author>Vaidyanathan Nagarajan</author>
        <year>2003</year>
        <price>49.99</price>
    </book>
</bookstore>
```

###  DOM 解析
```java
package com.syaningv.javatest.xmlparse;
 
import java.io.File;
import java.io.IOException;
 
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
 
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;
 
public class DomParserTest {
    public static void main(String[] args) {
        read();
        write();
    }
 
    /**
     * Read and parse an xml document.
     */
    public static void read() {
        File file = new File("document/bookstore.xml");
        Document document = null;
 
        try {
            DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
            DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
            document = documentBuilder.parse(file);
        } catch (ParserConfigurationException | SAXException | IOException e) {
            e.printStackTrace();
        }
 
        // get root element
        Element root = document.getDocumentElement();
        System.out.println(root.getNodeName());
 
        // get booklist
        NodeList booklist = root.getChildNodes();
        for (int i = 0; i < booklist.getLength(); i++) {
            Node book = booklist.item(i);
            // since DOM treat each line break as a #text node, we should check
            // type of the node
            if (book.getNodeType() == Node.ELEMENT_NODE) {
                System.out.println("\t" + book.getNodeName() + "\t"
                        + book.getAttributes().getNamedItem("category").getNodeValue());
 
                NodeList bookinfo = book.getChildNodes();
                for (int j = 0; j < bookinfo.getLength(); j++) {
                    Node info = bookinfo.item(j);
                    if (info.getNodeType() == Node.ELEMENT_NODE) {
                        /*
                         * only annotation, attribute and text node has
                         * nodevalue, so we will get null if we use
                         * info.getNodeValue(). We can use info.getTextContent()
                         * or info.getFirstChild().getNodeValue()
                         */
                        System.out.println("\t\t" + info.getNodeName() + "\t" + info.getTextContent());
                    }
                }
            }
        }
    }
 
    /**
     * Create an xml document.
     */
    public static void write() {
        Document document = null;
 
        try {
            DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
            DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
            document = documentBuilder.newDocument();
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        }
 
        Element root = document.createElement("bookstore");
        document.appendChild(root);
 
        Element book = document.createElement("book");
        book.setAttribute("category", "children");
        root.appendChild(book);
 
        Element title = document.createElement("title");
        title.setAttribute("lang", "en");
        title.setTextContent("Harry Potter");
        Element author = document.createElement("author");
        author.setTextContent("J K. Rowling");
        Element year = document.createElement("year");
        year.setTextContent("2005");
        Element price = document.createElement("price");
        price.setTextContent("30.00");
 
        book.appendChild(title);
        book.appendChild(author);
        book.appendChild(year);
        book.appendChild(price);
 
        try {
            Transformer transformer = TransformerFactory.newInstance().newTransformer();
            transformer.setOutputProperty(OutputKeys.INDENT, "yes");
 
            DOMSource domSource = new DOMSource(document);
 
            File file = new File("document/domcreate.xml");
            if (file.exists())
                file.delete();
            file.createNewFile();
 
            StreamResult xmlResult=new StreamResult(file);
            transformer.transform(domSource, xmlResult);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
这里我们实现了两个方法，read为读取一个xml文档并进行解析，然后将文档内容按照层次打印出来。write方法为创建一个新的xml文档并写入内容。

DOM的基本对象有5个：Document、Node、NodeList、Element和Attr，它们之间的关系如图所示：
![](/2014/04/16/Java-XML/java_xml_1.png)
DOM对于xml文档的解析模型如图：
![](/2014/04/16/Java-XML/java_xml_2.png)
在读取文档过程中的一些注意点给出了注释，第一就是解析的时候会将换行也作为一个text类型的Node，所以在分析Node之前应当先判断Node类型是否为Element；第二就是只有备注、属性和文本才有nodevalue，所以对于一个节点，直接使用getNodeValue获取到的值为null，应当使用getTextContent()或者getFirstChild().getNodeValue()，即标签之内的文本是作为一个文本节点而存在的。

### SAX 解析
SAX顺序遍历文档,然后产生所有节点事件,随后在内存中只保留当前处理部分。在事件驱动模型下，API不保存任何文档的内容，相关的内容被传给事件相关的方法。SAX有四个核心接口，分别问：ContentHandler、DTDHandler、EntityResolver和ErrorHandler，SAX解析xml文档的模型如图：
![](/2014/04/16/Java-XML/java_xml_3.png)
下面给出简单的示例代码：
```java
package com.syaningv.javatest.xmlparse;
 
import java.io.File;
import java.io.IOException;
 
import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;
 
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;
 
public class SAXParserTest {
    public static void main(String[] args) {
        read();
    }
 
    public static void read() {
        try {
            SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
            SAXParser saxParser = saxParserFactory.newSAXParser();
            File file = new File("document/bookstore.xml");
            DefaultHandler handler = new MySAXHandler();
            saxParser.parse(file, handler);
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        } catch (SAXException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
其中MySAXHandler继承了DefaultHandler，代码如下：
```java
package com.syaningv.javatest.xmlparse;
 
import org.xml.sax.Attributes;
import org.xml.sax.helpers.DefaultHandler;
 
public class MySAXHandler extends DefaultHandler {
    @Override
    public void startDocument() {
        System.out.println("start document");
    }
 
    @Override
    public void endDocument() {
        System.out.println("end document");
    }
 
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) {
        if (qName.equals("bookstore"))
            System.out.println("\t" + qName);
        else if (qName.equals("book"))
            System.out.println("\t\t" + qName);
        else
            System.out.print("\t\t\t" + qName);
    }
 
    @Override
    public void endElement(String uri, String localName, String qName) {
        // do nothing
    }
 
    @Override
    public void characters(char[] ch, int start, int length) {
        String data = new String(ch, start, length).trim();
        if (data.length() > 0)
            System.out.println("\t" + data);
    }
}
```

### DOM和SAX的比较
DOM有着简单易用，API丰富的有点，并且DOM将整棵DOM树加载到内存中，可以对xml文档进行随机访问。但是正是因为DOM是基于树的，需要一次性将文档解析完，如果文档较大的话，整棵树加载到内存中成本就会很高。因此DOM方式适用于需要进行随机访问和修改xml文档的应用程序。

而SAX无需将整个文档读入内存中，因此对内存的消耗较少，并且允许注册多个ContentHandler。但是SAX不支持文档的随机访问以及修改，因此适用于只读的xml文档。

DOM和SAX应用场景选择：首先要看应用程序的目的，如果需要对数据进行修改，并作为xml文档进行输出，则应该使用DOM；然后要考虑数据量，如果文档比较大，那么SAX是较好的选择；同时还要考虑如何使用数据，如果只使用很少的一部分数据，那么使用SAX将数据抽取到应用程序中会好一些；如果考虑性能的话，通常情况下，SAX要比DOM快些。其实，DOM和SAX并不是互斥的，两者完全可以结合使用。

### 参考文章
[Java解析XML汇总（DOM/SAX/JDOM/DOM4j/XPath）](http://blog.csdn.net/smcwwh/article/details/7183869)
[sax解析xml案例一](http://www.cnblogs.com/linjiqin/archive/2011/03/11/1981076.html)
[DOM、JDOM、DOM4J的区别](http://blog.csdn.net/shijinupc/article/details/7653945)
[sax,dom,jdom,dom4j的比较](http://xiaoyuzhou.iteye.com/blog/1152541)