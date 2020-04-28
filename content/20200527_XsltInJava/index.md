---
title: "xslt transformations in Java"
date: 2020-04-26T18:00:00+01:00
publishdate: 2020-04-27T17:00:00+01:00
tags: ["testing", "java"]
summary: How should we test xslt transformations in java? Is the solution that we found on the most examples located on the internet suitable for us? In our case we still had problems that our tests where failing. The solution was correct. This means we need another approach.
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

public void run() throws Exception {
    DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
 
    File xml = new File("./input.xml");
    File xsl = new File("./input.xsl");
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

{{< /highlight >}}
There is a problem. The output is not correct. The xslt transformation does not fill in the values. What is happening?

The input data that I use is not that complex:
{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8" ?>
<getPersonResponse xmlns:mes="http://xmlns.cm.be/services/disabilitybenefits/business/managememberservice/v1/messages" xmlns="http://xmlns.cm.be/services/disabilitybenefits/business/managememberservice/v1/messages">
 <common:responseData xmlns:common="http://xmlns.cm.be/common">
 </common:responseData>
</getPersonResponse>
{{< /highlight >}}

And the transformation is like this.
{{< highlight xml >}}
<xslt>
{{< /highlight >}}


### Is the xslt wrong or the java code?
Was the xslt wrong? Was my code wrong? The code seems to be correct. I tested it with other xslt files and other input files. They seem to work correctly. But with our input file it did not work. After searching a lot, I asked it to the developers in our team. One developer had time and could also looking at the problem. After a while we discovered that the xslt seems to be correct. I created a python program that did the trick. It generated correct code.

{{< highlight python >}}
import lxml.etree as ET

dom = ET.parse("GetPersonResponse.xml")
xslt = ET.parse("input.xsl")
transform = ET.XSLT(xslt)
newdom = transform(dom)
print(ET.tostring(newdom, pretty_print=True).decode('utf-8'))
{{< /highlight >}}

But we decided to use java and not python. This solution is really the last resort. We searched again for a solution in java.
### The solution
What could be the solution to my problem? We searched that day. It was Friday evening, no solution. With no solution going in the weekend gave me not a sattisfiing feeling. 
After the weekend, I had a message in our team chat. One of the developers had found a solution in the weekend. I tried it with all my input data. And indeed, it worked.

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

### What did I learn ?

* Do not hesitate to get help from your developers. They want to help and they will help.
* Try to narrow the problem. Use other languages if needed to pinpoint to the real problem.
