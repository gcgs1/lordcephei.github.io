---
layout: page-fullwidth
title: "Introductory tutorial to full-potential program lmf"
permalink: "/tutorial/lmf/lmf_tutorial/"
sidebar: "left"
header: no
---
_____________________________________________________________

### _Purpose_
{:.no_toc}

This tutorial covers the basics for running a self consistent LDA calculation for silicon. The goal is to introduce you to the different file types and the basics of running the code.

_____________________________________________________________

### _Command summary_

The tutorial starts under the heading "Tutorial"; you can see a synopsis of the commands by clicking on the box below.

<div onclick="elm = document.getElementById('foobar'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Commands - Click to show</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="foobar">{:/}

    $ mkdir si; cd si                               #create working directory and move into it
    $ nano init.si                                  #copy and paste init file from box
    $ blm init.si --express                         #use blm tool to create actrl and site files
    $ cp actrl.si ctrl.si                           #copy actrl to recognised ctrl prefix
    $ lmfa ctrl.si                                  #use lmfa to make basp file, atm file and to get gmax
    $ cp basp0.si basp.si                           #copy basp0 to recognised basp prefix
    $ nano ctrl.si                                  #set iterations number nit, k mesh nkabc and gmax
    $ lmf ctrl.si > out.lmfsc                       #make self-consistent

{::nomarkdown}</div>{:/}

_____________________________________________________________

### _Preliminaries_

