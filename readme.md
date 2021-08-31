# **WASTE REPORT DATA GRAPHICS**
## TL;DR
- Go [here](https://sv-data-projects.github.io/waste-report-graphics/) to view graphics.
- Go [here](https://docs.google.com/spreadsheets/d/1V5HL7zCHGETUCDT8-3ZiDm9XncmOTEo9WDSP1Xe3wd0/edit#gid=272100684) to edit data.
- Scroll down to custom queries for how to modify graphics with query strings.
- You are in the right place if you're trying to edit code.

&nbsp;
## About the waste annual reports graphics
This repository contains web-based data visualisations created for use SV in a series of **Annual Waste Reports**. The original development of these graphics took place in 2021 by Brendan of [Little Sketches](http://littlesketch.es/) ...so if something code-related breaks contact brendan@littlesketch.es.
It is hoped that over time however, these graphics can be updated by other contributors via code changes managed in this repo. 

> A reminder: if changes are made, features added or new visualisations or methods added....**don't forget to update this documentation!**

&nbsp;
## Known bugs and to do's
List TBA

&nbsp;
***
&nbsp;
## CONTENTS
**General references**
- [**How web based graphics work**](#how-web-based-graphics-work)
    - [A guide for context and not a 'how to'](#a-guide-for-context-and-not-a-how-to)
    - [Using the web browser for rendering graphics](#using-the-web-browser-for-rendering-graphics)
    - [Data driven graphics:connecting to editable data sources](#data-driven-graphics-connecting-to-editable-data-sources)
    - [Using JavaScript for creating visualisation layouts](#using-javascript-to-create-visualisation-layouts)
    - [Using CSS for styling (where possible)](#using-css-for-styling-where-possible)
    - [Using plain SVG for illustration layers](#using-plain-svg-for-illustration-layers)
&nbsp;
- [**How to use these graphcis in different mediums**](#how-to-use-these-graphics-in-different-mediums)
    - [i. Published web links (for viewing and embedding)](#i-published-web-links-for-viewing-and-embedding)
    - [ii. Exporting vector graphics for designed print publications](#ii-exporting-vector-graphics-for-print-design-publications)
    - [iii. Creating raster image files for MS documents (Word and PowerPoint)](#iii-creating-raster-image-files-for-ms-documents-word-and-powerpoint)
    - [iv. Embedding (iframe) on the SV website (or other web applications)](#iv-embedding-iframe-on-the-sv-website-or-other-web-applications)

&nbsp;
**Developer guidance**
- [**How code is structured**](#how-code-is-structured)
    - [Using self-contained HTML pages](#using-self-contained-html-pages)
    - [General scripting structure](#general-scripting-structure)


**User guidance**
&nbsp;
- [**Waste report graphics: visualisation options](#waste-report-graphics-visualisation-options)
    - [Using query strings for setting visualisation options](#using-query-strings-for-setting-visualisation-options)
    - [Waste report graphics and their options](#waste-report-graphics-and-their-options)
    - [Further customisation of waste report graphics](#further-customisation-of-waste-report-graphics)
    - [Interactive waste report graphics](#interactive-waste-report-graphics)


&nbsp;
***
&nbsp;
# HOW WEB BASED GRAPHICS WORK

## A guide for context and not a 'how to'
These notes provide as guidance to *help you understand how* (these) web-based data visualisations are made. They are **not** intended to be a 'how to' guide - this stuff gets quite complex - but will hopefully provide useful context for anyone trying to understand, modify and extend the code-base for these graphics. 

> These visulisations are *small web apps* that **do not use (or need) a [JavaScript framework](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks)**, but they do use JS libraries, most notably [D3.js](https://d3js.org/) as a general data/DOM manipulation and rendering tool.

&nbsp;

## Using the web browser for rendering graphics
Each visual is made with web native technologies ([HTML](https://developer.mozilla.org/en-US/docs/Glossary/HTML)/[JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript)/[CSS](https://developer.mozilla.org/en-US/docs/Web/API/CSS)) and rendered on an individual HTML webpage (or the [Document Object Model (DOM)](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model), to be more specific). This means that each visualisation is published to its own URL that can be easily shared and embedded. 

Most complex graphics are render to an [SVG element](https://developer.mozilla.org/en-US/docs/Web/SVG) on the webpage. However other [HTML elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) are used where the are appropriate: this includes grid and table-based graphics, and components such as navigation features like menus and buttons, or extensive annotations.

The combination of these technologies means that graphic outputs are always (resolution independent) **[vector graphics](https://www.adobe.com/au/creativecloud/design/discover/vector-file.html)** that are best exported as PDFs using the browser's print to PDF function (note: Google Chrome is recommended). Notes on conversion to raster graphics formats (g.g. JPG, PNG etc.) are provided below. 

&nbsp;

## Data driven graphics: connecting to editable data sources
Each data visualisation is connected to 'source data' to create visual marks and arrangements. This means that when data is updated in the source, or if the code is 'pointed' to another dataset the visual output will change. Another for way to display a different visual output is by changing a visualisations (pre-programmed) 'settings' (see the [Visualisation options](#visualisation-options) section)

> Data is currently 'hosted' as tab separated values files from a Google Sheets spreadsheet. 
> - The link to the current Google sheet is [here](https://docs.google.com/spreadsheets/d/1V5HL7zCHGETUCDT8-3ZiDm9XncmOTEo9WDSP1Xe3wd0/edit#gid=0). 
> - The reference URLs are contained either directly in each visualisation's script, or in the case of the Market Bulletin graphics, in a shared data URLs object in 'core' folder (as a single object to update).


Connections to these tables is made using the [D3.js library's](https://d3js.org/) [Fetch API-based](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) date loaders. This setup means that URL links can be easily switched over to more secure hosting arrangement in the future. The trade off is that the Google Sheet based files can be easily shared, accessed and updated without knowledge of a host database.

*Side note: Data as originally connected via the more convenient and full featured [Tabletop.js](https://github.com/jsoma/tabletop) library however this was deprecated alongside the Google Sheets API (v3 to v4) migration in early August, 2021. The [PapaParse.js](https://www.papaparse.com/) library was then successfully implemented but introduced a further dependency (alongside D3.js), and so D3.js as eventually used as the simplest solution with least library dependencies (as its generally used here for most data visualisations).*

&nbsp;

## Using JavaScript to create visualisation layouts
Each data visualisation typically uses a combination of vanilla JavaScript and the [D3.js library](https://d3js.org/) to load and shape the data, before rendering the data-driven elements on screen as SVG. 

That is a relatively simple 'workflow' however D3 is a low level library with an imperative code-style. This generally leads to fairly lengthy and involved code-bases and is **generally only recommended** for highly customised data graphics. 

For basic charts, its much easier to use another library or application (a bunch of which are built on top on D3). The main advantage of D3 is that - as as low level API operating on web standard technologies (HTML/CSS/JS) - it is incredibly flexible and plays well with anything other web features and libraries. This means that visualisations can be just another (native) part of a larger web application. 

&nbsp;

## Using CSS for styling (where possible)
As these visualisations use web standards, most of the styling is contained in [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) files and assigned to elements with with [CSS selectors](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Selectors). This might not mean much to anyone that isn't familiar with CSS, but in short, it means that a **most of the colours and typographic styling can be found in the the CSS files**.

Colours are mostly referenced throughout the CSS using [CSS Variable names](https://developer.mozilla.org/en-US/docs/Web/CSS/--*). This is so that the data visualisation palette (including all Brand Guideline colours) can be controlled from one central **core.css** file. Updates and extensions to the colour palette should be done in this core stylesheet.

**The notable exception to using CSS is usually when data-driven styling is applied to SVG elements inline, through the D3.js code. In these cases, look for colour references in the JS code to update colours.**

&nbsp;

## Using plain SVG for illustration layers
Some visualisations contain more illustrative graphic layers. These are SVG-based graphics and illustrations whose code is copied into HTML code (note: SVG is web-native HTML). This SVG code can be directly edited but for the most part, these illustration were created in a vector graphics editor, cleanup up in the [SVG OMG](https://jakearchibald.github.io/svgomg/) tool, and imported into the HTML code. 
Further (data vis) SVG is added over the top using JavaScript and [D3.js](https://d3js.org/) 

&nbsp;
***
&nbsp;

# HOW CODE IS STRUCTURED
[Return to contents](#contents)

The following is a rough outline of how the code for each visualisation is structured. Not all visualisations follow the exact same structure (mainly due their varying complexities). Again, treat this as a *how it was done* rather than *how to* guide.

> The best way to read code is to look through it. Extensive code comments have been included in all scripts to help describe the roles of each function and object.

&nbsp;

## Using self-contained HTML pages
The Annual Report graphics include the both CSS and JS inside the HTML file. This is not common practice - usually stylsheets and scripts are linked to separate .css and .js respectively - but works fine and was done to keep each visualisation as a relatively small and self-contained 'app'. This *should* make it a bit easier for novice developers to follow and make simple updates as  (almost) 'everything is in one place'. The exception is that there is a 'core' stylesheet (core.css) shared between all visualisations: this file simply contains the shared colour palette and font loaders. In each visualisation html file there is:

- CSS for that visualisation within the [style tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/style) (within the [head](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head)) section near the top

- A [body](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/body) element that will usually contain an SVG element where the data visualisation will be rendered. Unless there is an SVG illustration copied int the visual, the SVG element will be empty. HTML-based visualisations might instead have a [main](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main) element where the script will attach more DOM elements.

- A [script](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script) element at the bottom of the document: this is important bit that contains the visualisation instructions and is discussed in more detail below...

&nbsp;

### Global Objects
A handful of global objects (variables) are consistently structured used in all visualisations:
- `settings` object containing: a reference to the SVG element id (i.e. where to 'draw'), dims (i.e. canvas dimensions), and other settings like 'year' or 'date' (and others). A 'setting.geometry' reference is also included for more complex visualisations, however this is often defined later in the script (in the `renderVis()` or `renderMap()` function). This is generally because geometry settings use the canvas dimensions are requires the `settings.dims` property  to have been declared first; however other more custom layout data can also declared this way (e.g. the custom label positions on the [WRRG Data map]() )

- `data` object containing: the (parsed) loaded data and any 're-shaped' data required to support the visualisation code. It is stored in a global object for convenience, referencing and debugging. 

- `helpers` object: this appears at the end of the script and contains various utility functions for [number formatting](https://github.com/d3/d3-format) and   data parsing, creating [slug](https://developer.mozilla.org/en-US/docs/Glossary/Slug) names for classes etc. Not all of these are used in every visualisation.

&nbsp;

### The basic build sequence
- `buildFromXxx()`: the build function can be thought of as the initiation function. It is the only function directly called once the webpage load. It's purpose is to: 
    1. Connect to all required data files and and load them to the webpage. 
    2. Parse any numerical data from `text` to `number` type (using the `parseTable` helper function)
    3. Call the `applyQuerySettings()` to apply any query string options sent as part of the URL request. This function looks for any specified settings and updates the settings object if they are present. If not, no updates are made (i.e. the visualisation defaults to the settings object)
    4.  Call the `transformData()` function that will perform any data wrangling, shaping  and merging tasks required, including generating uniques lists. Data is stored back to the `data` object.
    5. Call the `renderVis()` (or `renderMap()`) function which uses the `data` object to render the visualisation to the webpage. 
    6. Call any other custom functions that might exist (e.g. `animateVis()`).  

&nbsp;
***
&nbsp;
# HOW TO USE THESE GRAPHICS IN DIFFERENT MEDIUMS
[Return to contents](#contents)
## i. Published web links (for viewing and embedding)
As these graphics are rendered in the browser, this repo has also published to [Github pages](https://pages.github.com/) so that they can be easily shared an embedded. In this way,  Github pages is used to as a convenient static web server for publishing these graphics, and can be used as links to embed graphics in other webpages and apps, including the SV CMS.

An HTML index page has also been added to the repo (index.html) that provides a user friendly single access point to all graphics [here](./index.html)

If (in the future) other hosting services are preferred, they can (most likely) be linked to this Github repo. 

&nbsp;
## ii. Exporting vector graphics for designed print publications
As mentioned, SVG and HTML based graphics export (via print to PDF) as vector graphic files. Vector files are generally the highest 'publication quality' format for data visualisation that can be embedded and/or further edited in publication software like Adobe Illustrator and InDesign, or Affinity Design and Publisher. [Inkscape](https://inkscape.org/) is also a (free) open source alternative for vector graphics.These software packages can also export in other common graphic file formats (e.g. PNG and JPG) from these software packages.

&nbsp;
## iii. Creating raster image files for MS documents (Word and PowerPoint)
For MS Word documents, embedding vector graphics is problematic on Windows based machines (at the time or writing, where embedding PDF is possible however they become rasterised when saved). This is not problem on Mac OS machines where MS Word and PowerPoint happily accept and vector PDF files. 

> The fallback for Windows users who don't have access to graphics software is to convert from PDF to PNG (as the preferable raster image format).

&nbsp;
## iv. Embedding (iframe) on the SV website (or other web applications)
Certain graphics have been designed to be embedded in another web page or application - most notably the SV CMS. To accommodate this embedding, each graphic is designed to be responsive and will stretch to the width of the browser page. This includes HTML text and spacing that is specified in [viewport width](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units) units so that it has the same resizing behaviour as SVG elements. 

> **Design considerations for the SV website:**
> - The current SV website has quite a narrow, centered content block. Data visualisations are embedded as into this content flow, meaning that they appear quite narrow on screen. Data visualisations intended for embedding on the SV website need to consider this limitation.
> - Every embedded data visualisation does also have a 'view full screen option': this allows some flexibility to design for full screen. 
> - The SV CMS requires that embedded graphics contain the [iframe resizer](https://github.com/davidjbradshaw/iframe-resizer) code snippet. This snipper is added as a CDN link to each graphic. All newly made graphics should be published with this snippet.   
> - The SV CMS embeds html based graphics using the "Advanced data visualisation (D3)" component that allows for any [iframe](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) to be specified. 


&nbsp;
# WASTE REPORT GRAPHICS: VISUALISATION OPTIONS
[Return to contents](#contents)

In most cases, graphics are designed to be flexible enough for use in print and digital mediums. The exceptions are mostly interactive graphics that have information designed to be displayed only when a user interacts with part of the graphic. 

&nbsp;
> ## Using query strings for setting visualisation options
> Most data visualisations have been developed to accept a [query string](https://en.wikipedia.org/wiki/Query_string) that is added to its URL. Query strings are simply an specially formatted text string at the end of a URL, after a '?". You can think of a query string as sending a bunch of 'settings' for the data vis application to apply. This means that the application must first be setup to do something with query string it receives. The table below outlines what options are available for each graphic.
>
>You can read about how query strings are structure [here](https://en.wikipedia.org/wiki/Query_string#Structure) - it'll only take a few seconds or minutes to work out. But another simple way to figure them out is to see an example: a visualisation might be setup to receive a 'year' parameter from a query string, in a financial year format like '2018-19'. It would  then use that 'specified year' to display the right data. 
> - The query string would be simply be **'?year=2018-19'** and the URL would look something like www.xxxx.com/vis.html?**year=2018-19**. 
> - Multiple parameters can be appended wih an '&'. So www.xxxx.com/vis.html**?year=2018-19&showGlass=false** would also send the value 'false' for the parameter 'showGlass'.
>
> And thats all there is to query strings!

&nbsp;

## Waste report graphics and their options
| Visualisation name | html filename | Query parameter name | Query parameter value format |  Query parameter default |  Query parameter description
| --- | --- | --- |---| ---| ---|
| Victorian waste flow illustrative graphic (full page) | waste-vic-flow-infographic.html | year | YYYY-YY |  2019-20 | Specifies FY data to display
| | | extended-typography | true, false | true |  Show title text with with handwriting typeface
| Victorian waste flows summary graphic (half page 'flow loop')| waste-vic-flow-summary-graphic.html |  year | YYYY-YY | 2019-20 | Specifies FY data to display
| | | mode | dark, light | light | Show the graphic with dark mode colour scheme
| Kerbside waste illustrative summary infographic (half page)| kerbside-flow-infographic.html |  year  | YYYY-YY | 2019-20 | Specifies FY data to display
| | | extended-typography | true, false | true | Show title text with with handwriting typeface
| Kerbside waste illustrative infographic (full page)| kerbside-summary-infographic.html | YYYY-YY |  year | 2019-20 | Specifies FY data to display
| | | extended-typography | true, false | true | Show title text with with handwriting typeface
| Kerbside bin composition graphic (single bin)| kerbside-bin-composition.html | year | YYYY-YY | 2019-20
| | | yellow-bin-lid | true, false | false | Whether to colour bin lid yellow (note: default is false colour clashes with material colour)
| Victorian waste flow (sankey) diagram (full page)| waste-vic-flow-sankey.html |  year | YYYY-YY | 2019-20 | Specifies FY data to display
| Waste metrics trends tables (headlines and by material summary) | waste-trends-summary-table.html |  year | YYYY-YY | 2019-20 | Specifies FY data to display
| Waste diversion radial bar chart and table | waste-diversion-by-material.html |  year |  YYYY-YY | 2019-20 | Specifies FY data to display
| | | mode | dark, light | light | Show the graphic with dark mode colour scheme
| | | layout | portrait, landscape, chart-only | portrait | Layout options for chart and accompanying data table (or to not show the table)
| Victorian recovered waste export map | waste-export-map.html |  year |  YYYY-YY | 2019-20 | Specifies FY data to display
| Victorian WRRG waste data map | wrrg-map.html|  year |  YYYY-YY | 2019-20 | Specifies FY data to display
| | | showGlass | true, false | true | Whether to show the layout with glass collection data (for WRRGs with collection/data)
| | | colourScale | ramp, categorical | ramp | Whether to show the WRRG regions with data encoded in a colour ramp, or as multi-coloured categorical colours (not recommended)

## Further customisation of waste report graphics 
Some visualisations can be tweaked and customised further:
- **Victorian recovered waste export map:** Customisation of the country to country links can be made via the source data table ('exportWasteData'). This allows for the curved links to be adjusted by country, and for the 'path endpoint' to be adjusted
- **Victorian waste flow (sankey) diagram:** The sankey layout is customised within using hte data fields of 'level' and 'sourceKey' (and fitered by the field 'sankey') in the source data table ('wasteByMaterialFlow'). These layout configurations reasonably complex and adjustments are likely to require some code changes.

## Interactive waste report graphics
Section TBA: this will cover any animation or customised interaction options for web-first graphics (currently being considered)

