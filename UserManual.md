# Introduction #

This is a short manual how to use APAP to generate documentation from SAP repository objects.

# Details #

Follow these steps:

### Generate the XML file from the repository objects ###

If you have not yet done so, import the report ZAPAP into your SAP system. You can either use [SAPlink](http://www.saplink.org) to import the Slinkee [PROG\_ZAPAP.slnk](http://apap.googlecode.com/svn/trunk/PROG_ZAPAP.slnk) or create the report ZAPAP with transaction SE38 and copy'n'paste the report text [PROG\_ZAPAP.txt](http://apap.googlecode.com/svn/trunk/PROG_ZAPAP.txt) into it.

The selection screen
  * **Application**: Enter an arbitrary name which will show up in the upper right-hand corner of the HTML pages (optional)
  * **Package**: Choose one or more packages to be exported.
  * **Recursively select subpackages**: In case you have a package hierarchy, packages will be processed recursively. In this case, it suffices to enter the top-level package in the **Package** field.
  * **Object types**: Select the repository object types to be included in the output.
  * **Include system table content**: Mark this checkbox if you want to add the content of system tables (type S') in the output file.
  * **Language**: Enter the language in which the object descriptions and documentations will be retrieved.
  * **Download to**: Use the F4 help to select a destination file to write the output to.

The report may run a while, depending on the size of the selected packages. It will then display the created output as a list while saving it to the specified destination file.

### Generate destination format from XML file with XSLT ###

The other [files of the project](http://apap.googlecode.com/svn/trunk/) take care of xslt-processing the APAP XML file to the destination format. Currently, only HTML is supported.

Unfortunately, the master file [apap.xsl](http://apap.googlecode.com/svn/trunk/apap.xsl) contains some directions proprietary to [Xalan](http://xalan.apache.org/). So you are currently restricted to using this XSLT processor.

If you have not yet done so, set up the XSLT processor:
  * Maybe your system already features the XSLT processor. For example, the Ubuntu distribution already provides the Xalan processor in the default installation (I think).
  * Otherwise set up the processor according to the instructions at the [Xalan homepage](http://xalan.apache.org/).
  * Another possibility is to use the [EclipseXSLT plugin](http://eclipsexslt.sourceforge.net/) which allows you to run the XSLT processor from within the Eclipse Workbench.

Use the XSLT processor to process the output file from above with the stylesheet [apap.xsl](http://apap.googlecode.com/svn/trunk/apap.xsl). You may pass a different directory as parameter `workdir` to the processor, but that parameter must end with a separator. The command line might therefore look like this (assuming the generated output file from the step above was named zapap.xml):
```
java org.apache.xalan.xslt.Process -in zapap.xml -xsl apap.xsl -param workdir c:\temp\
```

The processor will then have generated an API documentation in the specified directory.

# Contact #

Please post questions or any other feedback at [this article in the SAP Developer Network code gallery](https://wiki.sdn.sap.com/wiki/x/X4Y).