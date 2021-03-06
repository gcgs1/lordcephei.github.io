---
layout: page-fullwidth
title: "Full Potential (lmf) Mulliken Analysis"
permalink: "/tutorial/lmf/mulliken/"
header: no
---

____________________________________________________________

### _Table of Contents_
{:.no_toc}
*  Auto generated table of contents
{:toc} 

### _Purpose_
_____________________________________________________________

This tutorial demonstrates how to perform mulliken analysis using the full potential band code **lmf**{: style="color: blue"}.

### _Preliminaries_
_____________________________________________________________
This tutorial assumes you have cloned and built the **lm**{: style="color: blue"} repository (located [here](https://bitbucket.org/lmto/lm)). For the purpose of demonstration, _~/lm_{: style="color: green"} will refer to the location of the cloned repository (_source_ directory). In practice, this directory can be named differently.

All instances of commands assume the starting position is your _build_ directory (this can be checked with the _pwd_{: style="color: blue"} command).  In this tutorial it will be called _~/build/_{: style="color: green"}.

    $ cd ~/build/

with _~/build/_{: style="color: green} being the directory the **lm**{: style="color: blue"} repository was built in to.

_Note:_{: style="color: red"} the build directory should be different from the source directory.

### _Tutorial_
_____________________________________________________________
In order to perform a Mulliken analysis using the **lmf**{: style="color: blue"} code an input file is needed for the material of which the partial DOS should be found. A tutorial detailing the steps required to generate a basic input file can be found [here](https://lordcephei.github.io/tutorial/lmf/ctrlfile/). While this tutorial concerns itself with Cr3Si6, the steps involved are applicable to most other materials.

For the Mulliken analysis tutorial we will use the material Iron, Fe, and thus base our tutorial around the _fe 2_ test case. Our input file, created previously, will be referred to as _ctrl.fe_{: style="color: green"} and should be named as such.

We begin by running **lmfa**{: style="color: blue"} program which needs to be run before any **lmf**{: style="color: blue"} process in order to generate the free-atom densities which, in our case, is generated in to the file _atm.fe_{: style="color: green"} with the command

    $ lmfa fe

We can now proceed with our **lmf**{: style="color: blue"} commands

    $ lmf fe
    $ lmf --rs=0 --cls:1,1,2 --mull:mode=2 -vnk=6 -vnit=1 fe

Note that the **lmf**{: style="color: blue"} command is run twice - this is intended; the first run ensures a self-consistent solution. This will generate a variety of files needed to build our weights file for Mulliken analysis. Note, this command will generate a _dos.fe_{: style="color: green"} file. This file is _not_ our partial density of states but rather the full density of states file, the next command will overwrite this file.

We then run

    $ lmdos --nosym --mull:mode=2 --dos:npts=1001:window=-.7,.8 -vnk=6 fe

Which makes use of the previously generated files and generates the _dos.fe_{: style="color: green"} file which contains our Mulliken DOS. This can be plotted with your preferred plotting tool, although some manipulation of the data in the file may be required.   

A plotting tool is included in the suite, **fplot**{: style="color: blue"}, which can be used to generate a postscript file. We must first prepare the _dos.fe_{: style="color: green"} file with another program in the suite, _pldos_{: style="color: blue"}, with the command

    $ echo 20 10 -0.7 .8 | pldos -fplot -lst="1:7:2,19:31:2;9,11,15;13,17" -lst2 dos.fe

Which generates a _plot.dos_{: style="color: green"} file readable by **fplot**{: style="color: blue"}. We follow this with an **fplot**{: style="color: blue"} command

    $ fplot -disp -pr10 -f plot.dos

Which should result in a viewable image of the Mulliken DOS.

### _Other Resources_

Performing a Mulliken analysis using the **lmf**{: style="color: blue"} code is exemplified in three test cases

    $ ~/lm/fp/test/test.fp fe 2
    $ ~/lm/fp/test/test.fp cr3si6 2
    $ ~/lm/fp/test/test.fp gdn 2

Should you want a more in depth look, or a practical example, these are good places to start. You will find that the primary difference between this process and that of a standard partial DOS (see the tutorial linked in the 'Preliminaries' section) is the use of the `--mull` switch. 

_Note_{: style="color: red"}: These test cases are not pedagogical, they are useful to verify the code works and to extract data.


