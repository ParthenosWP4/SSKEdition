
#SSK EDITION tool
This is the SSK scenario creation and update tool.
It is based on the TEIMETA software, see doc below.

# teimeta-edit

**An editor (generated automatically from a ODD description) for subparts of an XML file**

## Downloading or using the tool

Online use: [http://ct3.ortolang.fr/teimeta/](http://ct3.ortolang.fr/teimeta/)

Downloading the tool for MacOs: [http://ct3.ortolang.fr/download/teimeta-macos.zip](http://ct3.ortolang.fr/download/teimeta-macos.zip)

Downloading the tool for windows 64: [http://ct3.ortolang.fr/download/teimeta-x64.exe](http://ct3.ortolang.fr/download/teimeta-x64.exe)

Downloading the tool for windows 32: [http://ct3.ortolang.fr/download/teimeta-x86.exe](http://ct3.ortolang.fr/download/teimeta-x86.exe)

Downloading the TEI Spoken language ODD: [http://ct3.ortolang.fr/teimeta/teispoken.odd](http://ct3.ortolang.fr/teimeta/teispoken.odd)

Downloading the TEI Media ODD: [http://ct3.ortolang.fr/teimeta/media.odd](http://ct3.ortolang.fr/teimeta/media.odd)

Downloading the DC/OLAC ODD: [http://ct3.ortolang.fr/teimeta/olac.odd](http://ct3.ortolang.fr/teimeta/olac.odd)

## Description

TEIMETA is a tool for editing XML files. The editing structure, possibilies, and information are described in an XML ODD TEI file. TEIMETA uses this description (the model) to produce automatically a user interface in a webbrowser or an independant Electron application.

Integration of the tool in another application is possible through two different method:
1) integrate the library as a plugin in another website, using your own functions to load and save the data
2) redesign the HTML interface using the full power of the tool.

The tool was created to edit TEI files, but it can be used for any type of XML format. XML schema control of the XML created files is not (yet) implemented. The quality of the final files is automatically controled by the description (the ODD model) but the quality of what the model generates is not controlled by TEIMETA (note that it would be very difficult to control that the ODD are garantied to allways build correct XML files).

TEIMETA allows to edit any XML node but not to move this node around in the XML file. New nodes can be created and nodes can be erased (if this is allowed by the model). The ODD describes node position from the root. **The values of an XML node not described in the ODD file will never be modified. This means that it is possible to edit parts of an XML file respecting the integrity of the rest of the file.**

## Version information
  - 0.6.5 - resize for multiline and correction of errors and correct reading of files with BOM header
  - 0.6.3 - corrections of small errors
  - 0.6.2 - fully documented version
  - 0.5.2 - improved style of CSS for all display size
  - 0.5.1 - full English or French version
  - 0.5.0 - 2 may 2018 - ODD files can describe the visual presentation of the data edited using CSS features.
  - 0.4.9 - feb 2018 - correction of bugs and namespace implementation

## Organization of the data

The main editing features are:
- creating a node, adding node to other nodes
- editing a node with a specific format
- editing attributes from a node with a format
  - for the two previous cases, editing can include
  - free text
  - vocabulary (open or closed)
  - number
  - date
  - duration
  - languages, countries

## Editing the ODD and XML examples

The ODD is using the TEI support for inserting tags into the TEI. Actually, we don't do that but
we use desc field to provide rich information that will be provided to the user when editing data.
The corresp, rend et rendition have a different fonctionnaly than in the TEI. They allow to describe
the control of the data edited by TEIMETA. This is also the case for the remarks tag which is used
to describe the visual presentation of the data to be edited. The other fields follow the TEI guidelines.

### Description format for the XML nodes

The ODD is made of elementSpec elements that can refer (using elementRef) to other elements of the same type. Each elementSpec contains the description of the node content, attributes, and pointer to descendants.
moduleRef are not used.

  * schemaSpec
    * start = root for the result xml
    * ns = namespace for the result xml
    * rend = name of the optional css file
    * altIdent = sub node that contains all material that goes in the header of the result file

  * elementSpec
    * ident="name of the tag in the TEI" corresp="optional tag to be used when ident is not unique"
    * desc: multilingual description (see xml:lang)
    * content: content of the node, subelement and text node
    * attList: list of the attributes
    * remarks: CSS presentation of an elementSpec and/or the content field
      * ab: raw CSS content
      * p/ident: CSS class name (to be found in the CSS file prodive in the rend attribute of schemaSpec)

  * content
    * sequence minOccurs maxOccurs (if there is more than one element)
      * elementRef key="name of the tag in the TEI" corresp="optional tag to be used when ident is not unique"
      * elementRef minOccurs maxOccurs (min and max are optionals: if omitted their value is 1)
    * textNode
      * dataRef : to be indicate the format of the node (instead of text)
      * integer decimal NCName string duration anyURI data open closed - if nothing, then no format control
      * valList : to be used if the node can take a series of values

  * attList
    * attDef ident="attribute-in-the-TEI" rend="default value of the attribute" usage="rec or req"
      * desc (multilingual)
      * desc = name of the section, information for the user
    * datatype
      * dataRef : format of the attribute
      * ... or a list of values: if only one they this is the default value
      * remarks: CSS presentation of the datatype editing field
    * valList ==> multiple choices presented to the user
      * valItem ident="value-in-the-TEI"
      * desc (multilingue) + rendition (optional help)
      * remarks: CSS presentation of an elementSpec and/or the content field
        * ab: raw CSS content
        * p/ident: CSS class name (to be found in the CSS file prodive in the rend attribute of schemaSpec)

  * classRef
    * this allows to include a OOD into another ODD so as not to repeat elements defined otherwhere.
    * key: display name of included file
    * source: name of the included file (with classRef all ODD must have an http address - the included file must be in the same web directory than the main ODD)
    * in this case, elementSpec defined in the included files are superseeded by elementSpec with the same name in the main ODD

#### Example

```
<schemaSpec ident="NameOfOdd" ns="http://basic-namespace.org" start="olac:olac"
					 	rend="http://ct3.ortolang.fr/teimeta/olac.css">
            <altIdent type="xmlns:olac">http://www.language-archives.org/OLAC/1.1/</altIdent>

<classRef key="filedesc" source="filedesc.odd"/>

<elementSpec ident="tag_TEI" module="header"
    corresp="name_for_a_unique_identification">
    <desc xml:lang="fr">... in French ... - partie affichée pour décrire
        l'élément</desc>
    <desc xml:lang="en">... in English ... - information for the user</desc>
    <content>
        <sequence minOccurs="0" maxOccurs="unbounded">
            <elementRef key="sub_tag_1"
                corresp="another_name_for_unique_identification"/>
            <elementRef key="sub_tag_2"/>
        </sequence>
        <elementRef key="idno" corresp="handle" minOccurs="1" maxOccurs="unbounded"/>
        <textNode/>
        <dataRef name="NCName"/>
    </content>
    <attList>
        <attDef ident="mimeType" usage="req" mode="change">
            <valList type="closed">
                <valItem ident="audio/wav"/>
                <valItem ident="audio/mp3"/>
                <valItem ident="other"/>
            </valList>
            <!-- this will display the current attribute (mimeType) as it is described in the <ab> field -->
            <remarks>
                <ab>color:red; width: 400px;</ab>
            </remarks>
        </attDef>
        <attDef ident="dur-iso" usage="req" mode="change">
            <desc xml:lang="fr">durée du média : chaque medias peut avoir une durée
                différente</desc>
            <desc xml:lang="en">media duration : each media could have a different
                duration </desc>
            <datatype>
                <dataRef name="duration"/>
            </datatype>
        </attDef>
    </attList>
    <!-- this will display the elementSpec block as it is described in the <ab> field -->
    <remarks style="element">
        <ab>background-color: lightgreen; border-radius: 8px; margin: 3px; margin-top: 10px;</ab>
        <p><ident>css-classname-for-element</ident></p>
    </remarks>
    <!-- this will display the texnode/datatype in the content part as it is described in the <ab> field -->
    <remarks style="content">
        <ab>color: blue; width: 500px;</ab>
        <p><ident>css-classname-for-content</ident></p>
    </remarks>
</elementSpec>
```

### Use as a library in another software using basic library (implemented)
TEIMETA is written using typescript and it can be easily interface with javascript or typescript using the distribution file or directly from the sources.

Include *lib.js and directory fonts* (found in folder dist)
Instructions are within lib.ts file in teimeta folder in sources.
Five functions that are sufficient to use teimeta as a library are integrated in the global object:
    window.teimeta.loadXmlOddCss - load from raw data
    window.teimeta.readXmlOddCss - load from url addresses
    window.teimeta.finalizeHTML - finalize HTML display
    window.teimeta.generateXml - get results of edition from the user
    window.teimeta.teimetaParams - set parameters
Two supplementary function are available for easy implementation of a standalone HTML page software such as TEIMETA.
    window.teimeta.readTextFile - read an url as raw data
    window.teimeta.saveFileLocal - save raw data as a local file in the download section

#### EXEMPLE
```
<html>
<script src="../dist/lib.js"></script>
<style>
pre {
    white-space: pre-wrap;       /* Since CSS 2.1 */
    word-wrap: break-word;       /* Internet Explorer 5.5+ */
}
</style>
<body>
  <span><b>Please edit the data and then</b></span>
  <button id="test102" type="button" onclick="saveTheData();"><b><em>Save the data</em></b></button>
  <hr/>
  <div id="teimeta"></div>
  <pre id="info">Nothing yet.</pre>
</body>
<script>

window.teimeta.teimetaParams.defaultNewElement = false;
window.teimeta.readXmlOddCss('http://localhost/test/test102.xml', 'http://localhost/test/test102.odd', null,
  function (h) {
    var el = document.getElementById('teimeta');
    el.innerHTML = h;
    window.teimeta.finalizeHTML();
  });

function saveTheData() {
  var r = window.teimeta.generateXml();
  window.teimeta.saveFileLocal('xml', 'test102-result.xml', r);
  var el = document.getElementById('info');
  info.textContent = r;
}

</script>
</html>
```

### Use as a library in another software using the full implementation (implemented)
Include the dist directory minus lib.js (unneeded) and teimeta.html (or you can redesign this file). This takes advantage of all functions implemented. It only requires to redesign the HTML page. The software can runned calling all internal basic functions.
Include *bundle.js and directory fonts* (found in folder dist)

  - window.teimeta.openXml() - ask the user to find an XML file on his computer (warning: a hidden tag with id 'upload-input-transcript'
  must exist in the HTML file - for exemple <div id='upload-input-transcript'></div>)
  - window.teimeta.newXml(choice) - create an empty Xml data with an ODD - choice can be set to 'previous'. In this case the ODD loaded
  in memory is used
  - window.teimeta.openOdd() - ask the user for an ODD and create new XML with it.
  - window.teimeta.openCss() - ask the user for a CSS and wait for further processing.
  - window.teimeta.cleanCss() - reset the value of the css data included in the presentation of an odd.
  - window.teimeta.saveLocal() - save the content of the XML in the download directory.   
  - window.teimeta.saveAsLocal() - ask for a file name and save the content of the XML in the download directory.   
  - window.teimeta.oddParams() - open the parameter box for the user to choose parameters
  - window.teimeta.setLanguage(lang) - set the language in the software and in the ODD - values: fre, eng, spa, jpn
  - window.teimeta.showAll() - expands all editing boxes
  - window.teimeta.hideAll() - reduces all editing boxes
  - window.teimeta.dumpHtml() - download the content of the innerHTML (useful for checking the HTML data and testing)
  - window.teimeta.emptyFile() - clean the data
  - others - see exports in events.ts and common.ts

### Use the sources with require/import

For people which want to use the source code, then they can download it and then use the require() / import features of javascript or typescript. For example:

  - import * as teimeta from 'teimeta/teiedit/teimeta'; // equivalent to the features of lib.js - see above
  - import * as events from 'teimeta/ui/events'; // equivalent to the features of bundle.js - see above
  - import * as common from 'teimeta/ui/common'; // see events.ts and common.ts - see also init-singlepage.ts

Download source code and distribution: [https://github.com/christopheparisse/teimeta/](https://github.com/christopheparisse/teimeta/)

Issues, questions and comments [issues](https://github.com/christopheparisse/teimeta/issues).

#### License [CC0 (Public Domain)](LICENSE.md)
