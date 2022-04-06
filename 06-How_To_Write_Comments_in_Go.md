### Introduction

Comments are lines that exist in computer programs that are ignored by compilers and interpreters. Including comments in programs makes code more readable for humans as it provides some information or explanation about what each part of a program is doing.

Depending on the purpose of your program, comments can serve as notes to yourself or reminders, or they can be written with the intention of other programmers being able to understand what your code is doing.

In general, it is a good idea to write comments while you are writing or updating a program as it is easy to forget your thought process later on, and comments written later may be less useful in the long term.

Comments in Go begin with a set of forward slashes (`//`) and continue to the end of the line. It is idiomatic to have a white space after the set of forward slashes.

Generally, comments will look something like this:

Comments do not execute, so there will be no indication of a comment when running a program. Comments are in the source code for humans to read, not for computers to execute.

In a “Hello, World!” program, a comment may look like this:

hello.go

In a `for` loop that iterates over a slice, comments may look like this:

sharks.go

Comments should be made at the same indent as the code it is commenting. That is, a function definition with no indent would have a comment with no indent, and each indent level following would have comments that are aligned with the code it is commenting.

For example, here is how the `main` function is commented, with comments following each indent level of the code:

color.go

Comments are made to help programmers, whether it is the original programmer or someone else using or collaborating on the project. If comments cannot be properly maintained and updated along with the code base, it is better to not include a comment rather than write a comment that contradicts or will contradict the code.

When commenting code, you should be looking to answer the _why_ behind the code as opposed to the _what_ or _how_. Unless the code is particularly tricky, looking at the code can generally answer the _what_ or _how_, which is why comments are usually focused around the _why_.

Block comments can be used to explain more complicated code or code that you don’t expect the reader to be familiar with.

You can create block comments two ways in Go. The first is by using a set of double forward slashes and repeating them for every line.

The second is to use opening tags (`/*`) and closing tags (`*/`). For documenting code, it is considered idiomatic to always use `//` syntax. You would only use the `/* ... */` syntax for debugging, which we will cover later in this article.

In this example, the block comment defines what is happening in the `MustGet()` function:

function.go

It is common to see block comments at the beginning of exported functions in Go; these comments are also what generate your code documentation. Block comments are also used when operations are less straightforward and are therefore demanding of a thorough explanation. With the exception of documenting functions, you should try to avoid over-commenting the code and trust other programmers to understand Go, unless you are writing for a particular audience.

Inline comments occur on the same line of a statement, following the code itself. Like other comments, they begin with a set of forward slashes. Again, it’s not required to have a whitespace after the forward slashes, but it is considered idiomatic to do so.

Generally, inline comments look like this:

Inline comments should be used sparingly, but can be effective for explaining tricky or non-obvious parts of code. They can also be useful if you think you may not remember a line of the code you are writing in the future, or if you are collaborating with someone who you know may not be familiar with all aspects of the code.

For example, if you don’t use a lot of math in your Go programs, you or your collaborators may not know that the following creates a complex number, so you may want to include an inline comment about that:

You can also use inline comments to explain the reason behind doing something, or to provide some extra information, as in:

You should only use inline comments when necessary and when they can provide helpful guidance for the person reading the program.

In addition to using comments as a way to document code, you can also use opening tags (`/*`) and closing tags (`*/`) to create a block comment. This allows you to comment out code that you don’t want to execute while you are testing or debugging a program you are currently creating. That is, when you experience errors after implementing new lines of code, you may want to comment a few of them out to see if you can troubleshoot the precise issue.

Using the `/*` and `*/` tags can also allow you to try alternatives while you’re determining how to set up your code. You can also use block comments to comment out code that is failing while you continue to work on other parts of your code.

multiply.go

**Note**: Commenting out code should only be done for testing purposes. Do not leave snippets of commented out code in your final program.

Commenting out code with the `/*` and `*/` tags can allow you to try out different programming methods as well as help you find the source of an error through systematically commenting out and running parts of a program.

## Conclusion

Using comments within your Go programs helps to make your programs more readable for humans, including your future self. Adding appropriate comments that are relevant and useful can make it easier for others to collaborate with you on programming projects and make the value of your code more obvious.

Commenting your code properly in Go will also allow for you to use the [Godoc](https://godoc.org/golang.org/x/tools/cmd/godoc) tool. Godoc is a tool that will extract comments from your code and generate documentation for your Go program.