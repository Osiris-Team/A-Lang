# A-Lang
A language is my idea of a perfect programming language, which was
born from the frustration and joy of using languages like Java, C, C++ and JavaScript.

There is an actual compiler written in Java that compiles A => C => binary.
Thus A achieves the same cross-platform performance as C.

Its main aim is to make programming fun and provide higher-level concepts without sacrificing performance.
Functions are variables for example. To be exact, everything in this language is a variable and
you can learn the whole language by reading this file.

If you want to get started right away, download the [A-Sample](https://github.com/Osiris-Team/A-Sample) repo,
open a terminal in that folder and
execute `./a/a` on Linux or `.\a\a.exe` if you are on Windows (this starts the A compiler command-line interface).



<details>
<summary><b>Core Ideas, Highlights and Status</b></summary>

 ## Core Ideas
 - Tiny standard library, to keep the focus on language development. This will change once the language is more mature.
 - Allow easy use of GPU / hardware acceleration, instead of only writing CPU code.
 - Provide cross-platform methods for all input and output devices, like (touch-)screens, keyboards, files, etc.
 - Include a dependency management system and provide an online repository.
 - Include a build-tool and make compilation frictionless.
 - Performance, fast writability, and readability have the same importance.
 - Encourage the use of event listeners.
 - Garbage collected via [bdwgc](https://github.com/ivmai/bdwgc), can be [disabled](#TODO).
 - Library developers should benefit the same amount as their users, in terms of language features.
 - Language patterns/features should be simple, consistent, logical, and similar to each other.

## Highlights
 - Modular Object Oriented Programming (MOOP) via [object parts](#object-blocks--parts).
 - Compiles to C and [allows embedding raw C code](#TODO).
 - Everything is a variable and [primitives functionality can be extended](#Extending-functionality).

## Status
- No release yet, still in early development (spec- and implementation-wise). Once the basics are done 1.0 will get released.
- This repository contains the A compiler which is written in Java and misses a bunch of the features mentioned in this file.
However it more or less lays the groundwork and style for the compiler.
</details>



## Statements
Multiple statements on a single line must be separated by a semicolon `;` otherwise
the semicolon is optional.
```java
a = 10
b = 20; c = 30
```

⚠️ All variables are pointers. Thus keep in mind that something like `a = b` does **not** copy/clone the value of b into a instead these are now fully equal
and changing the value for one affects the other, meaning a points to b. Thus look out when dealing with primitives especially, you might want something like this `a = b.clone()` instead.
<details>
 <summary>How is this still typesafe?</summary>

Because null values are not allowed, every variable has a default value from which we determine the type.
</details>

<details>
 <summary>Issues with readability?</summary>

With this Python-like approach, we do not know if it's the initial usage / declaration of the variable or updating/setting the variable, and would need to rely on IDEs to make that difference clear.
Which I do not really like because of github PRs for example where the code is basically in its raw text form and only has some syntax highlighting.

Thus maybe instead something like this?
```java
new a = 10
new b = 20; new c = 30
```
I think in this case we could directly just insert the type name instead of "new", to increase readability, however for longer types + if we some day get generics
this can also decrease readability.

Another problem with this is that it might seem we also create a "new" object with this keyword even though we only create a new variable/pointer, for example: 
```java
new a = 10
new b = a
```
</details>



## Comments
Everything inside a comment is ignored. 
`//` marks the start of a comment and goes until the end of the line.
```java
a = 1 // Single line comment
```



## Values, Variables and Types
- A variable is a value with a name/reference. It's a particular set of bits or type of data located in the RAM that can be modified.
- Primitive types (listed below) are available in all your code without the need of importing something.
- Providing the type before the variable name is optional, meaning both `int x = 10` and `x = 10` are valid, since types are inferred.

### Numbers
| Usage                 | Name    | Description                                                                                                         |
|-----------------------|--------|---------------------------------------------------------------------------------------------------------------------|
| `v = true` or `v = false` | boolean | Has only two possible values: true (1) and false (0). Represents one bit of information.                            |
| `v = 1b`              | byte    | 8-bit signed two's complement integer. It has a minimum value of -128 and a maximum value of 127 (inclusive).       |
| `v = 1s`              | short   | 16-bit signed two's complement integer. It has a minimum value of -32,768 and a maximum value of 32,767 (inclusive) |
| `v = 1`               | int     | 32-bit signed two's complement integer, which has a minimum value of -2^31 and a maximum value of 2^31 -1.          |
| `v = 1l`              | long    | 64-bit two's complement integer. The signed long has a minimum value of -2^63 and a maximum value of 2^63 -1.       |
| `v = 1f`              | float   | Single-precision 32-bit IEEE 754 floating point.                                                                    |
| `v = 1d` or `v = 1.0`     | double  | Double-precision 64-bit IEEE 754 floating point.                                                                    |

### Text
| Usage          | Name   | Description                                                                                                                       |
|---------------|--------|-----------------------------------------------------------------------------------------------------------------------------------|
| `v = "a"`     | char   | Single 16-bit Unicode character. It has a minimum value of '\u0000' (or 0) and a maximum value of '\uffff' (or 65,535 inclusive). |
| `v = "aa"`    | string | String of characters, or more accurately: an array of char with variable length.                                                  |

### Special 
| Usage              | Name       | Description                                                                                       |
|--------------------|------------|---------------------------------------------------------------------------------------------------|
| `{}`               | code       | Single code block.                                                                                |
| `v = {}` or `v = (){}`  | function   | Function without arguments and empty body. As you can see you can "promote" any code block into a function easily by giving it a name/variable. |
| `v = ObjectName()` | ObjectName | Comes from the ObjectName.a file you created. If in another folder requires an import to be used. ObjectName is a placeholder for anything you might come up with. |
| `v = (0, 1, 2)` | `[int*3]` | Fixed size array of elements. Inspired by the function arguments syntax. Supports different types per index/element. If defining the type you must provide the type of its elements and their amounts. |

### Attributes
Variables can have additional/optional attributes which get added 
at the start: `hidden final a = 10`
 - `hidden` hides the variable from other files. 
You can disable this by adding `show hidden` to the start of your file.
 - `final` makes the variable unchangeable after first value assignment.
 - `global` makes the variable globally accessible in the format `ObjectName.myGlobalVariable` and thus the variable lives the whole runtime of the program.

### Arrays/Tuples
Additional features of arrays/tuples and some syntax clarification if you need to write down the type of an array:
```java
[int] array = (10, 11, 12) // Type error, max size is not given, more accurately its wrong 1 != 3
[int] array = (10) // Valid, correct max size: 1
[int*1] array = (10) // Same as above
[int*3] array = (10, 11, 12) // Valid: max size info given, in this case its 3

valueAtIndex0 = array[0] // 10, use brackets to access the value at an index
valueAtIndex1 = array[1] // 11

[int, string] array = (10, "hello") // You can use any amount of different types

[int*100] array = (0...*100) // Fill with 0 values
[int*50, string*50] array = (0...*50, "hello"...*50) // Works with any types
size = array.length // 100
// Note that .length is not a real variable, instead its a placeholder. The compiler replaces it with the actual numeric value.

// switch supports arrays
array = (1, 2, 3)
txt = ""
switch x {
  if (1, a, b) { txt = "Contains a 1 at first position!" }
  if (a, 1, b) { txt = "Contains a 1 at second position!" }
  else { txt = "Unknown number pattern!" }
}
```

### Extending functionality
Primitives are strictly speaking not really primitives but objects located in `./a`, thus you can also extend their functionality by using the object parts feature explained further below. 

Note that primitives must not be imported because the compiler adds a hidden import statement to import everything from `./a`.

<details>
<summary>Why is there no public/private?</summary>
 
All variables are public by default. If you search public and private on GitHub
you will see that public is used around 422 million times and private only 177M times,
thus public is the default, to reduce the amount of code written.
</details>

<details>
<summary>Where is null?</summary>

Gone! Due to the countless runtime errors arising from it and because otherwise
we would need to name the type when defining a variable. This way its ensured all variables
have a default value which lets us determine the type at compile time and gives us the same
benefits as statically typed languages.
</details>


## Code/Scopes
A scope is code within brackets `{}`. 
Variables created within a scope are not accessible from outside:
```java
a = 3
{
  b = 0
  // a can be used here
}
// a can be used here
// b cannot be used here
```

Let's say the code above is located in the `Utils` file and we want to access 
it in our `Main` file:
```java
Utils utils = Utils()
utils.a // Can be accessed
utils.b // Error: Cannot be accessed
```
</details>

⚠️ Note that a code block executes directly, but you can promote it to a function by naming it. Thus do not forget
to call the function to actually execute your code if you did so.

## Functions
Functions are special code blocks that are held by the `function` variable.
Note that functions are `final` by default due to the limitations by the underlying C language and
we must provide type information for the arguments and return type.
```java
b = 0;
setNumber = {
  b = 9
}
setNumber // Does nothing
setNumber() // Executes the code

getNumber = returns int {
  return b
}
result = getNumber() // Executes the code and returns b
```
Parameters can be passed over too like so:
```java
multiply = returns int (int a, int b) {
  return a * b
}
multiply(10, 20)
```
Note that changing a parameter's value in a function,
affects the original variables value:
```java
setTo10 = (int a) {
  a = 10
}
myVariable = 27
setTo10(myVariable)
// myVariable is now 10
```


### Side effect-free functions and clone
The compiler enforces the usage of double parenthesis when defining and using a function that has no side effects,
meaning that it doesn't update its arguments or other variables/fields of the object (except global fields). For this to work you either only read the argument or clone it and update its clone only.

So we can modify the previous example, which removes the whole purpose of the setTo10 function, however it should clarify everything:
```java
setTo10 = ((clone int a)) {
  a = 10
}
myVariable = 27
setTo10((myVariable))
// myVariable is still 27, because the operation is performed on a clone/copy
```

The `clone` keyword in a function parameter simply is a quality of life addition to reduce code, the previous example would look much worse without it:
```java
setTo10 = ((int a)) {
  a1 = a.clone()
  a1 = 10
}
myVariable = 27
setTo10((myVariable))
// myVariable is still 27, because the operation is performed on a clone/copy
```

<details>
<summary>TODO How to reduce code duplication for libraries?</summary>
 
 Let's say we have a string object and we want to provide a `str.replace("x", "y")` function with replaces all x characters with y in this case,
 we would know that it affects the string directly / itself. Now we also want to provide `str1 = str.replace(("x", "y"))` function which does the same,
 however returns a new string with the changes. Does the library developer really need to define the function twice and code the logic twice?
</details>


### Function overloading
Function overloading is not allowed. 
```java
myFunction = {}
myFunction = (int a) {} // error
```
This ensures that less duplicate documentation is written
and related code is inside the same function.

Optional function parameters should help you out if you allow multiple different types of inputs in your function for example.
This is documented further below.


### Return multiple values
Sometimes you want to return multiple values from a single function.
In most languages you would need to create a new class or a new datatype which can be annoying.
A however has a built in solution for this to make it easier:
```java
myFunction = returns [int*2] {
  return (10, 20)
}
array = myFunction()
a = array[0] // == 10
b = array[1] // == 20
// or simpler:
a, b = myFunction() // a == 10, b == 20
```

### Special functions
There are different types of functions / code blocks, which all extend the `code` type: `function, if, if else, else if, switch, for, for each, while`.
These will be explained further below. 

<details>
 <summary><h4>Logic: if, if else, else if, switch</h4></summary>
 
```java
a = true
b = true
if a { // short version for: if a == true
  a = false // do something if a is true, set it to false for example
} else { // else is optional
  a = false
}

// inline example
if a do a = false 
else do a = false // else is optional

if a > b do a = false
else if b do b = false // elseIf example, also optional

// you can easily make it a variable/function
myLogic = if a do a = false
myLogic()

// do not confuse the above with this:
result = if a use false
// result is now false and not a function because of the "use" keyword

// note that switch also supports arrays/tuples (see that section for details)
x = 10
txt = "Its a 10!"
switch x {
  if 10 { txt = "Its a 10!" }
  if 3 { txt = "Its a 3!" }
  else { txt = "Its another number!" }
}

// inline example
switch x 
  if 10 do txt = "Its a 10!"
  if 3 do txt = "Its a 3!"
  else do txt = "Its another number!"
```
</details>

<details>
 <summary><h4>Loops: for, for each, while</h4></summary>
 
```java
numbers = (1, 2, 3, 4)
current = 0
for index i = 0; i > numbers.length; i++ {
  current = numbers[i] // access number at index position in numbers array
}

// inline example
for index i = 0; i > numbers.length; i++ do current = numbers[i] 

for each x in numbers {
  current = x
}

// inline example
for each x in numbers do current = x 

for each x in numbers and index i { // for each with optional index
  current = x
}

// inline example
for each x in numbers and index i do current = x

i = 0
while i < numbers.length {
  current = numbers[i]
  i++
}

// inline example
i = 0
while i < numbers.length do current = numbers[i]; i++
```
</details>

Since everything is a variable you can convert a `for index` loop into a variable.

⚠️ Keep in mind that this converts the code block that executed directly, into a function that only executes when you call it, for example:
```java
numbers = (1, 2, 3, 4)
current = 0
myLoop = for index i = 0; i > numbers.length; i++ {
  current = numbers[i] // access number at index position in numbers array
}
myLoop() // Only executed once you call it
```


## Null safety and optional parameters
All variables must have a starting/default value when defined, which means
that code like this: `a;` will not work. Thus there is no `null` type in A.

Optional function parameters must have a default value.

You can make parameters optional by writing `optional: var1, var2, etc...`.
Here is an example:
```java
multiply = returns int (int a, int b, optional: int c = 1, int d = 1){ 
  return a * b * c * d
}
multiply(10, 20) // Valid
multiply(10, 20, 30, 40) // Not valid, optional parameters must have the variable name
multiply(10, 20, c:30, d:40); // Valid
multiply(10, 20, c:30) // Valid
multiply(10, 20, d:40) // Valid
```



## The `constructor` keyword
The main paradigm is modular object oriented programming.
Objects get initialized by using the `ObjectName()`
like so:

`Main`
```java
john = Person(63)
peter = Person(35)

john.age // == 63
john.id // == 1

peter.age // == 35
peter.id // == 2

Person.count // == 2
```
`Person`
```java
global count = 0

constructor = (int age) {
    count++
    id = count
}
```
The constructor is the function called to initialize an object:
- There can only be one in a file/object.
- It is similar to a regular function, which means that it can also have parameters,
  but no return type (note that the parameters will be available to the instantiated object if not set to private).
- It gets added by the compiler automatically if not existing (with no parameters).
- Variables defined inside the constructor are only available to the instantiated object.

Note that everything you write outside the constructor will only be executed once, specifically at the first time the file/object is used somewhere else.



## Files/Objects
Each file represents one object.
```
project
 - Main.a
 - MathLib.a
 - folder
    - MathLib.a
    - AnotherLib.a
```
`MathLib` can be used in `Main` like so:
```java
math = MathLib()
```
`MathLib` from /folder can be used in `Main` like so:
This must be done like this, since MathLib exists twice, in the
current folder and in /folder.
```
math = MathLib()
math1 = ./folder/MathLib()
```
Normally you just enter the files'/folders' relative path on the top:
```java
./folder/AnotherLib

// Otherwise you can import the whole folder:
./folder

anotherLib = AnotherLib()
```



## Object
The default object all other objects automatically extend from contains following functions:
- `obj.free()` for optional manual memory management, after calling this the memory for this object is cleared and referencing it is not allowed anymore.
- `obj.clone()` returns a deep copy of the object.


## Object Blocks / Parts
```
/Main.a
/Person.a
/Brain.a
/Hand.a
```
Object Blocks make it possible to break down
an otherwise very large file into multiple smaller files, since an Object Block has access to the code
of all the other related blocks (except for optional Object Blocks).

Besides that it allows "true" functionality extension of existing Objects (for example from an Object of another library, or even primitive types)
with no need of code refactoring.

Brain:
```
part of Person
```

Hand:
```
optional part of Person
```

Usage in Main.a:
```
// How do I use the Person class with its Object Blocks?
p = Person()

// How do I make sure Person gets loaded with all Object Blocks, even optional ones?
p = Person+*()

// How do I only use specific optional Object Blocks?
p = Person+Hand()
```



## Inheritance
There are two ways of inheriting another objects' functionality, namely
via the `inherits` and `implements` keywords.

`inherits <obj1>, <obj2>, ...` lets you use the methods and fields of the inherited object in your current object.
- Their constructors are called in the order they were listed.
- If there are overlapping top-level variables (for example functions with equal names),
you also need to specify the variable name behind the object like so (myVariableName exists inside AnotherObject and AnotherObject2 in this example): `inherits AnotherObject, AnotherObject2+myVariableName`.

`implements <obj1>, <obj2>, ...` lets you use the methods and fields of the implemented object in your current object, but you
must provide your own implementation for all of them.
- Their constructors must also be implemented, and are called in the order they were listed.

```java
inherits AnotherObject, AnotherObject2
implements AnotherObject3, /path/to/AnotherObject4
```



## Object Semantic Versioning
This is more relevant for library developers since it forces you at compile time to update your objects' version if you
did either a major/breaking change, an addition like adding a new function or variable, or minor code changes / bug fixes.
To enable this feature simply add something like `version 1.0.0` to the top of your file.

Its usage is optional, but it's recommended for any objects you expect your users to touch, if you are a library developer.

If a user uses your library and wants to ensure the final executable contains no duplicate dependencies
and the user has another library (lets call it XTremeLib) that uses your library too, but an older version, now the compiler can check
for version conflicts that would otherwise only be visible through bugs at runtime, since the user has to choose
a specific version of your library, which may break XTremeLib.



## Project structure and dependencies/libraries
The project root directory is located where your `a` directory is in.
In your code, you can only use/import code that is within that folder.
To reference it in code use `./`.

The A compiler has a dependency management system (DMS) built in.
Currently, it supports A projects hosted and released on GitHub.
With the command `add lib github_repo_url/github_repo_name`, the DMS
will fetch the `src.zip` of the projects' latest release and extract its
contents into `./libs/lib_author/lib_name/lib_version`.

To use that lib in your code, you would simply import it by entering
its path at the top of your file.
```java
./libs/lib_author/lib_name/lib_version/SomeObject
```

Updating libs can be done with the DMS too: `update libs` to update all, or
`update lib github_repo_url/github_repo_name` to update a specific lib.
The DMS will take care of renaming/updating your imports in your code too.

## TODO
- Implement most of the mentioned features.
- Spec for multithreading
  - maybe even event-loop based (javascript)
  - and where sleeping does not block the actual thread, but instead yields like in java virtual threads?
- Spec for errors/exceptions.
- Spec for GPU / hardware acceleration, or no spec if code can be fully auto-generated by the compiler at the needed places.
- Spec for all mathematical primitive operations.
