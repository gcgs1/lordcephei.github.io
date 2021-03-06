---
layout: page-fullwidth
title: "The File Preprocessor"
permalink: "/docs/input/preprocessor/"
header: no
---

### _Purpose_
_____________________________________________________________
{:.no_toc}

This manual documents the functioning of Questaal's preprocessor.
Many files in the Questaal suite (e.g. the main input file
_ctrl.ext_{: style="color: green"}) are read through the prepocessor
before they are used.

### _Table of Contents_
_____________________________________________________________
{:.no_toc}
*  Auto generated table of contents
{:toc}  
{::comment}
(/docs/input/preprocessor/#table-of-contents)
{:/comment}


### _Main Features_
_____________________________________________________________
{::comment}
(/docs/input/preprocessor/#main-features)
{:/comment}

The preprocessor allows you to declare variables and evaluate expressions. It also possesses some
programming language capability, with branching control to skip or loop over a selected block of
lines.

The preprocesor is built into 
the source code, <b>rdfiln.f</b>{: style="color: green"}.
Comments at the beginning of <b>rdfiln.f</b>{: style="color: green"}
document directives it can process.  Here we use _rdfiln_{: style="color: green"}
as a name for the preprocessor.

#### Curly brackets contain expressions
{::comment}
/docs/input/preprocessor/#curly-brackets-contain-expressions
{:/comment}

_rdfiln_{: style="color: green"} treats curly brackets **{...}** specially and substitutes 
its contents for **something-else**.
Typically **{...}** will contain an algebraic expression, which is evaluated as a binary number and rendered back as an ASCII 
representation of the number.

Thus the line

~~~
    talk {4/2} me
~~~
becomes

~~~
    talk 2 me
~~~
**{...}** can contain other kinds of syntax as well; see [below](/docs/input/preprocessor/#expression-substitution).

_Note:_{: style="color: red"}
The substitution <FONT size="+1"><tt>{...}</tt></FONT>&rarr;<FONT size="+1"><tt><i>string</i></tt></FONT>
may increase the length of the line.  If the modified line exceeds the maximum size it is truncated.
This maximum length is controlled by parameter **recln0** in the main program.
Source codes are distributed with **recln0=120**.

#### Variables
{::comment}
/docs/input/preprocessor/#variables
{:/comment}

The preprocessor permits three kinds of variables: floating point scalar,
floating-point vector, and strings.  They can be declared with 
[preprocessor directives](/docs/input/preprocessor/#variable-declarations-and-assignments).
Scalar and character variables can also be declared on the command-line using, e.g.\\
`-vsnam=expr`&nbsp; or &nbsp;`-vcnam=string`.

  * Separate symbol tables are maintained for each of the three kinds of variables.
  * Scalar variables and vector elements can be used in algebraic expressions.
  * Character variables can be used in string expressions (see below).

_Note:_{: style="color: red"} As _rdfiln_{: style="color: green"} parses a file, it may create new variables, thus enlarging the symbols
table.  Variables allocated this way are temporary, however.  When _rdfiln_{: style="color: green"} has finished with whatever it is
reading, it destroys variables created by preprocessor directives. You can preserve variables for future use with the **% save** directive.

#### Branching and looping constructs
{::comment}
/docs/input/preprocessor/#branching-and-looping-constructs
{:/comment}

You can [conditionally read](/docs/input/preprocessor/#branching-constructs)
certain lines of a file, or [loop over lines](/docs/input/preprocessor/#looping-constructs) multiple times.

### _Expression Substitution_
{::comment}
/docs/input/preprocessor/#expression-substitution
{:/comment}

Enclosing a string in curly brackets, _viz_ **{strn}**, instructs the preprocessor to
parse the contents of &nbsp;**{strn}**&nbsp; and substitute it with something else.

_Note:_{: style="color: red"} To suppress expression substitution, prepend **{strn}** with a backslash, _viz_ **\\{strn}**.
The preprocessor will remove the backslash but leave **{strn}** unaltered.

**strn** must take one of the following syntactical forms.
If _rdfiln_{: style="color: green"} cannot match the first form, it tries the second, and so on.
(It is an error if no form can be matched.)
These four forms are as follows, arranged by the precedence they take in parsing:

+ (_string substitution_): &nbsp; **strn** is name of a **character variable**.  The value of the variable is substituted.\\
  The variable _may_ be followed by a qualification (see 1a and 1b below).   
+ (_conditional substitution_): &nbsp; **strn** begins with a "**?**".  An expression is evaluated, which determines what string is substituted.  See 2 below.
+ (_vector substitution_): &nbsp; **strn** is the name of a vector.  The result is the contents of the vector.  See 3 below.
+ (_expression substitution_): &nbsp; **strn** an algebraic expression.  Expressions use C-like syntax.  See 4 below.

In more detail, the four rules are as follows:

{::nomarkdown} <a name="string-substitution"></a> {:/}
{::comment}
/docs/input/preprocessor/#string-substitution
{:/comment}

1. (_string substitution_) **strn** consists of (or begins with) a character variable, say **mychar**.

   a. **strn** is a character variable.
       _rdfiln_{: style="color: green"} replaces **{mychar}** with contents of **mychar**.\\
       _Example_: If mychar='foo bar', &nbsp;&nbsp;  **{mychar} &rarr; foo bar**.

   b. **strn** is a character variable followed by a qualifier (...),
       which must be one of the following:

      * **(integer1,integer2)** (substring of &nbsp;**strn**).
       **{mychar(n1,n2)}** is replaced by the (**n1:n2**) substring of **{mychar}**.\\
       _Example_: If mychar="foo bar", **{mychar(2,3)}** &rarr; **oo**.

      * **(charlst,n)** (index to charlst). **{mychar(charlst,n)}** returns in index to **{mychar}**.
      **{mychar}** is parsed for characters in **charlst**, returning the index to the **n**1<sup>th</sup> occurrence;
      **charlst** is a sequence of characters.\\
      **n** is optional: if omitted, the preprocessor uses **n=1**.\\
       _Example_:  let mychar="foo bar" and charlst='abc'.
       Note that "foo bar" contains characters 'b' and 'a'.\\
       **{mychar('abc',2)} &rarr; 6**, because the 6<sup>th</sup> character contains the second occurence of [abc].

      * **(:e)** returns an index marking last nonblank character in **{mychar}**.\\
         _Example_: If mychar='foo bar',&nbsp;&nbsp; **{mychar(:e)} &rarr; 7**.

      * **(/'strn1'/'strn2'/,n1,n2)** substitutes **strn2** for **strn1**.\\
       Substitutions are made for the **n**1<sup>th</sup> to **n**2<sup>th</sup> occurrence of **strn1**.\\
       _Example_ If **mychar="foo bar"**, then &nbsp; **{mychar(/'foo'/'boo'/)}** &rarr; "boo bar"
       **n**1 and **n**2 are optional, as are the quotation marks.

2. (_conditional substitution_) **strn** takes the form **{?~**_expr_**~strn1~strn2}** &nbsp; (Note: the '~' can be any character).\\
       _expr_ is an algebraic expression; **strn1** and **strn2** are strings.
       _rdfiln_{: style="color: green"} returns either **strn1** or **strn2**, depending on the result of _expr_.\\
       If _expr_ evaluates to nonzero, **{...}** is replaced by **strn1**.; else **{...}** is replaced by **strn2**.\\
       _Example:_ &nbsp; **{?~(n<2)~n is less than 2~n is at least 2}** :\\
           **{...}** &nbsp; becomes &nbsp;&nbsp;"**n is less than 2**"&nbsp;&nbsp; if _n_<2;  otherwise it becomes
         **&nbsp;&nbsp;"n is at least 2"&nbsp;&nbsp;**

3. (_vector substitution_) **strn** is name of a vector variable, say **myvec**.
       _rdfiln_{: style="color: green"} replaces  **{myvec}** with a sequence of numbers separated
       by one space, which are the contents of **myvec**.\\
       _Example_ : suppose **myvec**. has been declared as a 5-element quantity in the following way:\\
         &nbsp;&nbsp; % vec myvec[5] 6-1 6-2 5-2 5-3 4-3\\
       **{myvec}**&nbsp; will be turned into **5 4 3 2 1** \\
       A single element of a vector acts like a scalar.  Thus &nbsp;**{3*myvec(2)-2}**&nbsp; becomes **10**.

4. (_expression substitution_) **strn** is an algebraic expression composed of numbers combined with unary and binary operators. The syntax is very similar to the C programming language.
   _rdfiln_{: style="color: green"} parses &nbsp;**strn**&nbsp; to obtain a binary number, renders the result in ASCII form, and
       substitutes the result.

   _Note:_{: style="color: red"} **strn**&nbsp; may consist of a sequence of expressions, separated by commas.
   _rdfiln_{: style="color: green"} returns the value of the last expression.
   A variable should be assigned to each intermediate expression.
   Assignment may be simple (**=**) or involve an arithmetic operation.\\
       _Examples:_
   <pre>
     {x=3}               &larr;  assigns x to 3 and returns '3'
     {x=3,y=4}           &larr;  assigns x to 3 and y to 4, and returns '4'
     {x=3,y=4,x*=y}      &larr;  assigns x to 3*4 and y to 4, and returns '4'
     {x=3,y=4,x*=y,x*2}  &larr;  assigns x to 3*4 and y to 4, and returns '24'
   </pre>

#### Further properties of curly brackets

Brackets may be nested.
_rdfiln_{: style="color: green"} will work recursively
through deeper levels of bracketing, substituting <FONT size="+1"><tt>{..}</tt></FONT> at each level with
a result before returning to the higher level.  <br>
<i>Example</i>: Suppose &nbsp;**{foo}**&nbsp; evaluates to **2**. Then:
<pre>  {my{foo}bar}</pre>
will be transformed into
<pre>  {my2bar}</pre>
and finally the result of &nbsp;**{my2bar}**&nbsp; evaluated.

If _rdfiln_{: style="color: green"} cannot evaluate <FONT size="+1"><tt>{my2bar}</tt></FONT> it will abort with a message similar to this one:
<pre>
 rdfile: bad expression in line
 {  ...  my2bar}
</pre>

_Note:_{: style="color: red"} there is a syntactical difference between **{expr}** and the value of _expr_ itself, because
**{expr}** returns an ASCII representation of _expr_, and precision is lost. Thus &nbsp;**{pi-3}**&nbsp; is replaced by **.141592654**

#### Syntax of Algebraic Expressions

The general syntax for an expression is a sequence of one or more expressions of the form
<pre>   {<b>name=</b>expr<b>[,name=</b>expr<b>...]</b>}</pre>
Commas separate declarations.  Arithmetic operators can be used in place of assignment (**=**), for example
&nbsp;**{x=3,y=4,x*=y,x*2}**.
The final expression may (and typically does) consist of an expression only omitting &nbsp;**name=**.

_Note:_{: style="color: red"} _expr_ may not contain any whitespace.

{::nomarkdown} <a name="expr-syntax"></a> {:/}
{::comment}
/docs/input/preprocessor/#expr-syntax
{:/comment}

_expr_ has a syntax very similar to C.  It is composed of numbers, scalar variables, elements of vector variables, and macros, combined with unary and binary operators.
<pre>
    <i>Unary</i> operators take first precedence:
    1.   - arithmetic negative
         ~ logical negative (.not.)
           functions abs(), exp(), log(), sin(), asin(), sinh(), cos(), acos()
                     cosh(), tan(), atan(), tanh(), flor(), ceil(), erfc(), sqrt()
           Note: flor() rounds to the next lowest integer; ceil() rounds up.

    The remaining operators are <i>binary</i>, listed here in order of precedence with associativity
    2.   ^  (exponentiation)
    3.   *  (times), / (divide), % (modulus)
    4.   +  (add), - (subtract)
    5.   <  (.lt.); > (.gt.); = (.eq.); <> (.ne.); <= (.le.);  >= (.ge.)
    6.   &  (.and.)
    7.   |  (.or.)
    8&9  ?: conditional operators, used as: **test**?**expr1**:**expr2**
    10&11 () parentheses 
</pre>

The &nbsp;**?:**&nbsp; pair of operators follow a C-like syntax:
**test**, **expr1**, and **expr2** are all algebraic expressions.  
If **test** is nonzero, **expr1**  is evaluated and becomes the result.  Otherwise **expr1** is evaluated and becomes the result.  

#### Assignment Operators

The following are the allowed assignment operators:
<pre>
  assignment-op         function
    '='            simple assignment
    '*='           replace 'var' by var*expr
    '/='           replace 'var' by var/expr
    '+='           replace 'var' by var+expr
    '-='           replace 'var' by var-expr
    '^-'           replace 'var' by var^expr
</pre>

#### Examples of expressions

Suppose that the variables table looks like:
<pre>
   Var       Name                 Val
    1        t                   1.0000
    2        f                  0.00000
    3        pi                  3.1416
    4        a                   2.0000
 ...
   Vec       Name            Size   Val[1..n]
    1        firstnums          5    1.0000        5.0000
    2        nextnums           5    6.0000        10.000
 ...
     char symbol                     value
    1 c                               half
    2 a                               whole
    3 blank
</pre>

_Note:_{: style="color: red"} You can print out the current variables table with the **% show** directive.
 As described in more detail <A href="#variabledecl">below</A>, such a variables table can be created with the following directives:
<pre>
 % const a=2
 % char c half a whole blank " "
 % vec firstnums[5] 1 2 3 4 5
 % vec nextnums[5] 6 7 8 9 10
</pre>

Then the line
<pre>
  {c} of the {a} {pi} is {pi/2}</pre>
is turned into the following;
<pre>
  half of the whole 3.14159265 is 1.57079633</pre>
whereas the line
<pre>
  one quarter is {1/(nextnums(4)-5)}</pre>
 becomes
<pre>
  one quarter is .25</pre>

_Character Substrings Example_: 
<pre>
 % char c half a whole
  To {c(1,3)}ve a cave is to make a {a(2,5)}!
</pre>
 becomes
<pre>
  To halve a cave is to make a hole!
</pre>

_Vector Substitution Example_: 
<pre>
  {firstnums}, I caught a hare alive, {nextnums} I let him go again ...
</pre>
becomes
<pre>
  1 2 3 4 5, I caught a hare alive, 6 7 8 9 10  I let him go again ...
</pre>

_Nesting Example_: 
The following illustrates nesting to three levels.
The innermost block is substituted first.  Beginning with
<pre>
    % const xx{1{2+{3+4}1}} = 2
</pre>
substitution takes place in three passes:
<pre>
    % const xx{1{2+71}} = 2
    % const xx{173} = 2
    % const xx173 = 2
</pre>

_ Example of **{?~expr~strn1~strn2}** syntax_
<pre>
    MODE={?~k~B~C}3</pre>
 evaluates to, if _k_ is nonzero:
<pre>
    MODE=B3
</pre>
or, if k_ is zero:
<pre>
    MODE=C3
</pre>

_Note:_{: style="color: red"} the scalar variables table is always initialized with predefined variables **t=1** and **f=0** and **pi=&pi;.
It is STRONGLY ADVISED that you never alter any of these variables.

### _Preprocessor Directives_
{::comment}
/docs/input/preprocessor/#preprocessor-directives
{:/comment}


+ Lines beginning with &nbsp;**% keyword**&nbsp; are be interpreted as preprocessor directives.  Such lines are not part of the the post-processed input.
+ Lines which begin with &nbsp;**#**&nbsp; are comment lines and are ignored. (More generally, text following a **#** in any line is ignored).

Recognized keywords are
<pre>
   const cconst cvar udef var vec                        &larr; allocate and assign numerical variables
   char char0 cchar getenv vfind                         &larr; allocate and assign character variables
   if ifdef ifndef iffile else elseif elseifd endif      &larr; branching construct
   while repeat end   exit                               &larr; looping and terminating constructs
   echo include includo macro save show stop trace udef  &larr; miscellaneous
</pre>

#### Variable declarations and assignments
{::comment}
/docs/input/preprocessor/#variable-declarations-and-assignments
{:/comment}

&nbsp;&nbsp;Keywords&nbsp;:&nbsp;&nbsp; **const cconst cvar udef var vec char char0 cchar getenv vfind**

{::nomarkdown} <a name="vardec"></a> {:/}
{::comment}
(/docs/input/preprocessor/#vardec)
{:/comment}


1. **const**&nbsp; and &nbsp;**var**&nbsp; load or alter the variables table.  <i>Example</i>:
   <pre>% const  myvar=<i>expr</i> </pre>
   does two things:
   * adds **myvar** to the scalar variables symbols table if it is not there already.
         **const** and **var** are equivalent in this respect.
   * assigns the result of <i>expr</i> to it, if <i>either</i>
     * you use the **var** directive  <i>or</i>
     * you use the **const** directive <i>and</i> the variable had not yet been created.

   In other words, if &nbsp;**myvar**&nbsp; already exists prior to the directive,
   &nbsp;**const**&nbsp; will not alter its value but &nbsp;**var**&nbsp; will.  Thus the lines

       % const a=2
       % const a=3

   incorporate **a** into the symbols table with value 2, while

       % const a=2
       % var a=3

   does the same but assigns 3 to **a**.

   _Note:_{: style="color: red"} if **myvar** exists, you can
    multiply, divide, add, subtract from, or exponentiate it with _expr_, 
    using one of the following C-like syntax:
   <pre>
      myvar*=<i>expr</i>  myvar/=<i>expr</i>  myvar+=<i>expr</i>  myvar-=<i>expr</i>  myvar^=<i>expr</i>
   </pre>
   These operators modify &nbsp;**myvar**&nbsp; for both &nbsp;**const**&nbsp; and &nbsp;**var**&nbsp; directives.

2. **cconst**&nbsp; and &nbsp;**cvar**&nbsp; _conditionally_ load or alter the variables table.  <i>Example</i>:
   <pre>% cconst <i>test-expr</i> myvar=<i>expr</i> </pre>
   <i>test-expr</i>&nbsp; is an algebraic expression (e.g., **i==3**)
   that evaluates to zero or nonzero. <br>
   If <i>test-expr</i> evaluates to nonzero, the remainder
   of the directive proceeds as &nbsp;**const**&nbsp; or &nbsp;**var**&nbsp; do.<br>
   Otherwise, no further action is taken.

      &nbsp;&nbsp;<i>Example</i>: the input segment

       % const a=2 b=3 c=4 d=5
       A={a} B={b} C={c} D={d}
       % const a=3
       % var d=-1
       % const b*=2 c+=3
       A={a} B={b} C={c} D={d}
       % cconst b==6  b+=3 c-=3
       A={a} B={b} C={c} D={d}
       % cconst b==6  b+=3 c-=3
       A={a} B={b} C={c} D={d}

   generates four lines:

       A=2 B=3 C=4 D=5
       A=2 B=6 C=7 D=-1
       A=2 B=9 C=4 D=-1
       A=2 B=9 C=4 D=-1

   **a** is unchanged from its initial assignment while **d** changes.

   Compare the two &nbsp;**cconst**&nbsp; directives. **b** and **c** are altered in the first
   instance, since the condition &nbsp;**b==6**&nbsp; evaluates to 1, while they do not change in
   the second instance, since now &nbsp;**b==6**&nbsp; evaluates to zero.


3. **char** loads or alters the character table.  <i>Example</i>:

       % char  c half     a whole      blank

   loads the character table as follows:

        char symbol                     value
       1 c                               half
       2 a                               whole
       3 blank

   The last declaration can omit an associated string, in which case its value is a blank, as &nbsp;**blank**&nbsp; is in this case.

   _Note:_{: style="color: red"} Re-declaration of any previously defined variable will change the contents of the variable.

4. **char0**&nbsp; is the same as &nbsp;**char**&nbsp;, except re-assignment of an existing
   variable is ignored.  Thus &nbsp;**char0**&nbsp; is to &nbsp;**const**&nbsp; as &nbsp;**char**&nbsp; is to &nbsp;**var**&nbsp;.

5. **cchar**&nbsp; is similar to &nbsp;**char**&nbsp;&nbsp; but tests are made
   to enable different strings to be loaded depending on the results of the tests.
   The syntax is
   <pre>% cchar nam  <i>expr1</i> str1 /i>expr2</i> str2 ... </pre>
   **nam**&nbsp;&nbsp; is the name of the character variable;
    <i>expr1 expr2</i>&nbsp; etc are algebraic expressions.\\
   **nam**&nbsp;&nbsp; takes the
   value &nbsp;**str1**&nbsp;&nbsp; if &nbsp;<i>expr1</i>&nbsp;&nbsp; evaluates to nonzero, the value &nbsp;**str2**&nbsp;&nbsp; if &nbsp;<i>expr2</i>&nbsp;&nbsp; evaluates to nonzero, etc.

6. **getenv**&nbsp; has a function similar to **char**&nbsp;, only the contents of the variable are read from the unix environment variables table.  Thus
   <pre>% getenv myhome HOME </pre>
    puts the string of your home directory into variable **myhome**.

7. **vec**&nbsp; loads or alters elements in the table of vector variables.

       % vec v[n]                      &larr; creates a vector variable of length n
       % vec v[n] n1 n2 n3 ...         &larr; does the same, also setting the first elements

   Once **v**&nbsp; has been declared, individual elements of &nbsp;**v**&nbsp;&nbsp; may be set with the following syntax
<pre>
 % vec v(<i>i</i>) <i>n</i>                    &larr; assigns <i>n</i> to v(<i>i</i>)
 % vec v(<i>i1</i>:<i>in</i>)  <i>n1</i> <i>n2</i> ... <i>nn</i>    &larr; assigns range of elements <i>i1</i>..<i>in</i> to <i>n1 n2 ... nn</i>
</pre>
   There must be exactly **<i>in&minus;i1</i>+1**&nbsp; elements **<i>n1 ... nn</i>**&nbsp;.

   _Note:_{: style="color: red"} if **v**&nbsp; is already declared, it is an error to re-declare it.

8. **vfind**&nbsp; finds which element in a vector that matches a specified value.
   The syntax is
   <pre>% vfind v(i1:i2)  svar  <i>match-value</i> </pre>
   **svar**&nbsp; is a scalar variable and
   <i>match-value</i>&nbsp; a number or expression.
   Elements &nbsp;**v(i1:i2)**&nbsp;&nbsp; are parsed.
   &nbsp;**svar**&nbsp;&nbsp; is
   assigned to the the first instance i for which
   &nbsp;**v(i)**=<i>match-value</i>&nbsp;.
   If no match is found, **svar**&nbsp; is set to zero.\\
   &nbsp;&nbsp;<i>Example</i>:

       % vec  a[3] 101 2002 30003
       % vfind a(1:3) k 2002           &larr; sets k=2
       % vfind a(1:3) k 10             &larr; sets k=0

#### Branching constructs
{::comment}
/docs/input/preprocessor/#branching-constructs
{:/comment}

&nbsp;&nbsp;Keywords&nbsp;:&nbsp;&nbsp; **if ifdef ifndef iffile else elseif elseifd endif**

Branching constructs have a function similar to the C constructs.

1. **if <i>expr</i>**,&nbsp;**elseif <i>expr</i>**,&nbsp;**else**&nbsp; and &nbsp;**endif**&nbsp; are conditional read blocks.
   Lines between these directives are read or not, depending on the value of &nbsp;**<i>expr</i>**.  <i>Example</i>:

       % if Quartz
        is clear
       % elseif Ag
        is bright
       % else
        neither is right
       % endif

   generates this line  if &nbsp;**Quartz**&nbsp; evaluates to nonzero:\\
   &nbsp;&nbsp;&nbsp;&nbsp; **is clear**\\
   otherwise this line  if &nbsp;**Ag**&nbsp; evaluates to nonzero*\\
   &nbsp;&nbsp;&nbsp;&nbsp; **is bright**\\
   and otherwise\\
   &nbsp;&nbsp;&nbsp;&nbsp; **neither is right**

2. **ifdef**&nbsp; is similar to **if**&nbsp;, but has a more general idea of what
   constitutes an expression.

   <UL>

   <LI> <b>if</b> <i>expr</i>&nbsp;&nbsp; requires that <i>expr</i>&nbsp; be a valid
   expression, while &nbsp;<b>ifdef</b> <i>expr</i>&nbsp; evaluates <i>expr</i>&nbsp;
   as <b>false</b> if it invalid (e.g. it contains an undefined variable).

   <LI>
   <i>expr</i>&nbsp;&nbsp; can be an algebraic expression, or
   a sequence of expressions separated by
   &nbsp;<b>&</b>&nbsp;&nbsp; or &nbsp;&nbsp;<b>|</b>&nbsp;&nbsp;
   (<i>AND</i> or <i>OR</i> binary operators), <i>viz</i>:
   <pre>% ifdef <i>expr1</i> | <i>expr2</i> | <i>expr3</i> ...   </pre>
   If any of &nbsp;<i>expr1</i>,&nbsp;&nbsp; &nbsp;<i>expr2</i>,&nbsp;&nbsp; ... evaluate to nonzero, the result
   is nonzero, whether or not preceding expressions are valid.

   <br><FONT color="#ff0000"><I>Note</I>&nbsp;</FONT> the syntactical significance of the spaces.
   &nbsp;<i>expr1</i><b>|</b><i>expr2</i>&nbsp;&nbsp; cannot be evaluated unless both &nbsp;<i>expr1</i>&nbsp;&nbsp; and
   &nbsp;<i>expr2</i>&nbsp;&nbsp; are valid expressions, while &nbsp;<i>expr1</i>&nbsp; <b>|</b> &nbsp;<i>expr2</i>&nbsp;&nbsp; may
   be nonzero if either is valid.

   <LI> <b>ifdef</b>&nbsp;&nbsp; allows a limited use of character variables in expressions. Either of the following are permissible expressions:
   <pre>
      char-variable            &larr; T if char-variable exists, otherwise F
      char-variable=='<i>string</i>'  &larr; T ifchar-variable has the value <i>string</i> </pre>
   <i>Example</i>:
   <pre> % ifdef  x1==2 & atom=='Mg' | x1===1  </pre>
   is nonzero if scalar &nbsp;<b>x1</b>&nbsp;&nbsp; is 2 <i>and</i> if character variable &nbsp;<b>atom</b>&nbsp;&nbsp; is equal to "Mg",
   <i>or</i> if scalar &nbsp;<b>x1</b>&nbsp;&nbsp; is 1.

   <FONT color="#ff0000"><I>Note</I>&nbsp;</FONT> binary operators
   &nbsp;<b>&</b>&nbsp;&nbsp; and &nbsp;&nbsp;<b>|</b>&nbsp;&nbsp; 
   are evaluated left to right: &nbsp;<b>&</b>&nbsp;&nbsp; does not take precedence
   over &nbsp;<b>|</b>. <P><P>


3. **elseifd**&nbsp;&nbsp; is to &nbsp;**elseif**&nbsp;&nbsp; as &nbsp;**ifdef**&nbsp;&nbsp; is to &nbsp;**if**.

4. **ifndef** <i>expr</i>&nbsp; ... is the mirror image of &nbsp;**ifdef** <i>expr</i>.
   Lines following this construct are read only if &nbsp;**<i>expr</i>**&nbsp;&nbsp; evaluates to 0.

5. **iffile filename**&nbsp;&nbsp; is a construct analogous to &nbsp;**%if**&nbsp;&nbsp; or &nbsp;**%ifdef**&nbsp;&nbsp; for conditional reading of input lines.  
   The test condition is set not by an expression, but whether file &nbsp;**filename**&nbsp;&nbsp; exists or not.\\
   _Note:_{: style="color: red"}&nbsp; **if**,&nbsp;&nbsp; &nbsp;**ifdef**,&nbsp;&nbsp;
   and &nbsp;**ifndef**&nbsp;&nbsp; constructs may be nested to a depth of _mxlev_.
   The codes are distributed with _mxlev=6_.

#### Looping constructs
{::comment}
/docs/input/preprocessor/#looping-constructs
{:/comment}

&nbsp;&nbsp;Keywords&nbsp;:&nbsp;&nbsp; **while repeat end**

1. **while**&nbsp; and &nbsp;**end**&nbsp; mark the beginning and end of a looping construct.
   Lines inside the loop are repeatedly read until a test expression evaluates to 0.
   <pre>
   % while [<i>expr1</i> <i>expr2</i> ...] <i>test-expr</i> &larr; skip to `% end' if <i>test-expr</i> is 0
   ...                              &larr; these lines become part of the input while <i>test-expr</i> is nonzero
   % end                            &larr; return to the `% while' directive until <i>test-expr</i> is 0</pre>
   The (optional) expressions **[**<i>expr1</i> <i>expr2</i> ...**]**&nbsp;
   follow the rules of the &nbsp;**const**&nbsp;&nbsp; directive:
     * Each of <i>expr1</i>,&nbsp; <i>expr2</i>,&nbsp;, ... take the form &nbsp;**nam=** <i>expr</i>&nbsp;&nbsp; or &nbsp;**nam op=** <i>expr</i>.
     * A simple assignment &nbsp;**nam=**<i>expr</i>&nbsp;&nbsp; has effect only when &nbsp;**nam**&nbsp;&nbsp; has not
      yet been loaded into the variables table.  Thus it has effect on the first pass through the
     &nbsp;**while**&nbsp;&nbsp; loop (provided &nbsp;**nam**&nbsp;&nbsp; isn't declared yet) but not subsequent passes.

   These rules make it very convenient to construct loops, as the following example shows.
   <pre>
   % udef -f db                   &larr; removes db from symbols table, if it already exists
   % while db=-1 db+=2 db<=3      &larr; db is initialized to -1 only once
   this is db={db}                &larr; the body of the loop that becomes the input
   % end                          &larr; return file pointer to %while until test db<=3 is 0</pre>
   
   generates

       this is db=1
       this is db=3

   **Pass 1:** &nbsp;**db**&nbsp; is created and assigned the value &minus;1, then
   incremented to 1.
   Condition &nbsp;**db<=3**&nbsp;&nbsp; evaluates to 1 and the loop proceeds.\\
   **Pass 2:** &nbsp;**db**&nbsp; already exists so &nbsp;**db=-1**&nbsp; has no effect.
   &nbsp;**db+=2**&nbsp; increments &nbsp;**db**&nbsp;&nbsp; to **3**.\\
   **Pass 3:** &nbsp;**db**&nbsp;&nbsp; increments to **5** causing the condition
   &nbsp;**db<=3**&nbsp;&nbsp; to become 0.  The loop terminates.

2. **% repeat**&nbsp;&nbsp; ... &nbsp;**% end**&nbsp;&nbsp; is another looping construct with the syntax
   <pre>
   % repeat varnam <i>list</i>
    ...                             &larr; lines parsed for each element in list
   % end
   </pre>
   As with the &nbsp;**while**&nbsp;&nbsp;
   construct, multiple passes are made through the input lines.
   &nbsp;<i>list</i>&nbsp;&nbsp; generates a sequence of integers
   (see the [integer list syntax](/docs/misc/integerlists/) manual).
   For each member of the sequence &nbsp;**varnam**&nbsp;&nbsp; takes its value
   and the body of the loop passed through.
   &nbsp;<i>list</i>&nbsp;&nbsp; can be just an integer (e.g. &nbsp;**7**&nbsp;) or define a
   [more complex sequence](/docs/misc/integerlists/), e.g. &nbsp;**1:3,6,2**&nbsp;&nbsp;
   generates the sequence &nbsp;**1 2 3 6 2**.

   <i>Example:</i>  nested &nbsp;**while**&nbsp; and &nbsp;**repeat**&nbsp; loops
   <pre>
   % const nm=-3 nn=4
   % while db=-1 db+=2 db<=3
   % repeat k= 2,7
   this is k={k} and db={db}
   {db+k+nn+nm} is db + k + nn+nm, where nn+nm={nn+nm}
   % end (loop over k)
   % end (loop over db)
   </pre>
   The nested loops are expanded into:
   <pre>
   this is k=2 and db=1
   4 is db + k + nn+nm, where nn+nm=1
   this is k=7 and db=1
   9 is db + k + nn+nm, where nn+nm=1
   this is k=2 and db=3
   6 is db + k + nn+nm, where nn+nm=1
   this is k=7 and db=3
   11 is db + k + nn+nm, where nn+nm=1
   </pre>

#### Other directives
{::comment}
/docs/input/preprocessor/#other-directives
{:/comment}

&nbsp;&nbsp;Keywords&nbsp;:&nbsp;&nbsp;
**echo exit include includo macro save show stop trace udef**

1. **echo <i>contents</i>**&nbsp;&nbsp; echoes &nbsp;**<i>contents</i>**&nbsp;&nbsp; to standard output.
   <br><i>Example</i> :
   <pre>
   % echo hello world 
   </pre>
   prints
   <pre>
   #rf    <i>line-no</i>: hello world
   </pre>
   <i>line-no</i>&nbsp;&nbsp; is the current line number.

2. **exit [**<i>expr</i>**]**&nbsp;&nbsp; causes the program to stop parsing the input file, as though it encountered an **end-of-file**.
     * If &nbsp;<i>expr</i>&nbsp;&nbsp; evaluates to nonzero, or if it is omitted, parsing ends.
     * If &nbsp;<i>expr</i>&nbsp;&nbsp; evaluates to 0 the directive has no effect.

   _Note:_{: style="color: red"}&nbsp; compare to the &nbsp;**stop**&nbsp; directive.

3. **include filename**&nbsp; causes _rdfiln_{: style="color: green"} to include the contents file &nbsp;**filename**&nbsp; into the input.
     * If &nbsp;**filename**&nbsp; exists, _rdfiln_{: style="color: green"} opens it and the file pointer is transferred to this file until
       no further lines are to be read.  At that point file pointer returns to the original file.
     * If &nbsp;**filename**&nbsp; does not exist, the directive has no effect.

   _Notes:_{: style="color: red"}&nbsp;
   **%include**&nbsp; may be nested to a depth of 10.&nbsp;
   Looping and branching constructs <i>must</i> reside in the same file.

4. **includo filename**&nbsp; is identical to &nbsp;**include**&nbsp;, except that
   _rdfiln_{: style="color: green"} aborts if &nbsp;**filename**&nbsp; does not exist.

5. **macro(arg1,arg2,..)** <i>expr</i>&nbsp; defines a macro.
   **arg1,arg2,...**&nbsp; are substituted into <i>expr</i>&nbsp; before it is evaluated.
   <i>Example</i> :
   <pre>
   % macro xp(x1,x2,x3,x4) x1+2*x2+3*x3+4*x4
   The result of xp(1,2,3,4) is {xp(1,2,3,4)}
   </pre>
   generates
   <pre>
   The result of xp(1,2,3,4) is 30
   </pre>
   _Note:_{: style="color: red"}&nbsp; macros are not quite identical to function declarations.  The following lines illustrate this:
   <pre>
   % macro xp(x1,x2,x3,x4) x1+2*x2+3*x3+4*x4
   The result of xp(1,2,3,4) is {xp(1,2,3,4)}
   The result of xp(1,2,3,3+1) is {xp(1,2,3,3+1)}
   The result of xp(1,2,3,(3+1)) is {xp(1,2,3,(3+1))}
   </pre>
   generates
   <pre>
   The result of xp(1,2,3,4) is 30
   The result of xp(1,2,3,3+1) is 27
   The result of xp(1,2,3,(3+1)) is 30
   </pre>
   **macro**&nbsp; merely substitutes 1,2,3,... for &nbsp;**x1,x2,x3,x4**&nbsp; in &nbsp;**<i>expr</i>**&nbsp; as follows:
   <pre>
   1+2*2+3*3+4*4            &larr; xp(1,2,3,4)
   1+2*2+3*3+4*3+1          &larr; xp(1,2,3,3+1)
   1+2*2+3*3+4*(3+1)        &larr; xp(1,2,3,(3+1))
   </pre>
   Operator order matters, so &nbsp;**4**&nbsp; and &nbsp;**3+1**&nbsp; behave differently.
   By enclosing the fourth argument in parenthesis, operator precedence is maintained.

6. **save**&nbsp; preserves variables after the preprocessor exits.
   The syntax is:
   <pre>
   % save                   &larr; preserves all variables defined to this point
   % save <i>name</i> [<i>name2</i> ...]  &larr; saves only variables named
   </pre>
   Only variables in the scalar symbols table are saved.


7. **show ...**&nbsp; prints various things to standard output:
   <pre>
   % show vars              &larr; prints out the state of the variables table
   % show lines             &larr; echos each line generated to the screen until:
   % show stop              &larr; is encountered
   </pre>
   _Note:_{: style="color: red"}&nbsp;
   because the vector variables can have arbitrary length, &nbsp;**show**&nbsp; prints only the size of the vector and the first and last entries.  

8. **stop [**<i>expr</i> **msg**]&nbsp; : causes the program to stop execution.
     * If &nbsp;**<i>expr</i>**&nbsp; evaluates to nonzero, or if it is omitted, program stops (&nbsp;**msg**&nbsp;, if present, is printed to standard output before aborting).
     * If &nbsp;**<i>expr</i>**&nbsp; evaluates to 0 the directive has no effect.

   _Note:_{: style="color: red"}&nbsp; compare to the &nbsp;**exit**&nbsp; directive.

9. **trace**&nbsp;
   turns on debugging printout.  _rdfiln_{: style="color: green"} prints to standard output information about what it is doing.
     + **trace 0** turns the tracing off
     + **trace 1** turns the tracing on at the lowest level.\\
               _rdfiln_{: style="color: green"} traces directives having to do with execution flow
               (**if-else-endif**, **repeat/while-end**).
     + **trace 2** prints some information about most directives.
     + **trace 4** is the most verbose
     + **trace** (no argument) toggles whether it is on or off.

10.
   **udef [&minus;f]**&nbsp; <i>name</i> [<i>name2</i> ...]'&nbsp; remove one or more variables from the symbols table.
   If the &nbsp;**&minus;f**&nbsp;
   is omitted, _rdfiln_{: style="color: green"} aborts with error if you remove a nonexistent variable.
   If &nbsp;**&minus;f**&nbsp; is included, removing
   nonexistent variable does not generate an error.
   Only scalar and character variables may be deleted.

### _Source codes_
{::comment}
/docs/input/preprocessor/#source-codes
{:/comment}

Source codes the preprocessor uses are found in
the &nbsp;**slatsm**&nbsp; directory:

<pre>
   rdfiln.f  The source code for the preprocessor. Subroutine <b>rdfile</b> parses an entire file and
             and returns a preprocessed one, can be found in rdfiln.f
             The key subroutine is <b>rdfiln</b>, which parses one line of a file.
   symvar.f  maintains the table of variables for floating point scalars.
   symvec.f  maintains the table of vector variables.
   a2bin.f   evaluates ASCII representations of algebraic expressions using a C-like syntax, converting the
             result into a binary number. Expressions may include variables and vector elements.
   bin2a.f   converts a binary number into a character string (inverse function to a2bin.f).
   mkilst.f  generates a list of integers for looping constructs,
             as described below.  describes the syntax of integer lists.
</pre>

_rdfiln_{: style="color: green"} also maintains a table of character variables.  It is kept in the character array
<b>ctbl</b>, and is passed as an argument to _rdfiln_{: style="color: green"}.

_Note:_{: style="color: red"}&nbsp; the ASCII representation of a floating-point expression
is represented to 8 or 9 decimal places; thus it has
less precision than the binary form.  For example, **'{1.2345678987654e-8}'**&nbsp;
is turned into **1.2345679e-8**.

{::comment}
Note: kramdown needs 3-space indentation for outside list
      code blocks 3+4 spaces.
      nested lists 3+2 spaces
      <pre> ... </pre> must also be indented
{:/comment}