Executables **blm**{: style="color: blue"}, **lmfa**{: style="color: blue"}, and **lmf**{: style="color: blue"} are required and are assumed to be in your path.  The source code for all Questaal executables can be found [here](https://bitbucket.org/lmto/lm).

_____________________________________________________________

### _Tutorial_

To get started, create a new working directory and move into it; here we will call it "si".

    $ mkdir si
    $ cd si

Now cut and paste the contents of the box below to _init.si_{: style="color: green"}.

    # Init file for Silicon
    LATTICE
       SPCGRP=227
       A=5.43 UNITS=A
    SITE
       ATOM=Si X=0 0 0

Si forms in the diamond cubic structure, space group 227 as you can readily determine from, e.g. [this web page](http://www.periodictable.com/Properties/A/SpaceGroupNumber.html).  Space group 227 is Fd-3m which has an underlying fcc Bravais lattice.  The init file equally allows you to supply the space group as **SPCGRP=Fd-3m**, or supply the lattice vectors directly, with a token **PLAT**, _viz_

      PLAT=  0.0    0.5    0.5
             0.5    0.0    0.5
             0.5    0.5    0.0

{::nomarkdown} <a name="lattice-and-basis-vectors"></a> {:/}
{::comment}
(/tutorial/lmf/lmf_tutorial/#lattice-and-basis-vectors)
{:/comment}

Lattice vectors are given in Cartesian coordinates in units of the lattice constant **A**.
You supply them in row format (i.e. the first row contains the x, y and z components of the first lattice vector and so forth).
**UNITS=A** tells **blm**{: style="color: blue"} that **A** is in &#x212B;.  If you do not specify the units, **A** is given in atomic units. (Questaal uses Atomic Rydberg units.)

Lattice vectors, lattice constant and the position of the basis vectors place in **SITE** is all that is needed to fix the structural information.  Atomic numbers for each atom is inferred from the symbol (Si). Token **X=** specifies that the coordinates are in "direct" representation, that is, as fractional multiples of lattice vectors.
It doesn't matter in this case, but you can use Cartesian coordinates : use **POS=** instead of **X** (see additional exercises below).  In such a case positions are given in Cartesian coordinates in units of **ALAT**.  So are the lattice vectors, if you supply them instead of the space group number.\\

The connection between positions expressed in Cartesian coordinates and as lattice vector multiples is:\\
&emsp; POS<sub><i>i</i></sub> = X<sub>1</sub>P<sub><i>i</i>,1</sub> &thinsp;+&thinsp; X<sub>2</sub> P<sub><i>i</i>,2</sub> &thinsp;+&thinsp; X<sub>3</sub> P<sub><i>i</i>,3</sub>, where P<sub><i>i</i>,<i>j</i></sub> is Cartesian component <i>i</i> of lattice vector <i>j</i>.

In order to run the DFT program **lmf**{: style="color: blue"}, you need an input file along with structural information. The **blm**{: style="color: blue"} tool read the init file as input and creates a template input file _actrl.si_{: style="color: green"} and structure file _site.si_{: style="color: green"}. The Questaal package recognises certain names as file types (such as "ctrl" for input file and "site" for structure file). **blm**{: style="color: blue"} writes
_actrl.si_{: style="color: green"} rather than _ctrl.si_{: style="color: green"} to prevent overwriting of an existing ctrl file. Extension "si" labels the materials system; it is something you choose.  Most files read by or generated by Questaal programs will append this extension.

Run **blm**{: style="color: blue"} as shown below and then copy the template file _actrl.si_{: style="color: green"} to _ctrl.si_{: style="color: green"}, which is the name of the main input file recognised by most codes in the Questaal package. The `--express` switch tells **blm**{: style="color: blue"} to make a particularly simple input file; we will see more complicated examples in later tutorials.  `--nit=1` causes **blm**{: style="color: blue"} to limit the self-consistency cycle to 1 iteration.  We will return to this shortly.

    $ blm init.si --express --nit=1
    $ cp actrl.si ctrl.si

The start of the **blm**{: style="color: blue"} output shows some structural and symmetry information. Further down, the "makrm0:" part gives information about creating the augmentation spheres, both silicon atoms were assigned spheres of radii 2.22 Bohr. Now open up the site file and you can see it contains the lattice constant and lattice vectors in the first line. Note that the lattice constant has been converted from Angstroms to Bohr since the code works in atomic units. The other terms in the first line are just standard settings and a full explanation can be found in the online page for the site file. The second line is a comment line and the subsequent lines contain the atomic species labels and coordinates. Note that **blm**{: style="color: blue"} writes Cartesian coordinates by default (they happen to be the same as fractional coordinates in this case) and that running **blm**{: style="color: blue"} produces a new actrl and site file each time.

Next take a look at the input file _ctrl.si_{: style="color: green"}.

    $ nano ctrl.si

The first few lines are just header information, then you have a number of basic parameters for a calculation. We won't talk about these values now but a full description is provided on the ctrl file page. Defaults are provided by **blm**{: style="color: blue"} for most of the variables except **gmax** and **nkabc**, which are left as "NULL". The "nkabc" specifies the k mesh. There is no sensible way for blm to select a default value, as it depends on the bandgap or details of the Fermi surface, and also the precision needed for the physical property you need. The "gmax" value specifies how fine a real space mesh is used for the interstitial charge density and this depends on the basis set as described below.

We now need a basis set, which will give us an estimate for **gmax**. This can done automatically by using the **lmfa**{: style="color: blue"} tool. **lmfa**{: style="color: blue"} also calculates free atom wavefunctions and densities. The latter will be used later to make a trial density for the crystal calculation; the free atom wavefunctions are used to fit the basis set parameters. Note that in the all electron approach, space is partitioned in two kinds of regions: an augmentation sphere part (around each atom) and an interstitial part (region between spheres). In the augmentation spheres, the basis set is composed of local atomic functions, tabulated numerically. In the interstitial regions, the basis consists of analytic, smooth-Hankel functions, characterised by their smoothing radius (**RSMH**) and Hankel energies (**EH**). The number of interstitial functions and their parameters (**RSMH** and **EH**) are defined in the basp file (the size of which is determined by parameters in the ctrl file). There are a few more subtleties to the code's basis set but we will leave further details to the theory pages and the input file pages. Run the following command:

    $ lmfa ctrl.si

Again the output shows some structural information and then details about finding the free atom density, basis set fitting and, at the end an estimate of **gmax** is printed out. Note that the Barth-Hedin exchange-correlation functional is used, as indicated by "XC:BH", this was specified by "**xcfun**=2" in the ctrl file (the default). We won't go into more detail now, but a full description can be found on the **lmfa**{: style="color: blue"} page. One thing to note is a recommended value for **gmax**  given towards the end: "GMAX=5.0". Now that we have a gmax value, open up the ctrl file and change the default NULL value to 5.0.

    $ nano ctrl.si

Alternatively, you can supply the information through **blm**{: style="color: blue"}. We will do this here to avoid the need to edit the file.  Run blm with an extra argument and compare to the original ctrl file:

    $ blm init.si --express --nit=1 --gmax=5
    $ diff actrl.si ctrl.si

The last switch enables blm to use a value for **gmax**.

Check the contents of your working directory and you will find two new files _atm.si_{: style="color: green"}  and _basp0.si_{: style="color: green"} . _atm.si_{: style="color: green"}  contains the free atom densities calculated by **lmfa**{: style="color: blue"}. File _basp0.si_{: style="color: green"}  is the template basis set file; the standard basis set name is basp and the extra 0 is appended to avoid overwriting. Take a look at _basp0.si_{: style="color: green"}  and you will see that it contains basis set parameters that define silicon's smooth Hankel functions. Changing these values would change their functional form, but **lmfa**{: style="color: blue"} does a reasonable job (also later on parameters can be automatically optimized, if desired) so we will leave them as they are. Copy _basp0.si_{: style="color: green"} to _basp.si_{: style="color: green"}, which is the name **lmf**{: style="color: blue"} recognises for the basis set file

    $ cp basp0.si basp.si

The second unknown parameter is the k-mesh.  A 4x4x4 k mesh is sufficient for Si. Set this value in your text editor by simply changing "nkabc=NULL" to "nkabc=4" (4 is automatically used for each mesh dimension, you could equivlaently use "nkabc=4 4 4").  Take a look at the last line, it contains information about the different atoms in the system (here we only have silicon) and their associated augmentation spheres.

    $ nano ctrl.si

You can also supply the information through **blm**{: style="color: blue"} with a switch.  Compare the effect of this new switch:

    $ blm init.si --express --nit=1 --gmax=5 --nk=4
    $ diff actrl.si ctrl.si

Whichever way you supply **gmax** and **nk** to the ctrl file, it should now have no NULL entries and we now have everything we need to run an all-electron, full-potential DFT calculation. This is done using the **lmf**{: style="color: blue"} program. Double check that you have specified the k mesh (**nkabc**) and a **gmax** value and then run the following command:

    $ lmf ctrl.si

The first part of the output is similar to what we've seen from the other programs. Look for the line beginning with "lmfp", it should be around 60 lines down. This line tells us about what input density is used. **lmf**{: style="color: blue"} first looks for a restart file _rst.si_{: style="color: green"} and if it's not found it then looks for the free atom density file _atm.si_{: style="color: green"}. **lmf**{: Style="Color: Blue"} then overlaps the free atom densities to form a trial density (Mattheis construction) and this is used as the input density. Next **lmf**{: style="color: blue"} begins the first iteration of a self-consistent cycle: calculate the potential from the input density, use this potential to solve the Kohn-Sham equations and then perform Brillouin zone integration to get the output density. Towards the end of the output, the Kohn-Sham total energy is reported along with the Harris-Foulkes total energy. These two energies will be the same at self-consistency (or very close at near self-consistency). Note that the calculation stopped here after a single iteration.  This is because the number of iterations **nit** was set to 1 by blm, because --nit=1 was given.  Now increase the number of iterations to something like 20 and run **lmf**{: style="color: blue"} again. A lot of text is produced so it will be easier to redirect the output to a file, here we call it _out.lmfsc_{: style="color: green"} (appending sc to indicate a self-consistent cycle).

    $ nano ctrl.si
    $ lmf ctrl.si > out.lmfsc

Now take a look at the output file _out.lmfsc_{: style="color: green"}. Look for the line beginning with "iors", again around line 60

~~~
 iors  : read restart file (binary, mesh density)
         use from  restart file: ef window, positions, pnu
         ignore in restart file: *
~~~



This time _rst.si_{: style="color: green"} was found (it was created after the initial iteration).  Now move to the end of the file.  The '**c**' in front of the Harris Foulkes **ehf** and Kohn-Sham **ehk** energies indicates that convergence was reached (note how similar the ehf and ehk energies are). A few lines up you can see that it took 8 iterations to converge: look for "**it 8 of 20**". At the end of each iteration the ehf and ehk total energies are printed and a check is made for self-consistency. The two parameters **conv** and **convc** in the ctrl file specify, respectively, the self-consistency tolerances for the total energy and root mean square (RMS) change in the density. Note that by default both tolerances have to be met. To use a single tolerance you simply set the one that you don't want to zero.

Further up again the Fermi energy and band gap values, and other key bits of information are reported in the Brillouin zone integration section.  You should find something similar to the output snippet below.

~~~
 BZWTS : --- Tetrahedron Integration ---
 ... only filled or empty bands encountered:  ev=0.185509  ec=0.229539
 VBmax = 0.185509  CBmin = 0.229539  gap = 0.044029 Ry = 0.59880 eV
 BZINTS: Fermi energy:      0.185518;   8.000000 electrons;  D(Ef):    0.000
         Sum occ. bands:   -1.4864297  incl. Bloechl correction:    0.000000
~~~

To see how the density and energy changes between iterations, try grepping for **DQ** and **ehk=-**

    $ grep 'DQ' out.lmfsc
    $ grep 'ehk=-' out.lmfsc

You can also check how the bandgap changes as iterations proceed to self-consistency by grepping _out.lmfsc_{: style="color: green"} for 'gap'.

And that's it! You now have a self-consistent density and have calculated some basic properties such as the band gap and total energy.
Other tutorials to look at are those to generate energy band structures, and density-of-states, or calculate a mechanical property such as the optical mode frequency.

_____________________________________________________________

### _Other Resources_

This [tutorial on PbTe](/tutorial/lmf/lmf_pbte_tutorial/) also covers the basic self-consistency cycle, in a bit more detail. It has has companion [tutorial for the ASA](/tutorial/asa/lm_pbte_tutorial/), allowing you to compare the FP and ASA methods.
There is [a more detailed tutorial](https://lordcephei.github.io/buildingfpinput/)
with some description of important tags the **lmf**{: style="color: blue"} reads, and of the **lmf**{: style="color: blue"} basis set.

For a tutorial showing a self-consistent calculation of ferromagnetic metal, see [this tutorial](/tutorial/gw/qsgw_fe/). [This tutorial](xxx) shows how to calculate optical properties for PbTe.  See [this tutorial](xx) for the calculation of the optical mode frequency in Si.  [This document](/docs/code/fpoverview/) gives an overview of the lmf implementation; the formalism behind the method is described in this [book chapter](xx).

_____________________________________________________________

### _FAQ_
{::comment}
/tutorial/lmf/lmf_tutorial/#faq
{:/comment}

Below is a list of frequently asked questions. Please get in contact if you have other questions.

1) How does **blm**{: style="color: blue"} determine the augmentation sphere radii?

Overlaps free atom densities and looks for where potential is flat.

2) What is the log file?

The log file _log.si_{: style="color: green"} keeps a compact record of key outputs in the current directory.  In successive runs, data is appended to the log file.

3) What is the **Harris-Foulkes** energy?

It is a functional of the input density, rather than the output density.  At self-consistency it should be the same as the standard Kohn-Sham functional.  The Harris-Foulkes functional tends to be more stable, and like the Kohn-Sham functional, it is stationary at the self-consistent density. But it is not necessarily a minimum there. See [this paper](http://dx.doi.org/10.1103/PhysRevB.39.12520) by M. Foulkes and R. Haydock.


_____________________________________________________________

### _Additional Exercises_

1) Converting between fractional and Cartesian coordinates

For example, try running the command "blm init.si --express --wsitex" and you will see that **xpos** has been added to the first line of _site.si_{: style="color: green"}; this indicates that the coordinates are now in fractional form. Note that in this case the Cartesian and fractional coordinates happen to be the same.

2) The bandgap printed out by the code is not the actual LDA gap, but the smallest separation between the highest occupied and lowest unoccupied state it found on a discrete 4x4x4 k mesh.  The actual minimum occurs near k=(1,0,0), commonly referred to as the X point. The X point is on the 4x4x4 k-mesh, but the conduction band minimum itself is not quite at X.  Rerun the calculation with a very fine k mesh (not self-consistently this time) and observe that the bandgap is slightly smaller.  Use your text editor to set nkabc to 12 or 16 and do:

    $ lmf ctrl.si --quit=rho

You should find that the gap gets smaller by about 0.13 eV, which is close to the experimentally observed splitting between the conduction minimum and the conduction band at X.
See [this tutorial](/tutorial/lmf/lmf_bandedge/) to automatically locate the position of the conduction band.
