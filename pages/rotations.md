---
layout: page-fullwidth
title: "Specifying Rotations" 
permalink: "/docs/misc/rotations/"
header: no
---

### _Purpose_

The Questaal suite use rotations in various contexts,
for example to rotate the lattice to a new coordinate system.
This page explains how to specify a rotation.

_____________________________________________________________

You specify a rotation by a
sequence of one or more substrings separated by commas,

<pre>
  rot1[,rot2][...]
</pre>

Each substring **rot1**, **rot2**, ...  is a rotation around a particular axis and has the syntax

<pre>
  (<i>x</i>,<i>y</i>,<i>z</i>)<i>angle</i>
</pre>

*angle* is the size of the rotation, in radians; *x*, *y*, and *z* are three real numbers specifying the rotation axis.
There should be no spaces in the string.

Each successive substring specifies a rotation about the new coordinate
system.  As a special case, the Euler angles are defined as a sequence
of three rotations, the first about <i>z</i> by angle <i>&alpha;</i>,
the second a rotation about the new
<i>y</i> axis by angle <i>&beta;</i>; the third about the new <i>z</i> axis by
angle <i>&gamma;</i>.  For the case <i>&alpha;</i>=<i>&pi;</i>/4, <i>&beta;</i>=<i>&pi;</i>/3, and <i>&gamma;</i>=<i>&pi;</i>/2, the
syntax would be

<pre>
  (0,0,1)pi/4,(0,1,0)pi/3,(0,0,1)pi/2
</pre>

You can use as a the following strings as shorthand:\\
&nbsp;&nbsp;  **x:** = shorthand for **(1,0,0)**\\
&nbsp;&nbsp;  **y:** = shorthand for **(0,1,0)**\\
&nbsp;&nbsp;  **z:** = shorthand for **(0,0,1)**

Thus the rotation above could equally be specified as:

<pre>
z:pi/4,y:pi/3,z:pi/2
</pre>

Below are two instances of rotations, especially useful for cubic systems:

<pre>
  z:pi/4,y:acos(1/sqrt(3))  &larr; Rotates <i>z</i> to the (1,1,1) direction
  z:-pi/4,y:pi/2            &larr; Rotates <i>z</i> to the (1,-1,0) direction
</pre>
