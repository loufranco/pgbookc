# pgbookc

pgbookc is a script that will "compile" an iOS Swift Playground Book from a Contents folder from inside a host app.

The Contents folder must match [the .playgroundbook package specification](https://developer.apple.com/library/prerelease/content/documentation/Xcode/Conceptual/swift_playgrounds_doc_format/).

To allow the given Contents folder to be used in an app, pgbookc will make several transformations to the files inside of it.

The .playgroundbook package requires that there be a Contents.swift and LiveView.swift inside each page folder.  An app does not allow two .swift files with the same name, so:

1. Any file named Contents-*.swift will be renamed to Contents.swift. 
1. Any file named LiveView-*.swift will be renamed to LiveView.swift. 

It is likely that you need to include code in the app files that you don't want in the book. For example:

1. You many need to workaround a difference in the version of Swift or frameworks in Xcode vs. Swift Playgrounds.
1. You may need to wrap playground code that must be at the top-level, but app code requires it in a function.

To facilitate this, there are several comment styles that will affect the output:

`// REMOVE LINE`

Any line of code that ends with `// REMOVE LINE` will be removed in the book. This allows you to write:

```swift
func ContentsPage1() -> Bool { return // REMOVE LINE
funcIWantToCallInPlayground()
} // REMOVE LINE
```

Doing this will leave `funcIWantToCallInPlayground()` at the top level in the book. Additionally, `ContentsPage1()` is defined and can be called inside a unit test to test the playground.

`//-REMOVE`

If `//-REMOVE` is in a file, it (and only it) will be removed. This allows you to comment out a line that you want only to be in the Playground Book, but not appear in the app. One reason to do this is that the line might not work in the app, but it would in the Playground (because of Swift or Framework differences). Another reason is that you want to provide partially complete or uncompilable code for the reader to fix.

For example:

```swift
//-REMOVEfuncThatTakesArg(/*#-editable-code *//*#-end-editable-code*/)
```

This line would not compile in either the app or the book, but the exercise here is for the user to fill in the argument, so it would be ok to leave it in the book. To keep the app buildable, we would want it to be commented out. Another common thing is to put a `<#placeholder#>` in the code, which is not compilable in apps, but lets the Playground Book reader tap and replace it.

There is also `/*-REMOVE` and `REMOVE-*/` for doing the same thing, but for a section of lines.