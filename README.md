# objc-swift-python-extension

An experiment in building macOS Python (.so) extensions containing Swift code

This is an Xcode project which builds a compiled Python extension on macOS containing Swift code.

## Quick start

Open `spam.xcodeproj` in Xcode and build; then, in a terminal, change to the directory containing the built product `spam.so` (type `cd `, drag `spam.so` from the Xcode Products folder to the terminal, and backspace over `spam.so` leaving only its directory); then start Python:

```
$ python3
Python 3.11.2 (main, Feb 16 2023, 03:15:23) [Clang 14.0.0 (clang-1400.0.29.202)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import spam
>>> spam.add(3,4)
This is in Swift
7
>>> c = spam.Counter()
>>> c
<spam.Counter; count:0>
>>> c.increment()
>>> c.count
1
>>>
```

## The code

The code is a minimal Hello-World-style example, intended as a test of the process of building a Python extension.  As such, it produces a file named `spam.so` implementing a module which is  named, as per Pythonic tradition, `spam`. This module contains one standalone function: `add`, which takes two integers and returns their sum, and a class, `Counter`, which implements a simple integer counter, with a read-only `count` attribute and `increment()` and `reset()` methods. The business logic (the addition function and the counter class) is implemented in a Swift source file.

## Methodology

It appears to not be currently possible to compile Swift code to binaries complying with C calling conventions, and thus the foreign function interfaces of languages such as Python which expect dynamic libraries with C-style code. (While Swift has a `@convention(c)` attribute for specifying this calling convention, it is not available for use in function declarations, but only for callbacks passed to C functions called from Swift.) It also does not appear to be possible to define self-contained functions in Swift to be accessible to C code within the same project; C->Swift interoperability only covers Objective C classes.

As such, the approach used here is to define an Objective C-based class named `Glue`, with a number of class methods to act as relays between the C code exposed to Python and the Swift code containing the business logic. This, going through Objective C's dynamic dispatch mechanism, incurs a minor performance penalty, though this is not unreasonable, particularly in the context of communicating with a Python interpreter.

## Known issues

Attempting to load the compiled module after copying it to `/tmp` may fail with a code signing error,


