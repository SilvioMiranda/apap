# Introduction #

At my company, we have a lot of customer-specific development. Each project is usually finished with one or more documents which include detailed descriptions of SAP repository objects, e.g. the structure of database tables or function module interfaces.

Problems identified in this process:
  * The definitions are manually copied from the SAP workbench into the document, which is a tedious task.
  * Only short descriptions of the objects are provided, workbench features such as the documentation of data elements, function groups and modules are hardly ever used. Instead, the consultant may create a more in-depth documentation of the object in the manual, which results in a separation of the objects (in the SAP system) and their description (in the document on a file system).
  * If a change request requires a change of repository objects, the document needs to be updated, again in a tedious manual task.


This situation may be improved by automatically creating documentation from the repository objects. The Application Package Abstract Processor (APAP) described in this paper is a two-step process to extract the information from the SAP system and generate documentation in a specified format, currently HTML.

In the first step, an ABAP report downloads an XML file with the information of all repository objects of a selected set of packages. In the following step, an XSLT processor transforms the file into a collection of HTML pages.

Advantages of this process:
  * Easy to use, because only a few clicks are required to generate the HTML output.
  * The ABAP reports includes the documentation of various repository objects in the output, e.g. function modules, classes, class attributes and methods. In addition, the documentation may appear more than one time. For instance, the documentation of a data element is added to each structure which contains that data element as a field. It does pay out for the developer to provide the documentation in the SAP system.
  * XML and XSLT are up-to-date technologies.
  * The HTML output includes a Cascading Stylesheet, which may be adapted to the corporate image.
  * The transformation of the XML file to HTML is distributed among several stylesheet files. Since they themselves are XML files, it is possible to adapt the styleheets to one's own needs where the adaption of the CSS file does not suffice.
  * A different set of stylesheets may produce different output, e.g. PDF, from the same source - automatically as well.
  * APAP is an open-source project such that the community may contribute extensions for further repository objects or target formats.


# Using APAP #
## Generate an XML file from the repository objects ##
This step requires that the report ZAPAP is present in your SAP system. It may be downloaded from the project website either as a text file or, if you are familiar with SAPlink, as a Slinkee.

![http://apap.googlecode.com/files/selectionscreen-0.2.jpg](http://apap.googlecode.com/files/selectionscreen-0.2.jpg)

At the selection screen, you may enter an arbitrary name for the "application", which will be the outermost tag of the output file. Select one or more packages to be included into the output file. If your project contains a hierarchy of packages, it may be simpler to enter the top-level package and let the report select the subpackages recursively.

The following checkboxes allow the user to specify which repository objects will be included in the output, where system customising tables may additionally feature their content.

The final fields of the selection screen define the language in which short descriptions and documentation will be retrieved and the destination file.

The report may run a while, depending on the size of the selected packages. It will then display the created output as a list while saving it to the specified destination file.

Here is an extract of a sample output file:

```
<function-module name="BAPI_TSCPRCONNE_TRANSFER_CPR" active="X" fmode="R" include="01" description="Transfer CATS II Data by cProjects">
<tables name="TRANSFER_RECORDS" type="BAPI_TSCPRCONNE_TRANSFER" description="Transferred Data from CATS II for Feedback in cProjects">
</tables>
<tables name="RETURN" type="BAPIRET2" description="Return Parameters">
</tables>
<documentation>
<functionality>
The business object TSCPRCONNE enables the Cross-Application Time Sheet (CATS II) to be connected to the cProjects application. Data is transferred to the target system using the IDOC interface.
The connection to the IDOC interface is made using the BAPI method &lt;LS&gt;TransferCPR&lt;/&gt;. This method calls function module &lt;LS&gt;BAPI_TSCPRCONNE_TRANSFER_CPR&lt;/&gt; in the relevant target system.
</functionality>
<hints>
To set up the transfer of data from the Time Sheet (CATS II) with cProjects, you must make the necessary settings in the &lt;DS:GLOS.352A41BED9685CD5E10000009B38F974&gt;ALE Distribution Model&lt;/&gt;.
</hints>
</documentation>
</function-module>
```

## Transform the XML file to the destination format through Extensible Stylesheets ##
The Extensible Stylesheet Language for Transformations (XSLT) is a flexible, powerful language for transforming XML documents into something else. The XSLT stylesheets define the rules for transforming the XML document, the XSLT processor does the work.

APAP currently provides a set of stylesheets to transform the XML file of the previous section into HTML. Unfortunately, the master file apap.xsl contains some directions proprietary to the open-source XSLT processor [Apache Xalan](http://xml.apache.org/xalan-j/).

The [Xalan homepage](http://xml.apache.org/xalan-j/getstarted.html) describes how to set up the processor on your computer. Another possibility is to use the [Orangevolt EclipseXSLT](http://eclipsexslt.sourceforge.net/) plugin which allows you to run the XSLT processor from within the Eclipse workbench.


Use the XSLT processor to transform the output file from above with the stylesheet apap.xsl. You may pass a different directory as parameter workdir to the processor, but that parameter must end with a separator. The command line might therefore look like this (assuming the generated output file from the step above was named zapap.xml):
```
java org.apache.xalan.xslt.Process -in zapap.xml -xsl apap.xsl -param workdir c:\temp\
```
The processor will then generate the API documentation in the specified directory. As in the well known [javadoc HTML API documentation](http://java.sun.com/javase/6/docs/api/), the pages are divided into a frame with all packages in the upper left-hand side, a frame with all objects of the selected package in the lower left-hand side and a main frame with details of the current repository object.

The content of the main frame differs between the repository objects. Examples:
  * Domains may contain a list of fixed values or a link to a value table. ![http://apap.googlecode.com/files/sample_doma-0.2.jpg](http://apap.googlecode.com/files/sample_doma-0.2.jpg)

  * Function groups enumerate their function modules along with their parameters and links the respective data elements. ![http://apap.googlecode.com/files/sample_fugr-0.2.jpg](http://apap.googlecode.com/files/sample_fugr-0.2.jpg)

  * Classes and interfaces feature the inheritance hierarchy, their attributes, methods and events, including those inherited from super classes. ![http://apap.googlecode.com/files/sample_clas-0.2.jpg](http://apap.googlecode.com/files/sample_clas-0.2.jpg)

The main frame also provides a navigation bar at the top and at the button, with some standard elements and some object-specific entries.

# Future enhancements #
There are still a lot of things to do to improve the project. On the input side, more repository objects should be supported. At the other end, HTML files may not suffice to finalise a customer project. A customer may request the documentation in PDF format, so another set of stylesheets must be added to APAP. The new set must transform the XML file to PDF, e.g. with the [Apache Formatting Objects Processor](http://xmlgraphics.apache.org/fop/).

One goal of the project is to encourage a developer to include more documentation in the SAP system itself. However, the documentation of data elements etc. is nearly included in the output as-is. The report should take care of SAPscript commands and formats.

# Contact #

Please post questions or any other feedback at [this article in the SAP Developer Network code gallery](https://wiki.sdn.sap.com/wiki/x/X4Y).

