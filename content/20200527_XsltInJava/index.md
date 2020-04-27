---
title: "xslt transformations in Java"
date: 2020-04-26T18:00:00+01:00
publishdate: 2020-04-27T17:00:00+01:00
tags: ["testing", "java"]
summary: How should we test xslt transformations in java? Is the solution that we found on the most examples located on the internet suitable for us? In our case we still had problems that our tests where failing. The solution was correct. This means we need another approach.
comments: false
---
### The problem
I am on a project that changes input data into output data. This means we need to transform data. Our team has choosen to handle the
transformation with an xslt transformation. The input data is xml and the output data is xml of another format, so that seems a logical
choice.

We decided to make test the transformation on its own in a separate java project. At first I did not know where to look how to test this transformation. I decided to have some input files, do the transformation and look then afterwards if the transformation matches the output. The first time I could just generate it with the test environment. Then I verified the results and the next run the stored results of the first run could be saved.

So I needed code to have do the transformation. I entered some keywords in DuckDuck and some links to a solution that looks like this showed up.
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
But there was a problem. The output was not correct. Maybe because of all the namespaces? 

### Is the xslt wrong or our code ?
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
