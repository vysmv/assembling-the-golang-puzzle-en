# Learning Go. Part 2: Documentation as a tool

Hello, everyone.

In this video, I will discuss the topic of “documentation.”  
At first, I wasn't going to make a video about this, but then I thought that some of you might find it interesting after all.  
That's because, by analyzing how to use documentation, I came to some interesting conclusions about Go in general.  
And maybe some of you will find it interesting too.

## General concept

As usual, I tried to get a grasp of the overall concept.
Since most of my experience was with PHP, I expected to see something similar to php.net.

But the logic of go.dev turned out to be different. It requires an understanding of how Go works, or, you could say, it reflects how Go works.

If you visit the [official website](https://go.dev/), you can see that its navigation leads both to its own pages and to other resources.

It looks something like this:
 
- [Why Go](#)
    - [Case Studies](https://go.dev/solutions/case-studies)  
    - [Use Cases](https://go.dev/solutions/use-cases)  
    - [Security](https://go.dev/security/)
- [Learn](https://go.dev/learn/)
- [Docs](https://go.dev/learn/#)
    - [Go Spec](https://go.dev/ref/spec)
    - [Go User Manual](https://go.dev/doc)
    - [Standard library](https://pkg.go.dev/std)
    - [Release Notes](https://go.dev/doc/devel/release)
    - [Effective Go](https://go.dev/doc/effective_go)
- [Packages](https://pkg.go.dev/)
- [Community](https://go.dev/learn/#)
    - [Recorded Talks](https://go.dev/talks/)
    - [Meetups](https://www.meetup.com/pro/go)
    - [Conferences](https://go.dev/wiki/Conferences)
    - [Go blog](https://go.dev/blog)
    - [Go project](https://go.dev/help)

Here we see the top-level sections "Why Go", "Learning", "Documentation", "Packages" and "Community". There is also a set of nested addresses in the sections.  
In general, after visually inspecting the navigation links and links on the internal pages, I didn't immediately understand the point of it all (there are links to go.dev, pkg.go.dev, and third-party resources such as meetup.com), but after digging a little deeper, I came to the following conclusions:

1. The specification at https://go.dev/ref/spec is a complete description of the language. That is, it is the entire Go language, outside the context of its application.

The specification describes the entire Go language from start to finish.  
That is:

- syntax
- keywords
- expressions
- types
- interfaces
- structs
- generics
- packages and imports
- scopes
- execution rules
- memory model
- ...

And we can draw the general conclusion that:  
**📌 If something is not in the specification, it is not in the language.**

It is also interesting that Go has only a small set of built-in functions, which are placed in a pseudo-package. `builtin`.  
Their description can be found at https://pkg.go.dev/builtin.  
But they are also described in the specification, in the section `Built-in functions`.

This package does NOT need to be imported — that is, it logically belongs to the language, not to the standard library.

This completely closes the question of language, as we have determined its source of truth. 

2. Next, there is the page https://pkg.go.dev/, which allows you to search all public packages in the Go ecosystem.  
Anyone can be a developer of these packages.  
The search results will be links to the page with the package description.

In other words, you can see what types, functions, methods, and constants it will add to the application if imported.
In other words, these are semantically unified sets of types, functions, methods, and constants created according to the rules of the specification (language) and available for import.

In other words, we can draw the following general conclusion:  
**📌 `pkg.go.dev` searches all public packages in the Go ecosystem, including the standard library.**

3. There is a page https://pkg.go.dev/std — it is the same as the previous section, but here only those packages are presented that are developed by `Go team`.  
They all come out of the box and are always available. The versions of these packages are tightly linked to the Go version. That is, unlike external packages, their versions are not selected separately.

In other words, we can draw the general conclusion that:  
**📌 `pkg.go.dev/std` — is a filter or a slice of the same catalog that shows only standard packages.**

4. The next important layer is the documentation for the Go Toolchain (which is available via the binary `go`).  
It is accessible via a call `go help` or at the address https://pkg.go.dev/cmd/go.  
I mentioned this in the last video when I was analyzing the Go toolchain.

From all of the above, we can summarize:

Go = Language + Packages + Tooling

Language:
  - fully described in the [Go Spec](https://go.dev/ref/spec) (https://pkg.go.dev/builtin);
  - includes a small set of functions and types exposed via the pseudo-package `builtin`;

Packages:
  - [standard library](https://pkg.go.dev/std);
  - [third-party packages](https://pkg.go.dev).

Tooling (a separate layer, not part of the language) ([docs](https://pkg.go.dev/cmd/go)):
  - `go build`;
  - `go test`;
  - `go mod`;
  - ...

Go is a combination of the language, packages written in it, and the developer's toolchain.  
And the core of Go documentation is the language description in the specification at `go.dev/ref/spec` + a separate page with a description of the pseudo-package `builtin` at `pkg.go.dev/builtin`.  
Next is the page describing the standard library packages at `pkg.go.dev/std`.  
The package search page for the entire Go ecosystem is located at `pkg.go.dev`.  
And also the page with documentation for the Go toolchain — at `pkg.go.dev/cmd/go`.

The rest is tutorials, best practices, links to authoritative resources, etc.  
That is, these are also important, but not primary.

Для полной картины нам остаётся разобраться с тем, как читать описание пакетов из коллекции std и любых сторонних.  
Далее определить, есть ли разница в логике описания.  
И в завершение сделать небольшую заметку о том, как работать с документацией из терминала и посмотреть несколько полезных ссылок.

## How to read package descriptions

Let's take it step by step.  
And let's start with "how to read package descriptions".

For example, I will take:

- standard package `fmt` — as simple and familiar as possible;
- third-party package `github.com/pkg/errors` — small, without overload.

In my experience,  
any page in the package at `pkg.go.dev` has the same structure:

- **Overview** — general overview of the package. Something like a comment on the package, describing its general purpose and sometimes key concepts or limitations. This is the only place where the package author explains why the package exists.  
  No other section does this.
- **Index** — this is the table of contents of the exported entities of the package, namely: `Constants`, `Variables`, `Functions`, `Types`. An index is not a description, but an API map.
- **Examples** — not always filled in, but if present, it contains examples of use.
- **Constants** — detailed description only of the constants listed in the Index.
- **Variables** — detailed description only of variables listed in Index.
- **Functions** — detailed description only of the functions listed in the Index.
- **Types** — detailed description of types (`struct`, `interface`, `type alias`, `defined types`), listed in the Index, and their methods (methods in Go are declared not for values or variables, but for specific types — `defined types`, but more on that another time).
- **Source Files** — files with the package source code.

When working with external packages, there is a slight UX difference. Since an external package has additional entities of type `README`, `LICENSE`, `CHANGELOG`, then you need to click on **Documentation** the left sidebar — this is a transition to the API reference, which will correspond to the structure described above.

## Documentation in the terminal

Now let's see how to work with documentation from the terminal.  
I am just mentioning this as an addition.

Go allows you to get documentation directly from the terminal using the command `go doc`.

For example, you can get a brief description of the standard package using the command:

```bash
go doc -short fmt
```

You can also view the description of the third-party developer's package (if it has already been downloaded locally):

```bash
go doc -short github.com/go-mysql-org/go-mysql/replication
```
I don't have it downloaded.  
Therefore, if I execute the command, it will return an error.

Alternatively, you can get a full description of the package by replacing `short` by `all`:
```bash
go doc -all github.com/go-mysql-org/go-mysql/replication  
```

Or

```bash
go doc -all fmt
```

Go also allows you to display a description, for example, of a specific function included in the package. To do this, simply specify its name after the package name, separated by a period:

```bash
go doc fmt.Append
```

Please note that all features can be found at pkg.go.dev/cmd/go.

## Useful links

Finally, I will add a few links from the official website that I found interesting, and perhaps you will also find something interesting there.

1. [Go Web Examples](https://gowebexamples.com/) provides easy-to-understand code snippets demonstrating how to use Go for web development.
2. A [master class](https://spf13.com/presentation/building-an-awesome-cli-app-in-go-oscon/) that introduces building CLI applications in Go, as well as the core principles of Go and CLI frameworks.
3. [Go by Example](https://gobyexample.com/) is a hands-on introduction to the Go programming language using annotated example programs.
4. [Tips for writing clear, efficient, and idiomatic Go code](https://go.dev/doc/effective_go)

I only gave these pages a quick look, of course, but they seemed interesting to me.

That's all. I consider this topic closed. If you found the video interesting, don't forget to subscribe and like it. Thank you all for watching, and see you next time.