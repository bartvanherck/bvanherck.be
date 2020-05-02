---
title: "xslt transformations in Java"
date: 2020-05-02T10:00:00+01:00
publishdate: 2020-05-02T10:00:00+01:00
tags: ["testing", "java"]
summary: How should we test xslt transformations in java? Is the solution that we found on the most examples located on the internet suitable for us? In our case we still had problems that our tests where failing. The solution is not correct. This means we need another approach.
comments: false
---
### I have a problem
Today I want to test an application that transforms some data from an xml file to another xml file. The application that our team needs to test is a soa application. In this application there is an xslt located that does the transformation. This means that I need to test the xslt. 

I test the xslt in a separate test project written in java. I write an application that takes an input file. The application transforms the data to the resulting format. At last, the program saves the result in the output file.

I need some java code that performs the transformation. The variable part is the xslt file. There are plenty websites that can help me. I looked it up in my search engine and got a lot of results. The following code showed up.

{{< highlight java >}}
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Source;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.URIResolver;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import javax.xml.transform.stream.StreamSource;
import java.io.File;
import org.w3c.dom.Document;

public class Transformation {

    public void run() throws Exception {
        File xml = new File("./input.xml");
        File xsl = new File("./input.xsl");
        
        Document document;
        DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
        DocumentBuilder builder = factory.newDocumentBuilder();
        document = builder.parse(xml);
        // Use a Transformer for output
        TransformerFactory transformerFactory = TransformerFactory.newInstance();
        StreamSource style = new StreamSource(xsl);
 
        Transformer transformer = transformerFactory.newTransformer(style);
        transformer.setOutputProperty(OutputKeys.INDENT, "yes");
        DOMSource source = new DOMSource(document);
        StreamResult result = new StreamResult(System.out);
       
        transformer.transform(source, result);
    }
}

{{< /highlight >}}
There is a problem. The output is not correct. The xslt transformation does not fill in the values. What is happening?

{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8"?>
<person:createResponse xmlns:person="services.person">
    <person:firstName/>
    <person:familyName/>
</person:createResponse>
{{< /highlight >}}

The input data that I use is not that complex:
{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8" ?>
<getPersonResponse xmlns:mes="messages" xmlns="messages">
 <mes:person>
    <tns:personDetails xmlns:tns="person">
       <tns:surname>GHOST</tns:surname>
       <tns:nickName>BABY</tns:nickName>
    </tns:personDetails>
 </mes:person>
</getPersonResponse>
{{< /highlight >}}

And the transformation is like this.
{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8" ?>
<xsl:stylesheet version="1.0"
                xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
                xmlns:tns="services.tns"
                xmlns:person="services.person"
                xmlns:mes="messages"
                xmlns:per="person"
                exclude-result-prefixes="xsl tns mes per mind"
                >
  <xsl:template match="/">
    <person:createResponse>
      <person:firstName>
        <xsl:value-of select="/mes:getPersonResponse/mes:person/per:personDetails/per:nickName"/>
      </person:firstName>
      <person:familyName>
        <xsl:value-of select="/mes:getPersonResponse/mes:person/per:personDetails/per:surname"/>
      </person:familyName>
    </person:createResponse>
  </xsl:template>
</xsl:stylesheet>
{{< /highlight >}}


### Is the xslt wrong or the java code?
Is the xslt wrong? Is the java code wrong? The java code seems to be correct at first sight. I did test it with another xslt file and orhter input files. That works like it supposed to work. After searching a lot I told the developers of the team my problem. One of them helped me with that problem. He did also not find a solution in 5 minutes.

I did want to know where the problem is. Is it the code or the xslt. In the program that we are making, the xslt works. But why did it not work in my test environment? I created a python program to do the same xslt transformation.

{{< highlight python >}}
import lxml.etree as ET

dom = ET.parse("input.xml")
xslt = ET.parse("input.xsl")
transform = ET.XSLT(xslt)
newdom = transform(dom)
print(ET.tostring(newdom, pretty_print=True).decode('utf-8'))
{{< /highlight >}}

This program did the transformation correctly. So I was sure now that the xslt and the input data is correct. The problem is in the java code.
### The solution
What could be the solution to this transformation problem? We searched that day. It was Friday evening and we did not find any solution. Having a weekend with no real solution. That is not satisfiing. After the weekend, I had a message in our team chat. One of the developers had found a solution in the weekend. I tried it with all my input data. And indeed, it worked.

The solution is very simple. Do not parse the xml into a Document, but keep it in a StreamSource. 

{{< highlight java >}}
public void run() throws Exception {
    String xslFile = "./input.xsl";
    String xmlFile = "./input.xml";

    TransformerFactory transformerFactory = TransformerFactory.newInstance();
    StreamSource xslSource = new StreamSource(xslFile);
    Transformer transformer = transformerFactory.newTransformer(xslSource);
    StreamSource xmlSource = new StreamSource(xmlFile);
    StreamResult result = new StreamResult(System.out);

    transformer.setOutputProperty(OutputKeys.INDENT, "yes");  // to have nice output on screen
    transformer.transform(xmlSource, result);
}
{{< /highlight >}}

{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8"?>
<person:createResponse xmlns:person="services.person">
    <person:firstName>BABY</person:firstName>
    <person:familyName>GHOST</person:familyName>
</person:createResponse>
{{< /highlight >}}

Now we can test our xslt transformations with our test framework.

### What did I learn ?

* Do not hesitate to get help from your developers. They want to help and they will help.
* Try to narrow the problem. Use other languages if needed to pinpoint to the real problem.
