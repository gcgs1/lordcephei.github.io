---
layout: page-fullwidth
title: "The ccomp processor"
permalink: "/docs/misc/ccomp/"
header: no
---
_____________________________________________________________


### _Introduction_
{:.no_toc}

Questaal's codes are compiled with a source code preprocessor, 
**ccomp**{: style="color: blue"}.  This tool, written in C,
performs a similar function to the conditional compilation
preprocessing step found in modern programming languages, e.g.

~~~
# if expr
# else
# endif
~~~

It was written long before conditional compilation became part of
FORTRAN.  Questaal still uses it in part to retain backwards
compatibility, but also because it retains some advantages 
(as well as some disadvantages).

_____________________________________________________________

### _How ccomp works_

**ccomp**{: style="color: blue"} mimics the C language conditional
compilation constructs such as the (**#ifdef**, **#else**, **#endif**) group,
in a fortran-77 compatible way.

FORTRAN statements beginning with **C#** are preprocessor directives.
The ones implemented are:

~~~
C#define   name
C#include  file-name
C#ifdef    expr
C#ifndef   expr
C#else
C#elseif   expr
C#endif 
~~~

#### _Boolean expressions_

**C#define** defines a name, to be used later in boolean expressions.

Names can also be defined through command line switches:
when they appear in a boolean expression they evaluate to **true** if they exist, 
else **false**.
Thus the expression `john & bill` evaluates to **true** if **john** and **bill**
had both been previously defined; otherwise **false**.
Boolean expressions can make use of the **AND**
(&) and **OR** (|) operators.
Precedence of operators is strictly left to right,
so that `john | bill & mike` is equivalent to `(john | bill) & mike`,
whereas `john  & bill | mike` is equivalent to `(john & bill) | mike`.

**Note**{: style="color: red"}: 
**ccomp**{: style="color: blue"} distinguishes case in the names (as does C).

#### _Running ccomp_

You can also define (and undefine) names on the command line.
Names so defined (undefined) take precedence over subsequent occurences of
**C#define** in the source file.   Thus if a name is defined
in the source code but undefined on the command line, it is undefined.

**ccomp**{: style="color: blue"} writes to standard output, unless a destination filename is
supplied.  For example:

~~~
ccomp -dMPI -uMPIK source.f dest.f
~~~
reads file _source.f_{: style="color: green"} and writes the modified file
to _dest.f_{: style="color: green"}.  **MPI** is defined, 
**MPIK** is undefined.

To see arguments **ccomp**{: style="color: blue"}, accepts, type `ccomp --h`.

#### _Conditional commenting or uncommenting of lines_

**C#ifdef**, **C#ifndef**, and **C#elseif** are followed by a boolean
expression, e.g.

~~~
C#ifdef CRAY
C#ifndef john | bill & mike
~~~

At any point in parsing the source file, **ccomp**{: style="color: blue"} is in a **true** or **false** state,
depending on the result of the last boolean expression evaluated.
In addition to knowing the 'current' state, it knows what the 'prior' state of the original code is at that point, i.e. what the
state would be if given the names as defined in the source file.

Any time a new directive such as **C#ifdef** is encountered, the current and prior states
are recalculated.
For lines between conditional compilation directives (e.g. a **C#ifdef/C#else/C#endif** block), **ccomp**{: style="color: blue"} does one of the following:

+ If the current and prior states are the same it outputs the line unmodified
+ If the current state is **true** and the prior is **false**, it comments out the line
+ If the current state is **false** and the prior is **true**, it removes a **C** from the first column.
+ If the **C** is missing, **ccomp**{: style="color: blue"} exits with an error message.

Comments use the fortran-77 convention (**C** in the first column).


### _Applications of ccomp_

Programs **lmf**{: style="color: blue"}, **lm**{: style="color: blue"}, **lmdos**{: style="color: blue"}, **lmchk**{: style="color: blue"}, etc.
derive the same source code, **lmv7.f**{: style="color: green"}.  Any one of them can be got from the other by suitable definitions of names.
**ccomp**{: style="color: blue"} is used extensively to create new branches code, to and
customize code specific to certain compilers, either for optimization purposes or to avoid compiler bugs.

### _Other resources_

The source code to **ccomp**{: style="color: blue"} can be found [here](https://bitbucket.org/lmto/lm/src/e82e155d8ce7eb808a9a6dca6d8eea5f5a095bd6/startup/ccomp.c).
