# A
**A language compiles to C, which gets compiled to an executable or library. 
This means that A achieves the same cross-platform performance as C, with a
significantly simpler syntax and object-oriented code. 
Its main aim is to make developing software easy and fun.**

**Download the [A-Sample](https://github.com/Osiris-Team/A-Sample) repo,
open a terminal in that folder and
execute `./a/a` or `.\a\a.exe` if you are on Windows.**

You can learn the whole language by reading this file and if you are unsure how to pronouce A, there are two good tutorials [here](https://www.youtube.com/watch?v=yBLdQ1a4-JI) and [here](https://www.youtube.com/watch?v=pwTzHbIXSlI).




## Goals
 - Provide both high and low level methods to everything.
 - Don't overcomplicate things and keep it easy to read/write.
 - Stay relevant, meaning new good stuff will replace old stuff. Breaking changes are ok.
 - Allow easy use of GPU and hardware acceleration, instead of only writing CPU code.
 - Performance and readability have the same importance.
 - Provide cross-platform methods for all input and output devices, like (touch-)screens, keyboards, files, etc.
 - Encourage the use of event listeners.
 - Include a dependecy management system.



## Status
- No release yet, still in early development. 
- Once the basics are done 1.0 will get released.
- A compiler is written in Java.



## Statements
Multiple statements on a single line must be separated by a semicolon `;` otherwise
the semicolon is optional.
```A
int a = 10
int b = 20; int c = 30
```



## Comments
Everything inside a comment is ignored. 
`//` marks the start of a comment and goes until the end of the line.
```A
int a = 1 // Single line comment
```



## Variables/Types
- A variable is a particular set of bits or type of data located in the RAM that can be modified.
- To reference a specific variable it has to be named.
- The three major data types are numbers, text and special.
- All major types have minor types which are listed further below.
- Variables are made of 3 parts: `int a = 3` They have a type (int), name (a) and default value (3).
- Variables can be made of 2 parts: `a = 3`.
- If a variables' type is not set, it is determined and set by the compiler to the smallest type (or the biggest if it fails).
- The variable name cannot contain spaces.
- Variables can interact with each other via operands like `+ or -`.
- Variables are available in all your code without the need of importing something.

#### Numbers
 - `boolean` has only two possible values: true (1) and false (0). Represents one bit of information.
 - `byte` is an 8-bit signed two's complement integer. It has a minimum value of -128 and a maximum value of 127 (inclusive).
 - `short` is a 16-bit signed two's complement integer. It has a minimum value of -32,768 and a maximum value of 32,767 (inclusive).
 - `int` is a 32-bit signed two's complement integer, which has a minimum value of -2^31 and a maximum value of 2^31 -1.
 - `long` is a 64-bit two's complement integer. The signed long has a minimum value of -2^63 and a maximum value of 2^63 -1.
 - `float` is a single-precision 32-bit IEEE 754 floating point.
 - `double` is a double-precision 64-bit IEEE 754 floating point.
 
#### Text
 - `char` is a single 16-bit Unicode character. It has a minimum value of '\u0000' (or 0) and a maximum value of '\uffff' (or 65,535 inclusive).
 - `string` is a string of characters, or more accurately: an array of char with variable length.
 
 Text in code is identified by encapsulating the data in quotes `"`:
 ```a
 "a" // char
 "Hello World!" // string
 10 // number
 "10" // string
 ```
 
#### Special
 - `var` the generic type. Gets replaced by the actual type at compilation.
 - `code` is a single code block.
 - `<ObjectName>` is a type/object you created and named, that contains variables.

Variables can have additional/optional attributes which get added 
at the start: `public final int a = 10`
 - `public` makes the variable accessible from other files.
 - `final` makes the variable unchangeable after first value assignment.
 - `[<size>]` creates an array of the current type, of the specified size (integer type).



## Constructor and the `new`/`this` keywords
Objects get initialised by using the `new` keyword in code
like so:

`Main`
```A
Person john = new Person()
Person peter = new Person(35)
```
The constructor is the function called to initialise an object.
- It behaves like a regular function, which means that it can also have parameters,
  but no return type.
- There can be multiple constructors with different parameters.
- It gets added by the compiler automatically if not existing (with no parameters).

`Person`
```A
int age = 0

construct with {
}

construct with (int age) {
    this.age = age
}
```
The `this` keyword references the current object and can be used
to differentiate between variables with the same names as shown above.



## The `static` modifier
The `static` modifier makes a variable independent of its object
and thus must be accessed in another way:

`Main`
```A
Person.age  // valid
new Person().age // not valid
```

`Person`
```A
int public,static age = 0
```


## Scopes
A scope is code within brackets `{}`. 
Variables within a scope are not accessible from outside:
```A
int a = 3
{
  int b = 0
  // a can be used here
}
// a can be used here
// b cannot be used here
```
Let's say the code above is located in the `Utils` file and we want to access 
it in our `Main` file:
```A
Utils utils = new Utils()
utils.a // Can be accessed
utils.b // Error: Cannot be accessed
```



## Functions
Functions are special code blocks that are held by the `code` variable.
Note that functions are `final` by default due to the limitations by the underlying C language.
```A
int b = 0;
code setNumber = {
  b = 9
}
setNumber // Does nothing
setNumber() // Executes the code

code getNumber = returns int {
  return b
}
int result = getNumber() // Executes the code and returns b
```
Parameters can be passed over too like so:
```A
code multiply = (int a, int b) returns int {
  return a * b
}
multiply(10, 20)
```
Note that changing a parameter's value in a function,
affects the original variables value:
```A
code setTo10 = (int a) {
  a = 10
}
int myVariable = 27
setTo10(myVariable)
// myVariable is now 10
```

### Function overloading
Function overloading is not allowed. 
```a
code myFunction = {}
code myFunction = (int a) {} // error
```
This ensures that less duplicate documentation is written
and related code is inside the same function.



## Null safety and optional parameters
All variables must have a starting/default value when defined, which means
that code like this: `int a;` will not work.

Even optional function parameters cannot be null and must be handled
inside the function scope, inside "sub-functions".

You can make parameters optional by writing `optional: var1, var2, etc...`.
Here is an example:
```A
code multiply = returns int (int a, int b, optional: int c, int d){
  int result = a * b
  (c) = { // Only executed when c is provided
    result = result * c 
  }
  (d) = { // Only executed when d is provided
    result = result * d
  }
  return result
}
multiply(10, 20) // Valid
multiply(10, 20, 30, 40) // Not valid, optional parameters must have the variable name
multiply(10, 20, c:30, d:40); // Valid
multiply(10, 20, c:30) // Valid
multiply(10, 20, d:40) // Valid
```



## Files and Objects
Each file represents one object (must have no file extension).
```
project
 - Main
 - MathLib
 - folder
    - MathLib
    - AnotherLib
```
`MathLib` can be used in `Main` like so:
```A
MathLib math = new MathLib()
```
`MathLib` from /folder can be used in `Main` like so:
This must be done like this, since MathLib exists twice, in the
current folder and in /folder.
```
MathLib math = new MathLib()
./folder/MathLib math1 = new MathLib()
```
Normally you just enter the files'/folders' relative path on the top:
```A
./folder/AnotherLib

// Otherwise you can import the whole folder:
./folder

AnotherLib anotherLib = new AnotherLib()
```



## Inheritance
The first two lines of the file are reserved for `extends ...` and `overrides ...`.

`extends <obj1>, <obj2>, ...` lets you use all 
the listed objects methods and fields in your current object.
Their constructors are called in the order they were listed.
If there are overlapping functions (functions with equal names),
those objects cannot be extended.

`overrides <obj1>, <obj2>, ...` lets you use all 
the listed objects methods and fields in your current object, but you
must override all of them.
Their constructors must also be overriden, and are called in the order they were listed.
If there are overlapping functions (functions with equal names),
those objects cannot be overriden.

```A
extends AnotherObject, AnotherObject2
overrides AnotherObject3, /path/to/AnotherObject4
```

## Project structure and dependencies/libs
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
```A
./libs/lib_author/lib_name/lib_version/SomeObject
```

Updating libs can be done with the DMS too: `update libs` to update all, or
`update lib github_repo_url/github_repo_name` to update a specific lib.
The DMS will take care of renaming/updating your imports in your code too.
