<!-- 
Use Visual Studio Code to edit and view this Markdown.

##### Editing TODOs
* Find some unlucky soul to proofread.
* Make sure everything is relevant.
* Check for missing topics.
* Check for things that can be cut out.
* Check for parts that are over-explained and may come off as patronising.
* Check for parts that assume knowledge and aren't explained deeply enough.
* Check the ordering of topics to best facilitate learning.
    * Data Types may be better placed near the beginning instead of after Operators.
* Check that topics are under the correct headings.
* Check for errors.
* Figure out how to export this doc so it looks good as a PDF. Do you actually want a PDF.

##### Content TODOs (search for more in-line todos)
* testing and debugging
    * test the limits of your code just before, the limit, and just after
* Events and triggers
* explain sanitising input
* blocking vs non-blocking (probably not relevant, but leaving it here for now)
* Figure out the order of operations for operations
-->



## Preface

This is intended to be an introduction to PangoScript and general programming concepts within the context of PangoScript.

New to lasers and with only access to the Beyond demo, this is written solely from the experience of a software developer. Thus, doesn't currently have practical examples of how to use PangoScript.

> <blue>**Note:**</blue> `�` in the official Help represents an error in the document, and the original symbol is unknown.

## Intro

PangoScript is a programming language used to provide custom behaviour in Beyond.

## Structure overview

