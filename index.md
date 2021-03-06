# Sapphire
language of cherry picked functional & objective.

# Document definition
syntax is wrote in [ABNF](https://en.wikipedia.org/wiki/Augmented_Backus–Naur_form)

## predefined rule
### ABNF core rules
```ABNF
<ALPHA> = %x41-5A / %x61-7A ; alphabets [a-zA-Z]
<DIGIT> = %x30-39 ; digit [0-9]
<HEXDIG> = DIGIT / "A" / "B" / "C" / "D" / "E" / "F" ; [0-9a-fA-F]
<DQUOTE> = %x22 ; double quote
<SP> = %x20 ; space
<HTAB> = %x09 ; tab
<WSP> = <SP> / <HTAB>
<LWSP> = *(<WSP> / <CRLF> <WSP>)
<VCHAR> = %x21-7E ; printable characters
<CHAR> = %x01-7F ; ascii characters without NUL
<OCTET> = %x00-FF ; 8bit data
<CTL> = %x00-1F / %x7F ; control characters
<CR> = %x0D
<LF> = %x0A
<CRLF> = <CR> <LF>
<BIT> = "0" / "1"
```

### implement rule
```ABNF
<implement>
```

### util rules
```ABNF
<eol> = <CRLF> / <CR> / <LF>
<unicode-char> = <implement> ; implement unicode characters (without control character, but included WSP)
```

# Outline

* [Control](#Control-statements)
* [Lexical elements](#Lexical-elements)
    * [identify](#identify)
    * [comments](#comments)
    * [reserved keywords](#reserved-keywords)
    * [operators](#operators)
        * [unary operators](#unary-operators)
        * [binary operators](#binary-operators)
        * [ternary operator](#ternary-operator)
        * [assignment operators](#assignment-operators)
        * [access operators](#access-operators)
        * [call operator](#call-operator)
    * [variable](#variable)
    * [string and character literal](#string-and-character-literal)
    * [numeric literal](#numeric-literal)

# Control statements

## import
### syntax
```ABNF
<import> = "import" <module-name>
<import-func> = "import" <function-names> "of" <module-name>

<function-names> = <function-name> *("," <function-name>)
```

# Lexical elements

## identify
### syntax
```ABNF
<identify> = <ALPHA> *(<ALPHA> / <DIGIT> / "_")
```

## comments
### syntax
```ABNF
<comments> = <line-comment> / <block-comment>
<line-comment> = "//" *(<unicode-char>) <eol>
<block-comment> = "/*" *(*(<unicode-char>) <eol>) "*/"
```
### example
```
// line comment
/*block comment*/
```
## reserved keywords
```ABNF
<keyword> = <implement> ; anyword of follow contents
```

> reserved keywords contains not used for compatibility of other languages (ex. c/c++).

```
alias
align
array
asm
async
assert
auto

bool
break
byte

case
cast
catch
char
class
compiled
const
continue

default
delegate
delete
deprecated
do
double

else
enum
export
extern

false
final
float
for
foreach
function

goto

if
immutable
import
in
inout
int
interface
is

lazy
let
long
local

macro
module

new
nothrow
null

out
override

package
pragma
private
protected
public

ref
return
scope
shared
short
static
string
struct
switch
sync

template
this
throw
true
try
type
typeid
typeof

ubyte
uint
ulong
unicode
union
unit
ushort
ustring

while
with

__file_path__
__file_name__
__function__
__line__
```

## operators
### unary operators
```ABNF
<unary-operators> = "++" / "--" / "**" / "!" / "^"
```
### binary operators
```ABNF
<binary-operators> = "+" / "-" / "*" / "/" / "&" / "&&" / "|" / "||" / ">>" / "<<" / "|>" / "%"
```

### ternary operator
exist only **?:**

### syntax
```
<sentence> "?" <sentence> ":" <sentence>
```

### assignment operators
```ABNF
<assignment-operators> = "+=" / "-=" / "*=" / "/=" / "|=" / "&=" / "%="
```

### access operators
```
[]
*
&
.
->
.?
```

### call operator
```
()
```


## variable


### variable attributes

|attribute name|effect|
|---|---:|
|let|mutable|
|lazy|lazy evaluation and mutable|
|const|immutable|

### builtin types

|type name or syntax||
|---|---:|
|(\<arguments\>)->\<return-type\>|function|
|bool|boolean|
|byte|signed 8bit integer|
|ubyte|unsigned 8bit integer|
|short|signed 16bit integer|
|ushort|unsigned 16bit integer|
|int|signed 32bit integer|
|uint|unsigned 32bit integer|
|long|signed 64bit integer
|ulong|unsigned 64bit integer|
|float|32bit floating point|
|real|64bit floating point|
|char|utf8 character|
|string|utf8 string|

### builtin structure types

|type syntax||
|---|---:|
|[\<type\>]|types array|
|(\<type01\>,\<type02\>,...)|tuple|

### syntax
```ABNF
<variable> = (<variable-attribute>  <variable-declaration> *("," <variable-declaration>) ";") / (<omit-variable> *("," <omit-variable>) ";")
<variable-declaration> = <typed-variable> / <deduced-variable>
<typed-variable> = <identify> ":" <type-attribute> ["=" <initializer>]
<deduced-variable> = <identify> "=" <initializer>
<omit-variable> = <identify> ":=" <initializer>
```

### example
```
let normal_variable : byte = 10; //mutable byte value
let m0 : int, m1 : short; //multiple declaration
let deduced_variable = "message"; //deduce to string
const const_value : int = 7; //read only
omit_variable := 5; //deduce to let int

//if "normal_variable" is changed to 5 when evaluating the number of variables, the result is 50.
lazy lazy_value : byte = normal_variable * 10;
```

## string and character literal
### syntax
```ABNF
<character-literal> = "\'" <unicode-char> "\'"
<string-literal> = "\"" *<unicode-char> "\""
```

## numeric literal
### syntax
```ABNF
<integer-literal> = *<DIGIT> ; use cast,if you want to set type of smaller or bigger than int
```

# function

### syntax
```ABNF
<forward-declare-function> = <identity> "(" <type-attribute> *("," <type-attribute>) ")" "->" <type-attribute>
<function-body> = [<identity>] "(" <type-attribute> *("," <type-attribute>) ")" "->" <type-attribute> <function-block>
<function-block> = 1*(<function-pattern>) / <function-scope>
<function-pattern> = [<pattern>] <function-scope>
<function-scope> = "{" *(<expression>) "}"
```

### example
```
// forward declaration
add (int, int) -> int;

// body declaration
add (let left : int, let right : int) -> int {
    return left + right;
}

// lambda
let add = (let left : int, let right : int) -> int {
    return left + right;
}

// generic
let add = (let left, let right) {
    return left + right;
}

// pattern
let add = (let left, let right)
|> left == 0 && right == 0 {
    return 0;
}
|> left == INF && right == INF {
    return INF;
}
```