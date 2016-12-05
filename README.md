# Guide for C# devs to learn F# real FAST

This guide is mostly samples based. It will take you 10minutes of your time
and you by understanding it you will already get a hang of 80% of the
language.

### Example 1: Basic function declarations and implementation

In C#

```
public int GiveMeTheLength(string input)
{
    var result = input.Length;
    return result;
}
```

In F#

```
let GiveMeTheLength(input) =
    let result = input.Length
    result
```

Things to note:
* All things are public by default unless you explicitly specify `private` modifier.
* Specifying types is always optional, except in very special cases.
* You don't need the `return` keyword. The last element of the function is the value to be returned.
* No need for braces, it works via 4-space indentation like Python.
* No need for semi-colons to denote the end of a line.

If you want to specify the types:

```
let Length(input: string): int =
    let result: int = input.Length
    result
```

### Example 2: Basic keywords and operators

In C#

```
using System;

class Main {
    void Main() {
        int exitCode = 0;
        if (incomingChar == Environment.NewLine)
            exitCode = 1;
        else if (!(incomingChar == String.Empty))
            exitCode = 2;
        else if (incomingChar != "\t" && incomingChar.Length > 1)
            exitCode = 3;
        Environment.Exit(exitCode);
    }
}
```

In F#

```
open System

let mutable exitCode = 0
if (incomingChar = Environment.NewLine) then
    exitCode <- 1
else if not (incomingChar = String.Empty) then
    exitCode <- 2
else if (incomingChar <> "\t" && incomingChar.Length > 1) then
    exitCode <- 3
Environment.Exit(exitCode)
```

Things to note:
* The `using` keyword becomes `open`.
* The `if () x; else y;` pattern becomes `if () then x else y`, with extra keyword `then`.
* Initial assignment (to a readonly constant) operator is `=`. If you need to re-assign a
new value to the same element, then you explicitly mark it as mutable and use the `<-`
operator.
* Thanks to the above, the `=` operator can be a comparison operator too (no need for
doubling it like in C#: `==`).
* Operator `!=` becomes `<>`.
* Operator `!` becomes `not`.
* Operators `&&` and `||` are same in F#.
* No need to enclose entry point of program in Main() function, just write your statements
in a .fsx script or write the statements in the last .fs fed to the F# compiler.

In general, such a simple piece of code like the one in the example can be coded easily
without a mutable variable, just by doing readonly assignments, this way:

```
open System

let exitCode =
    if (incomingChar = Environment.NewLine) then
        1
    else if (incomingChar = String.Empty) then
        2

Environment.Exit(exitCode)
```

(This is similar to the use of the `?` operator in C#, but more succint and easy to read.)


## Example 3: Basic blocks

In C#

```
try {
    TrySomething();
} catch (SomeEx) {
    DoSomethingElse();
} finally {
    MakeSureToCleanup();
}
```

In F# the `catch` keyword becomes `with`. However, there are no `try-with-finally` blocks!
We have only `try-with` blocks and `try-finally` blocks. Therefore the equivalent in F# would need nesting:

```
try
    try
        TrySomething()
    with
    | SomeEx(ex) -> DoSomethingElse()

finally
    MakeSureToCleanup()
```

### Example 4: Basic collections

In C#

```
var list = new int[] { 1, 2, 3 }
IEnumerable<int> sequenceOfIntegers = list;
```

In F#

```
let list = [ 1 ; 2 ; 3]
let sequenceOfIntegers: seq<int> = list
```

Things to note:
- `IEnumerable<T>` becomes `seq<T>`
- Commas become semicolons when enumerating elements of array.


### Example 4: Avoiding nulls and ignoring things

In C# you write null checks everywhere:

```
void Check(SomeType someParam1, SomeType someParam2)
{
    if (someParam1 != null)
        stringBuilder.Append(someParam.ToString());

    if (someParam2 != null)
        stringBuilder.Append(String.Empty);
}
```

In F#, `void` becomes `unit`, and you do
the null check with an `Option<T>` type (similar
to `Nullable<T>`) and a match expression (pattern
matching):

```
let Check(someParam1: Option<SomeType>,
          someParam2: Option<SomeType>): unit =

    match someParam1 with
    | Some(someValue) -> // like 'as' in C#, you cast and want the value
        let str = someValue.ToString()
        ignore(stringBuilder.Append(str))
    | None -> ()

    match someParam2 with
    | Some(_) -> // like 'is' in C#, you don't care about the value
        stringBuilder.Append(String.Empty) |> ignore
    | _ -> ()

```

Things to note:
* A `match-with` block is almost like a switch block, but more succing because it includes the casting (to someValue).
* There are three ways of ignoring things:
** For example, we don't care about the return value of Append(), in C# we just ignore it but in
F# you need to be explicit about ignoring it, using the `ignore()` magic function.
** The underscore in a match expression: it's like a `default` in a C# `switch`.
** The underscore in `Some(_)`, when we want to make sure the value is not None, but we don't care
about its contents (like an `is` operator in C#, instead of `as`).
* The pipe operator (like in bash) is `|>`. Then `ignore(x)` is the same as `x |> ignore`.
* Do nothing is `()`.

------------------------------------------------------

CONGRATS!! You already know enough to maybe understand 80% of F# code.
Or maybe 80% of simple F# code, which is the code that is being used
in most F# scripts: easy code.

I could explain you how to build the equivalent of a `class` or `struct` in F#
(respectively types and records, both denoted with the `type` keyword), but
most scripts don't even need types, they just need functions, values and calls.