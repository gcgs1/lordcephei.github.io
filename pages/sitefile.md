---
layout: page-fullwidth
title: "Site file"
permalink: "/docs/input/sitefile/"
header: no
---
_____________________________________________________________

### _Purpose_
{:.no_toc}

To describe how to use a site file to read lattice information or site information.

_____________________________________________________________

### _Introduction_

Structural data (lattice and site data) can be read from the **ctrl** file,
**ctrl._ext_**{: style="color: green"}.

You can alternatively read either lattice information, or site information,
or both from a site file.  That file has no predetermined name; it is 
given in the **ctrl** file.

_Note:_{: style="color: red"} The site file is normally read
through the [file preprocessor](/docs/input/preprocessor/).


To read lattice information from **sname._ext_**{: style="color: green"},
use the **FILE** token in **STRUC**.\\
To read site information from **sname._ext_**{: style="color: green"},
use the **FILE** token in **SITE**.  The following snippet does both:

~~~
STRUC  FILE=sname
       ...
SITE   FILE=sname
       ...
~~~

_Note:_{: style="color: red"}
If you use the **file** token in the **EXPRESS** category, it performs the function
of both **STRUC_FILE** and **SITE_FILE**, and supersedes both of these tags.  

In the **EXPRESS** mode the species labels are determined from the SITE file;
and the number of atoms and species are determined from the site file
so that **STRUC_NBAS** and **STRUC_NSPEC** are not used.  For every species label
in the site file there must be a corresponding label in the **SPEC** category.

The first nonblank, non-preprocessor directive, should begin with:

~~~
% site-data vn=#
~~~

_Note:_{: style="color: red"} Version 7 of the Questaal suite writes version 3.0 site files; it can read
versions 3.0 and prior versions.

This first line must also contain token **io=#**. &nbsp; **io=14** tells the reader
that minimal information is available in the file: 

+ the number of sites in the lattice
+ the lattice vectors
+ the basis vectors and their species ID.

Usually the first line contains the lattice constant as well. Consider the following snippet:

~~~
% site-data vn=3.0 xpos fast io=62 nbas=64 alat=7.39563231 plat= 2.0 0.0 0.0 0.0 2.0 0.0 1.0 1.0 3.58664656
#                        pos                                vel                     eula              vshft   PL rlx
 K1        0.0000000   0.0000000   0.0000000    0.0000000    0.0000000    0.0000000    0.0000000    0.0000000    0.0000000 0.000000  0 111
 Fe1       0.3750000   0.1250000   0.2500000    0.0000000    0.0000000    0.0000000    0.0000000    0.0000000    0.0000000 0.000000  0 111
~~~

The first line tells the parser the following:

+ **io=62** indicates that the following information is available for each site, in the order listed:
  + species index (labels must match those in category **SPEC** in the ctrl file).
  + site positions (as multiples of **plat** since **xpos** is in the first line).
  + velocities -- used in molecular dynamics.
  + Euler angles.  Rotates the spin quantization axis used by noncollinear parts of the ASA code.
  + site potential shifts, which may be used by the ASA.
  + principal layer index used by the layer Green's function code.
  + three binary integers **_nnn_** specifying which of the three Cartesian components are frozen
    in molecular dynamics or statics.\\
    **1** allows to relax while **0** freezes that coordinate.
+ **xpos** indicates that the basis vectors are written as fractional multiples of lattice vectors.
  By default these vectors are written in Cartesian coordinates in units of the lattice constant **alat**.
+ **fast** tells the parser to read basis information with FORTRAN read.  By default
  it will parse each element as an algebraic expression.  FORTRAN read is much faster, but you lose the capability of using expressions.
+ **nbas=64** tells the parser that there are 64 atoms in the crystal.
+ **alat=7.39563231** specifies the lattice constant, in atomic units.
+ **plat=...** specifies the lattice vectors, P<sub>1</sub>, followed by P<sub>2</sub> and P<sub>3</sub>.

The second line in the snippet is a comment line.  Then follow a sequence of 64 lines, one line for each atom.
As a minimum, the row must contain a species label and the site position (**io=14**).
In snippet above (**io=62**) extra information is given, as noted above.

### _Other resources_

Many of the tutorials, e.g. the [basic lmf tutorial](/tutorial/lmf/lmf_tutorial/), make use of site files.
The input file maker, **blm**{: style="color: blue"}, typically generates ctrl and site files as a pair.