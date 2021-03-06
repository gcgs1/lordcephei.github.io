---
layout: page-fullwidth
title: "Site Formatting Guide"
permalink: "/formatting/"
header: no
---

### _Purpose_
{:.no_toc}
_____________________________________________________________
Should you wish to add to or edit the tutorials and documentation on this website, this guide will cover the site-wide formatting in use to maintain consitency. Syntax specific to the site will also be outlined in the relevant sections.

{:toc}  

### _Table of Contents_
{:.no_toc}
*  Auto generated table of contents
{:toc}  

### _Preliminaries_
_____________________________________________________________
The site uses [Jekyll](https://jekyllrb.com/)  to parse Kramdown files in to HTML which can then be hosted. To edit the site, you will need access to the GitHub repository used to host the site (currently the site is hosted on GitHub Pages). For access to this repository please contact -. Once you have access simply clone the directory, make your changes and push to the repo. All the Jekyll site compliation is done server side by GitHub Pages.

It would be of benefit to read through the Kramdown documentation [here](http://kramdown.gettalong.org/syntax.html) and the theme specific formatting [here](http://phlow.github.io/feeling-responsive/) to get an idea of what is used on the site and, further, what is possible. Some syntax in this guide is specific to the site and will be detailed however some more general syntax will not be explained as the aim here is to outline the formatting rules. Should you wish to know what specific syntax elements are doing, refer to the Kramdown documentation linked previously.

### _Site Formatting_
_____________________________________________________________

##### _General Document Style_
The document should start with a "Purpose" section and a "Preliminary" section, where "Purpose" contains the purpose/aims of the tutorial and "Preliminary" contains the prerequesites, recommended reading or guidelines that should be read before progressing with the tutorial.
Next should be the content of the document, split up in to short and easily followed sections (large tutorials and documents should be split in to multiple smaller tutorials and documents where possible).
An 'Other Resources' section can be supplied if there are tutorials/papers/other useful documents that are relevant to the current document.
An 'Issues or Comments' section can be included with contact details should the reader have an issue or suggestion about the document.

Tutorials, unless they are very involved (e.g. the DMFT tutorial) should have a "Command Summary" section that assembles all the commands in the tutorial together in one place, for quick reference.   Thus tutorials generally should contain the following sections, in order shown.  You can use the [basic lmf tutorial](https://github.com/lordcephei/lordcephei.github.io/blob/master/pages/fpintrotut.md) for a template.

    Purpose
    Table of Contents (optional)
    Preliminaries
    Command summary (optional)
    Tutorial/Documentation
    Other Resources (optional)
    FAQ (optional)
    Additional Exercises (optional)

##### _Table of Contents_
Documents with multiple references or subsections should contain a table of contents, usually before any other text. Jekyll/Kramdown has support for auto-generating this ToC as shown here:

~~~
### _Table of Contents_
{:.no_toc}
*  Auto generated table of contents
{:toc}  
~~~

Adding the

    {:.no_toc}

flag directly under a header will exclude that category and its subcategories from the ToC autogeneration. 

##### _Front Matter_
Front matter is always placed at the top of *.md files. Jekyll uses this data to format the page in question. More information on the capabilities of the Front Matter can be found [here](http://jekyllrb.com/docs/frontmatter/). The Front Matter used, for example, this page is:

~~~
---
layout: page-fullwidth
title: "Site Formatting Guide"
permalink: "/formatting/"
header: no
---
~~~

Here we can see that our title is "Site Formatting Guide" which you can verify yourself above. Our link is also "/formatting/" which, if you check your current url, should also agree with the config.

An important note about the permalink: This is where your file is found on the site. To give an exmaple, lets say you have a file _test.md_{: style="color: green"} which has a frontmatter with

    permalink: "/tutorial/lmf/"

When trying to find your tutorial on the site, you will not find it under

    https://lordcephei.github.io/test.md/ or https://lordcephei.github.io/test/

But rather, you will find it under

    https://lordcephei.github.io/tutorial/lmf/

Which is what we defined in the front matter. Of course, these variables should be tailored for the page in question.

##### _Front Matter: Using The Permalink_
The site does not naturally use folders due to how **Jekyll**{: style="color: blue"} works. However to simulate files and pages being in different folders, enabling easier navigation or just simply looking better, we can use the "permalink" to pretend that our file is within a folder.

**Jekyll**{: style="color: blue"} will allow a permalink such as

    /test/tutorial/

Just as much as 

    /tutorial/

The site utilizes this functionality by grouping documentation and tutorials together, and further subdividing them by dropdown level. Lets construct a permalink. We have a file _lmf\_dos.md_{: style="color: green"} which is a _tutorial_ that we would store in the _lmf_ section of the tutorial dropdown (if you navigate to the site [here](https://lordcephei.github.io/) and hover over "Tutorials" you will see a "lmf" section, this is where we would like our file to show).   

If we click on a tutorial in the Tutorial>lmf dropdown, we will see that it's url starts with "/tutorial/lmf/", as would be expected. So to construct our front matter, we will start with "/tutorial/lmf/" and add our desired identifier at the end. In the end, our front matter may look something like

    permalink: "/tutorial/lmf/dos"

The url prefixes of the different sections can be found in the _\_data/navigation.yml_{: style="color: green"} file, under the section "prefix". To give a few common examples:

Section | Parent | Prefix | Resultant URL
- | - | - | -
Documentation | - | /doc/ | /doc/
Input Files | Documentation | /input/ | /doc/input/
Tutorials | - | /tutorial/ | /tutorial/
LMF | Tutorials | /lmf/ | /tutorial/lmf/
DMFT | Tutorials | /dmft/ | /tutorial/dmft/

##### _Headers_
Headers are nested automatically in the table of contents generation by number of # used. Generally, the site uses ### for primary headings and an extra ## for each subheading under that. For example, your primary heading may be ###, a subheading ##### and a subheading under the first subheading #######. In addition, headers are italicized with single underscores surrounding the header (\_Header\_).

##### _Colours_
The site uses colours to denote certain things. **Blue**{: style="color: blue"} is used for program/package names, terminal commands and scripts, such as the **lm**{: style="color: blue"} package or the console command **cd**{: style="color: blue"}. _Green_{: style="color: green"} is used for directories or files, such as the _ctrl.si_{: style="color: green"} file. **Red**{: style="color: red"} is used for important points such as a **Note:**{: style="color: red"}. Colouring items can be achieved with:

    **Placeholder Text**{: style="color: blue"}

"blue" can be changed to "red", "green" or any standard colour. Note that to colour a string (with or without spaces) there must be a text emphasis element (such as \_ \_ or \*\* \*\* etc) surrounding the string.

##### _Text Emphasis_
**Bold** is used for programs/package names, noteworthy points (**Note:**) and the names of input file tags (e.g. **SITE\_ATOM\_POS**). _Italics_ are used for directories, files, headers and scripts (or uncompiled programs, i.e. not executabels).

##### _Text Emphasis And Colours: A Summary_
Here we have a table of combinations of text emphasis and colours that should be applied throughout the site for each given object.

_Note:_{: style="color: red"} This list should contain all of the commonly needed formatting rules, if some are missing please contact us and let us know. There may be some niche cases that aren't covered here, if you find one feel free to contact us and we can include it in the table.

Item | Text Emphasis | Colour | Result
- | - | - | -
Directory | \_italic\_ | _green_{: style="color: green"} | _directory_{: style="color: green"}
File | \_italic\_ | _green_{: style="color: green"} | _file.md_{: style="color: green"}
Source Code | \*\*bold\*\* | **green**{: style="color: green"} | **bndasa.f**{: style="color: green"}
Input File Tags | \*\*bold\*\* | None | **SITE\_ATOM\_POS**
Scripts and Subroutines | \_italic\_ | _blue_{: style="color: blue"} | **script.py**{: style="color: blue"}
Package Names | \*\*bold\*\* | **blue**{: style="color: blue"} | **lmf**{: style="color: blue"}
Terminal Commands | \*\*bold\*\* | **blue**{: style="color: blue"} | **ls**{: style="color: blue"}
Note | \_italic\_ | _red_{: style="color: red"} | _Note:_{: style="color: red"}
Warning | \*\*bold\*\* | **red**{: style="color: red"} | **Warning:**{: style="color: red"}

##### _Code Blocks_
Code blocks, as would be expected, should be used for any code, input or outputs (such as the _ctrl.si_{: style="color: green"} input file), terminal commands and general instructions. Code blocks are always on a new line so it is not suitable if inline text is desired.  

Code blocks can be defined with either a 4-space indentation relative to the current indentation (so if your block of text has a 4-space indentation already, your code block would need to be 8-space). Code blocks can also be denoted with ~~~ at the open and close of a block.

~~~~~
~~~

Code here

~~~
~~~~~

##### _Drop Down Boxes_
Drop down boxes allow for hiding of information unless the user manually opens it, enabling tutorials and documentation to be condensed and easier to follow while not removing information. Drop down boxes are not included within standard Kramdown formatting and are implemented with html. To use a dropdown box, the syntax for a medium box is:

<div class="dropButtonMid" onclick="dropdown( this );">Click to show.</div>
{::nomarkdown}<div class="dropContent">{:/}

~~~
<div class="dropButtonMid" onclick="dropdown( this );">Click to show.</div>
{::nomarkdown}<div class="dropContent">{:/}

Content

{::nomarkdown}</div>{:/}
~~~

{::nomarkdown}</div>{:/}

There are two different ways to implement this html. The method shown above finds the next _"dropContent"_ div container and the button assumes control over it. This is the default action, the easiest and probably the one you want. If for some reason you want to define the button **not** next to the _"dropContent"_ div, e.g. 

    ...class="dropButtonMid"...
    some other markdown
    ...class="dropContent"...

Then you must manually set the target. To do this, you must change _dropdown( this );_ to _dropdown( this, tar );_ where _tar_ is the id name of the _"dropContent"_ box (we will see what this is next). You must then set the ID of the _"dropContent"_ div to tar, e.g. your end result should look like:

    <div class="dropButtonMid" onclick="dropdown( this, 'foobar' );">Click to show.</div>
    {::nomarkdown}<div id="foobar" class="dropContent">{:/}
    
    Content
    
    {::nomarkdown}</div>{:/}

Note _"foobar"_ used in the two locations. If you use this method, then _foobar_ **must** be unique for each dropdown box you make. You should also use this method if you encounter errors or broken dropdown boxes - this method is more involved, and requires management of unique IDs, but it is more robust and should not fail.

##### _Equations_
Mathematical equations can be added in-line within the markdown files by enclosing the equation in double dollar signs:

    $$equation$$

The equation itself is standard LaTeX syntax. If you are not familiar with LaTeX equation formatting, more information can be found [here](https://en.wikibooks.org/wiki/LaTeX/Mathematics).

##### _Images_
Images can be used on the site if so desired. They can be shown by using

    [image hover text](http://imageurl.svg)

Where the hover text is the text you want to appear when a user hovers over the image, and the url is the url that points to the domain hosting your image. While this is not a strict requirement, images should be in _*.svg_ format where possible. It is also possible to host your images, and other files, on the site. See below for more information.

##### _Lists_
To format a list in kramdown one must either use the characters _-_ and _*_ for an unordered list or numbers for an ordered list. The difference being, an ordered list will automatically increment as new elements are added (starting at 1, regardless of what number you started your list at) whereas unordered lists will act as simple bullet points. To initialize an unordered list you do:

    * List element 1
    * List element 2
    ...
    * List element n

Starting the line with an unordered character, with one space after it, defines the list. The same goes for an ordered list:

    1. List element 1
    2. List element 2
    ...
    n. List element n

With the difference being that for an ordered list, the number must be succeeded by a full stop.

##### _Tables_
Creating a table in kramdown involves first defining the headings. This can be done using the _|_ character between each heading:

    Heading 1 | Heading 2 | Heading 3

We follow this by defining the line as an actual heading line, this can be acomplished by 'underlining' it, using the following line under our heading line:

    - | - | -

Where the number of _-_ correspond to the number of headings. Finally we have our data lines situated under the 'underline':

    Data 1 | Data 2 | Data 3
    Data 4 | Data 5 | Data 6
    ...

Giving a final table that looks something like:

    Heading 1 | Heading 2
    - | -
    Data 1 | Data 2
    Data 3 | Data 4
    ...

### _Making Changes_

##### _Using Git_
The site is currently hosted on GitHub and **git**{: style="color: blue"} is used to manage the site and upload changes. The below text will provide instructions to allow contributions to the site.    

Actually making changes, for those of you unfamiliar with **git**{: style="color: blue"}, is fairly simple. It is best achieved with **git**{: style="color: blue"} installed on your machine, more information can be found  [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git). Before using git locally however, you must first navigate to the [repo page](https://github.com/lordcephei/lordcephei.github.io), sign in with your GitHub account, and fork the repo. This will create a server-side clone of the repo on your own account, allowing you full edit writes. At this point, if you prefer the command line, you can clone your repo:

    git clone https://github.com/youraccount/yourrepo.github.io.git site

With _youraccount_ and _yourrepo_ pointing to the correct names. This will clone the site's files in to the _site_{: style="color: green"} folder. You can now make your changes or additions to the site source files. When these are completed, we must add the file to the index for committing. The following commands assume you are working from the repository directory.

    git add *

You can also add files individually

    git add [filename]

With the files indexed, we can now set up the commit

    git commit -m "Commit message"

Where "Commit message" should be a useful message to let other people using the respository know what you are committing.    

Finally, we can commit the changes

    git push origin master

You may be asked for username and password details, fill these in all should be fine.   

With your local repo updated with changes you would like to push to the main repo, you can use GitHub's _pull request_ function. This will notify the site developers that you wish to merge your changes with the main site, on which they can review the changes and hopefully approve them. You can continue working on your local branch and create pull requests whenever you have changes you think are large enough to require another merge. With this method, however, you will not be able to see your changes on the site until a pull request is made. The next section details how you can locally view the changes to ensure the site compiles properly and you have not made mistakes.   

It would be useful to also email the site developers directly with your pull request to let them know who you are and what changes you are making. It also allows us to respond with feedback should the request be denied. Check out the [contact page](https://lordcephei.github.io/contact/) for more information.

##### _Hosting Locally_
The site can be built and hosted locally for you to check errors, formatting and the like. To do this, ensure **jekyll**{: style="color: blue"} is installed on your machine, more information [here](https://jekyllrb.com/docs/installation/).   

With **jekyll**{: style="color: blue"} installed, you can either build the site html for hosting elsewhere, or host with **jekyll**{: style="color: blue"}'s built in webserver. To build without hosting, simply

    jekyll build

From within the repository's directory. This will produce a _\_site_{: style="color: green"} folder which contains the copiled html files for the site. To host the site locally

    jekyll serve

This will create the same _\_site_{: style="color: green"} folder, but will also host the website locally. Read your terminal output for the address of the site.

_Note:_{: style="color: red"} Before committing your changes to the repo, please ensure you delete the _\_site_{: style="color: green"} folder so as to not clutter the repo unnecessarily.

### _Hosting Files_
Images, input files and whatever other files your tutorial or documentation needs can be hosted on the site, provided they are not too large. Files stored in the _assets/download/_{: style="color: green"} directory in the site source will be accessible on the site. Simply upload your file to the relevant folder, be that _img_{: style="color: green"}, _inputfiles_{: style="color: green"} etc. If a category does not exist, feel free to create one with a relevant name.

If your file is too large, contact us and we can look at other options. To actually commit the changes to the site, follow the making changes guide above; uploading your extra assets is as simple as commiting any other file.