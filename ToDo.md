# Introduction #

This site shows a list of APAP features to be added or updated.


# Details #

## ABAP side ##

The report ZAPAP should get the following improvements:
  * Currently it is only possible to select a main package, all contained packages will be recursively processed. This could be changed that the user selects one or more packages on the selection screen which do not need to be main packages. Those packages will either not be recursively processed or there could be a selection screen checkbox to enable recursion.
  * The following repository objects have to be added: Reports

## XSLT side ##

The stylesheets should get the following improvements:
  * The following repository objects have to be added: Enqueue objects, reports

Furthermore, the should be another set of stylesheets which convert the XML to PDF (or, more specifically, to [XSL Formatting Objects](http://en.wikipedia.org/wiki/XSL-FO) which may then be used to generate PDFs).


# Contact #

Please post questions or any other feedback at [this article in the SAP Developer Network code gallery](https://wiki.sdn.sap.com/wiki/x/X4Y).