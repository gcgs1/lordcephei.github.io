---
layout: page-fullwidth
title: "Syntax of integer lists" 
permalink: "/docs/misc/integerlists/"
header: no
---

### _Purpose_

Integer lists are used in a wide variety of contexts, e.g. in the preprocessor
[**repeat**](/docs/input/preprocessor/#looping-constructs) directive.
This web page documents the syntax of integer lists.

_Note:_{: style="color: red"} in some contexts elements of the list are real
numbers, not integers.  The syntax is the same for real numbers.

_____________________________________________________________

Integer lists consist of a sequence of strings separated by commas:

<pre>
       <i>strn1</i>,<i>strn2</i>,...
</pre>

Each of the &nbsp; <i>strn1</i>, <i>strn2</i>, ... &nbsp; can assume one of the following three forms:


1.  a single integer or algebraic expression

2.  two integer expressions separated by a colon, _viz_

        low:high

    gets expanded into

        low, low+1, low+2, ... high

3.  Three integer expressions separated by colons, _viz_

        low:high:step

    gets expanded into:

        low, low+step, low+2*step, ... high


_Examples_
<pre>
  5+1         becomes a single number, 6.
  5+1:8+2     becomes a sequence of numbers, 6 7 8 9 10
  5+1:8+2:2   becomes a sequence of numbers, 6 8 10.
  1:4,7:11    becomes the sequence 1 2 3 4  7 8 9 10 11.
</pre>


_Note:_{: style="color: red"}&nbsp; **slatsm/mkilst.f**{: style="color: green"}
contains the source code for generating integer lists.