* A PangoScript is a series of one or more [statements](#statements), each performing an action.
* Statements usually begin with a command that may have one or more parameters, which are values passed into the command to customise its action.
* Anything that is considered a value is called an [expression](#expressions).
    * For example, the statement `Size 80, 80, 50 + 30` consists of a command and three expressions as arguments.
* We can use a construct called a [variable](#variables) to store and name and the result of an expression.
* The variable's name is used to reference or update the stored value.
    * If we had the variables `x = 80` and `z = 50 + 30`, we could write `Size x, 80, z`. 
* We generally write one statement per line, and by default, they're executed as quickly as possible from top to bottom.
* Some statements allow us to [control the flow](#control-flow-statements) of execution.
    * For example, [conditionally](#if-statement) executing a statement, [jumping](#jump-statements) to another place, starting a different script, or exiting the current script early.

## Conventions

* Consistency is generally more important than convention. The goal is to make your code easier to read and understand.
* In documentation, `[]` specifies optional parts. E.g. for `Command arg1, [, arg2, arg3, ...]` only `arg1` is required for `Command`
    * E.g. the documented command `SetFX LayerIndex, EffectIndex [, EffectIndex, EffectIndex, ...]` requires at least one EffectIndex, but can accept more.
* In documentation, `<>` are descriptive placeholders for values you're meant to enter. E.g. `"Hi, I'm <name>."` or `Size <x>, <y>, <z>`. They are not valid PangoScript code.
* See [Footguns](#footguns)


### Case sensitivity

* PangoScript is not case-sensitive. For example, `TRUE`, `True`, `true`, and `tRuE` are all evaluated identically.
* Different "case types" are used as a convention to improve readability and understanding. For example:
    * `CONSTANT_NAMES` (macro case)
    * `LabelNames`, `FunctionNames`, and `CommandNames` (pascal case)
    * `variableNames` (camel case)

### Indentation

* Indentation has no effect on execution.

### Comments

* On a line, everything after `//` is considered a comment.
* Use comments to explain things that are unclear or why it's done.

### Naming

* Use descriptive names for variables and labels so that it's clear what they represent.
* In any complex script, ambiguously naming variables and labels

## Syntax

The rules that define how all the symbols of a programming language can be used.

## Semantics

Semantics refers to the meaning of code rather than the rules, i.e. syntax.

Some input values are syntactically valid for functions and operators, but produce unexpected or invalid results. This can be due to incompatible values, errors in logic, or unconventional behaviour. Results may appear correct for some values, possibly many, but not all.

> **Example 1:**  
> The [mod operator](#modulus-mod--integers-only) `%` should not be used with floating point numbers but will still evaluate them. This is because PangoScript will `round()`* the floating point inputs before the calculation.  
> `5.4 * 5 = 27` is correct.  
> `27 / 5.4 = 5` is correct.  
> `27 % 5.4 = 2` is incorrect as PangoScript initially rounds `5.4` to `5`. The expected result is `0`.  
> \* PangoScript always rounds `.5` down instead of up.

## Predefined constants

These words can be used in place of a number for expressions.

```js
TRUE   =  1
FALSE  =  0
ON     =  1
OFF    =  0
ANY    = -1 // (Used in WaitFor commands)
TOGGLE =  2 // (used in commands like MasterPause)
ASIS   = -2
```
> <yellow>**Warning:**</yellow> If these words are declared as a [variable name](#variables), the variable will, from that point, override the constant within the variable's scope.

## Operators

Special symbols that perform an action with one or more values. Each operator will only work with certain [data types](#data-types).

### Order of Operations

Operators are evaluated in this order. Those in the same level evaluate left to right.
Use parenthesis to ensure correctness and improve readability. 

1. `()`
2. `!`
3. `*`, `/`, `%`, `<<`, `>>`
4. `+`, `-`

### Arithmetic operators

#### Addition `+` (Any Number)

#### Subtraction `-` (Any Number)

#### Division `/` (Any Number)

#### Modulus (Mod) `%` (Integers only)

* Always returns an integer.
* PangoScript will automatically `round()`* all floating point inputs to integers before performing the calculation.

#### Inversion `!` (Integers only)
* This is the only unary operator and has the form `!<integer>`
* Given `!x` returns `-(x) - 1`. E.g. `!1 = -2`, `!100 = -102`, and `!0 = -1`
* When not a variable, negative numbers must be given in parentheses. E.g. `!(-100)`
> <blue>**Note:**</blue> To only flip the sign use `!x + 1`

> <yellow>**Warning:**</yellow> This is not a "not" operator, thus doesn't work for `TRUE` and `FALSE`. E.g. `!true = -2`.

### Assignment operator

#### Assignment `=`

* Used to assign a value to a [variable](#variables).
* Only assignment when **not** in parenthesis `()`, as `(a = b)` is considered a comparison expression.
* Cannot be chained as it doesn't return anything. The statement `a = b = c = 5` will only assign `c = 5`. `a` and `b` will remain unchanged. `Write a = a + b` will always print `0`.

### Comparison operators
Conditional operators are used within parenthesis `()` to create a conditional [expression](#expressions) that evaluates to `0` if false or `1` if true.

> <blue>Note:</blue> Comparison operators cannot be chained like `(a < b < c)` to see if `b` is strictly between `a` and `c`. First, `a < b` will evaluate to a [boolean](#3-boolean--bool-), and then the resulting boolean will be compared to `c`. The correct way is `((a < b) & (b < c))`. Combining conditionals must always be done using logical operators.

> <blue>Note:</blue> It's suggested to always use parenthesis for readability and ensure operations are done in the correct order. Operators have a specific order of operations. E.g. `(a | b & c)` is different from `(a | (b & c))`.

#### Greater than `>`
* `(a > b)` is `TRUE` if `a` is strictly larger than `b`

#### Greater than or equal to `>=`

* `(a >= b)` is `TRUE` if `a` is equal to `b` or larger than `b`

#### Less than `<`

#### Less than or equal to `<=`

#### Equal to `=`

#### Does not equal `<>`

### Bitwise logical operators (Integers only)

* OR, AND, and XOR are mainly used to combine conditional expressions.
* They operate on the binary representation of integers.
* **Always** use parenthesis around conditional expressions when combining with logical operators.

#### Bitwise OR `|` 

* `(a | b)` is only false if both expressions `a` and `b` are false, otherwise true.

#### Bitwise AND `&`

* `(a & b)` is true only if both expressions `a` and `b` are true, otherwise false.

#### Bitwise XOR `^`

* `(a ^ b)` is true only when one of the expressions `a` or `b` is true, but not both.
* This is also called "EOR",  "Exclusive OR".

#### Truth tables

<table>
  <tr>
    <td>
      <table>
        <tr>
          <th>|</th>
          <th>0</th>
          <th>1</th>
        </tr>
        <tr>
          <th>0</th>
          <td>0</td>
          <td>1</td>
        </tr>
        <tr>
          <th>1</th>
          <td>1</td>
          <td>1</td>
        </tr>
      </table>
    </td>
    <td>
      <table>
        <tr>
          <th>&</th>
          <th>0</th>
          <th>1</th>
        </tr>
        <tr>
          <th>0</th>
          <td>0</td>
          <td>0</td>
        </tr>
        <tr>
          <th>1</th>
          <td>0</td>
          <td>1</td>
        </tr>
      </table>
    </td>
    <td>
      <table>
        <tr>
          <th>^</th>
          <th>0</th>
          <th>1</th>
        </tr>
        <tr>
          <th>0</th>
          <td>0</td>
          <td>1</td>
        </tr>
        <tr>
          <th>1</th>
          <td>1</td>
          <td>0</td>
        </tr>
      </table>
    </td>
  </tr>
</table>

### Bitwise shift operators (Integers Only)

* These operate on the binary (base-2) representation of an integer. See [Binary](#binary--bin-)
* All computer storage and operations are done in binary.
* Bit shifting is incredibly fast for the CPU to perform when compared to multiplication or division.
* 8 bits = 1 byte
* > These operators are only used in very specific instances. There's a good chance you'll never need them.

#### Bitwise right shift `>>`

* `a >> b` means shift the binary representation of `a` to the right by `b` bits.
* `a >> 1` means to bit shift `a` once to the right. 
* > E.g. `1111 >> 1 = 111`, or in PangoScript `(15 >> 1) = 7` or `(0xF >> 1) = 7` as we cannot write binary representations in PangoScript.
* `a >> 3` means to bit shift `a` three times to the right. E.g. `11011 >> 3 = 11`, or in PangoScript `(27 >> 3) = 3`.
* The shifted bits at the end are just discarded.
* `a >> 1` is the same as `Int(a / 2)`, which means it discards the fractional component. E.g. in PangoScript `15 >> 1 = 7` and `15 / 2 = 7.5`

> <blue>Note:</blue> Beyond froze and crashed when I tried `writeln 15 >> 1` but worked with `writeln (15 >> 1)`.

#### Bitwise left shift `<<`

* `a << b` means shift the binary representation `a` to the left by `b` bits. ( Append `b` number of zeroes. )

> `7 << 3 = 56` and in binary representation `111 << 3 = 111000`

## Data types

Broadly, PangoScript only has **Strings** and **Numbers**, but numbers have three specific types, Integer, Float, and Boolean. 

* A data type describes what kind of thing a value is and what operations can be performed on it.
* In PangoScript, a value's type is based on its form. They are not explicitly set.
* The Help occasionally groups integers, floats, binary, and hexadecimals as a **Number** type.

> <yellow>**Warning:**</yellow> As there's no safeguards, it is your responsibility to ensure variables are of the correct type when evaluated.

> <blue>**Note:**</blue> The State tab in the PangoScript Editor will display variable values prefixed with `n` for **number** and `s` for **string**. For example, `a = 123` will appear as `a=n123`, and `b = "123"` will appear as `b=s123`

### 1. Integer ( int )

* Any number with without a fractional part. E.g. `..., -2, -1, 0, 1, 2, 3, ...`
* Hexadecimal and Binary are different ways of representing an integer, they are not data types.
* We typically use a decimal format (base-10) meaning each digit has ten distinct values, `0 - 9`.
* All values written and logged by PangoScript will be in decimal format.
* All values are evaluated as decimal format unless specifically prefixed with 0x for hex.

#### hexadecimal ( hex )

Hexadecimal is base-16, so each digit has sixteen distinct values. Instead of stopping at `9`, like in decimal format, it continues with letters `0, ... , 8, 9, A, B, C, D, E, F`, where `F` equals `15`.

In PangoScript, integers can be written as a hexadecimal number using the prefix `0x`. PangoScript never returns or outputs hexadecimal representations, e.g. `WriteLn 0xf4` will print `20`. 

These are commonly used to specify RGB color values. E.g. `0 - 255` is the same as `0x0 - 0xFF`, and color pickers often have `#000000 - #FFFFFF` meaning `#<RR><GG><BB>`.

Each digit is four bits making it somewhat easier to convert hex to binary than decimal to binary.

#### Binary ( bin )

You cannot express integers as binary in PangoScript. Where a binary is used, like in bit shift operators, a decimal or hexadecimal format must be written.

> The Windows 10 Calculator has a "Programmer" mode that works only with integers. It simultaneously shows hexadecimal, decimal, and binary representations as an easy way to convert between them without needing a separate program. 

<table>
  <tr>
    <td>
      <table>
        <tr>
          <th>Integer</th> <th>Binary</th> <th>Hex</th>
        </tr>
        <tr>
          <td>0</td> <td>0000</td> <td>0x0</td>
        </tr>
        <tr>
          <td>1</td> <td>0001</td> <td>0x1</td>
        </tr>
        <tr>
          <td>2</td> <td>0010</td> <td>0x2</td>
        </tr>
        <tr>
          <td>3</td> <td>0011</td> <td>0x3</td>
        </tr>
        <tr>
          <td>4</td> <td>0100</td> <td>0x4</td>
        </tr>
        <tr>
          <td>5</td> <td>0101</td> <td>0x5</td>
        </tr>
        <tr>
          <td>6</td> <td>0110</td> <td>0x6</td>
        </tr>
        <tr>
          <td>7</td> <td>0111</td> <td>0x7</td>
        </tr>
      </table>
    </td>
    <td>
      <table>
        <tr>
          <th>Integer</th> <th>Binary</th> <th>Hex</th>
        </tr>
        <tr>
          <td>8</td> <td>0100</td> <td>0x8</td>
        </tr>
        <tr>
          <td>9</td> <td>0101</td> <td>0x9</td>
        </tr>
        <tr>
          <td>10</td> <td>0110</td> <td>0xA</td>
        </tr>
        <tr>
          <td>11</td> <td>0111</td> <td>0xB</td>
        </tr>
        <tr>
          <td>12</td> <td>0100</td> <td>0xC</td>
        </tr>
        <tr>
          <td>13</td> <td>0101</td> <td>0xD</td>
        </tr>
        <tr>
          <td>14</td> <td>0110</td> <td>0xE</td>
        </tr>
        <tr>
          <td>15</td> <td>1111</td> <td>0xF</td>
        </tr>
      </table>
    </td>
  </tr>
</table>

### 2. Float

* Any number with a non-zero fractional part.
* They have 8 digit precision.

> <yellow>**Warning:**</yellow> **Floats** are not compatible with all commands and operators that expect numbers. Even if evaluated, invalid values will be returned. This means you need to be careful with expressions. 

### 3. Boolean ( bool )

* A single digit binary number that is either `0` or `1`
* Generally used to represent true/false, but can also mean on/off, yes/no, etc.
* Required for conditional operators and some commands.

### 4. String

* Strings are defined by a pair of double quotes `" "`
* To include `"` in a string, they need to be 'escaped' by use two. E.g. `writeln "one ""two"" three"` prints `one "two" three`

## Variables

* Variables holds a value that can change, and provides a name to reference that value.
* Variable names must start with a letter and can only contain letters, numbers, and underscores.
* A variable's type changes dynamically depending on the value.

### Declaration

* Variables must be declared once using `var` or `globalvar` before they can be used.
* After declaring, immediately assign an initial default value. `0` is the typical unless there is a more sensible default.

> <blue>**Note:**</blue> When testing in the BEYOND 5 Editor, a default value of `0` is set automatically after declaring. If declared again, the current value is lost and is set to `0`.
> * The Help explicitly says the script will error if the variable isn't manually initialized after declaration, so this implicit initialization to `0` may be new.
> * Regardless, it is usually a good practice to initialize variables with default values so that it's explicit what they are.

#### Local variables `var`

* Available in the current script from the time of declaration until the script exits.
* Not accessible by any other scripts.

#### Global variables `globalvar`

* Available in all scripts from the time of declaration until BEYOND exits.
* Global variables can be used for the configuration of multiple scripts or to create a global state.

## Expressions

* Expressions are pieces of code that evaluate to a value.

## Statements

Statements are a pieces of code that perform an action. They are made up of expressions and other statements.

* While convention is to have one statement per line, multiple statements can be put on the same line by separating with semicolons `;`.

### Functions and commands

Functions and commands refer to the built-in callable statements in PangoScript. The PangoScript Help differentiates the terms by using "function" to only refer to "pure functions".

* **Pure functions** only return a value and do not have side-effects. As they return a value, they're considered expressions.

* **Commands** perform an action.

> Modernly, they're both just referred to as "functions" and you would need to specifically search for "pure function".

### Contextual statements (Search "The Destination" in the PangoScript Help for additional info)

* Some commands, e.g. `Size <x>, <y>, <z>`, change whatever is set as the current context (destination).
* By default, the context is **Master**.
* Statements such as `ControlCue <pageIndex>, <cueIndex>` allows us to change the current context.
* Changing the context affects all following statements within the script until changed again or the script exits.

```js
// Set the single cue live control size to 50% for the first cue on the first page.
ControlCue 1, 1  // first page, first cue
Size 50, 50, 50
```

### Control flow statements

#### If statement

Conditionally execute a single statement.

* Has the form `if ( <expression> ) <statement>`
* `<expression>` must resolve to a number, and often utilizes the [comparison operators](#comparison-operators).
* `<statement>` will be executed if `expression` is True (True is defined as any number except `0`).
* Can only execute a single statement, i.e. multiple statements cannot be chained on the same line with semicolons.

> To conditionally execute multiple statements, you can invert the expression and use a `goto` statement to skip over the section to execute. The disadvantage is that you need a unique label for each `if` block. (A block of code is a grouped bit of code.)
> 
> ```js
> var result; result = (expression)  // Evaluate once instead of multiple times as the evaluation may change if the expressions variables change.
> if (result) statement1
> if (result) statement2
> if (result) statement3
>```
>
>```js
> if (expression = FALSE) goto AfterIf  // Skip if expression is false
>     statement1
>     statement2
>     statement3
> AferIf:
> ```

#### Jump statements

#### `goto` and labels

The command `goto <label>` immediately jumps to `<label>` and continues execution from that point. A label is a unique string ending with a semicolon `:` and is scoped to the script.


#### `restart`

Jump to the beginning of the script.

#### `exit`

Immediately exits the script, stopping its execution.

## Concepts

### Scope

Scope refers to where something can be used and how long it lives. A label and variable's scope is the script in which they are defined. A global variable's scope is all scripts from the point it is defined.

### Testing and debugging

Testing is best done incrementally as you're writing scripts as testing a complex script at the end will make it more difficult to find and fix bugs.

We can test logic by using output statements, `write` and `writeln`.

The output window only shows the last 32 lines of output, so to clear it we can use the following loop:

```js
var i; i = 0
loop:
writeln ""
i = i + 1
sleep 1
if (i < 32) goto loop
```

### State

A program is stateful if it stores information that may be recalled and affect future execution. This information is stored in variables. A program's state is all the values of these variables at a particular point in execution.

State can also refer to the set of global variables to define the state of a program. A 'config' refers to a set of values used to set the initial state of a program. 

#### Context

Context is the part of the program's state that is relevant to the current task. The context of the `Size` function is whatever the destination is currently set to. [Contextual statements](#contextual-statements-search-the-destination-in-help-for-additional-info) are used to set the context for other commands.

### Polling vs interrupts

These are concepts used in the exchange of information. Polling is periodically checking for new information. Interrupts trigger the receiving code to immediately process the incoming information.

> Polling is a nurse checking on a patient every hour. Interrupting is the patient hitting the emergency call button.

### Events and triggers

<red>TODO</red>

### Loops

A loop is a way to repeatedly execute code. PangoScript only has `goto` and `restart` commands that we can use for looping.

* Infinite loops are loops that never stop as they never hit an `exit` or `goto` statement used to "break" out of the loop. 

* Use `Sleep <ms>` statements to slow down execution and reduce the computational load on Beyond. Without sleep, long loops can easily overload and possibly crash Beyond.


```js
var i; i = 0  // index variable must be declared and initialised outside of the loop
BeginLoop:

WriteLn i    // Loop body

i = i + 1
Sleep 10    // pause for 10ms
if (i < 10) goto BeginLoop
```

### Version control system (VCS)

A tool used to track code change over time, allow multiple people to work on the same codebase simultaneously, merge changes together, and maintain multiple versions. [Git](https://git-scm.com/) is the most popular version control system. Specifically, Git is a distributed version control system (DVCS), meaning you have both a local repository (on your machine) and remote repository that stores all content and changes. GitHub, GitLab, BitBucket are all popular remote repository services for Git.

## Footguns

These are common things that can increase complexity, ambiguity, and bugs as scripts become large and complex.

* Magic numbers - these are raw integers used in expressions. As they have no descriptive information, their function can be unintuitive in all the most obvious of uses. Try to avoid them where possible. If you need to use non-obvious numbers, consider defining a 'static' variable with a descriptive name and use that instead. At the very least, add a comment explaining why/what it is.

* Rigidly and blindly following "best practices". They may be a good starting point, but may not always be the "best". Try to understand the implications of using them. "Standard practices" may be a better term.

* Not sanitising input.

* Poorly naming variables. Naming is hard, but makes everything much easier to understand and remember. Avoid single letter variable names like a, b, x, y unless it's a convention or immediately obvious what it means.

> Conventionally, `i`, `j`, and `k` are often used as counter/index variable names when looping in other programming languages.

* Too many comments or no comments. Comments are great to explain why things are done in a particular way, or the intent of unclear code. Comments also often don't get updated and maintained as code changes. This can mean that what a comment says and what the code does can be different. Use comments sparingly, and don't use comments to say what each line is doing unless it's not obvious. Let the code speak for itself where possible.

* Testing large scripts at the end instead of as they're developed. Testing individual pieces of code is much easier than everything all at once at the end. It may be difficult to determine what's causing a bug. 


## Undocumented and unsupported features

<red>**Anything in this section may change functionality without warning, not work as expected, or be removed entirely at any time. It's included for awareness and discussion.**</red>

### 2024-07-18 - FaceBook post asking about redundant computer failover if main computer fails during show with FB4s
Source: https://www.facebook.com/groups/PoweredbyPangolin/posts/7802818416438343/
```js
StartFb4Discovery // enable FB4 discovery and auto-connect
StopFb4Discovery // disconnect FB4 and disable auto connect
Fb4DiscoveryMode 0 // 0-always, 1-auto, 2-manual
// Auto mean - if one BEYOND see the other, it will not try to connect to FB4.
```

<style>
yellow { color: yellow; }
red { color: Tomato; }
green { color: MediumSeaGreen; }
blue { color: DodgerBlue; }
h3 { text-decoration: underline; }
</style>
