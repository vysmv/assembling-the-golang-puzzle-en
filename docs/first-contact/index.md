# Learning Go. Part 1: Installation, go toolchain, modules, packages, and versions.

## Introduction

Hello, everyone.

In this video, I want to share with you my experience in learning Go.
Go - is a new language for me.
That means this playlist will include videos in which I simply **recorded** my journey in learning Go.

In this process, the first thing I try to do is get a general idea of the technology.
I don't like teaching a "spherical language in a vacuum", starting with syntax or going down the path of pure practice without any systematization.

Although I don't think it's a bad method. It just doesn't suit me.

I like it when I have an understanding of the laws of this world, and then, I study their practical application, delving into the nuances of syntax and industry-accepted patterns.

That is, first I form a general picture, and then I consider language as a set of rules for writing code.
If you find this format and the topic itself interesting, don't forget to like and subscribe to the channel.
Now let's get down to business.

Go was developed in 2007 within Google, and its first public version was released in 2012.
The main goal of the developers was to create a language that combines:

- simplicity and readability of code,
- high performance and efficiency,
- built-in support for concurrency,
- and an integrated toolchain that provides uniform handling of compilation, dependencies, formatting, and testing.

## GO installation

Well, let's install Go and see what we get. To do this, go to the [official website](https://go.dev/dl/).

Just in case, I'll remind you that all links, command examples, and code will be in the text version of the video on GitHub.
The link will be in the pinned comment below the video.

Next, we will download the archive for our operating system.

<p align="center">
  <img src="img/1.png" alt="download Go">
</p>

Since I have Linux, and I am doing this part of the demonstration on VirtualBox, I will simply copy the link to the archive and download it manually via the terminal:

```bash
wget https://go.dev/dl/go1.25.5.linux-amd64.tar.gz
```

Next, I will simply unpack this archive into the directory `/usr/local`:

```bash
sudo tar -C /usr/local -xzf go1.25.5.linux-amd64.tar.gz
```

The next thing we need to do is add the path to the directory with the Go binary to the PATH variable.
To do this, we need to edit the bash file `micro ~/.bashrc`.
It is located in the home directory.
Open the file for editing, scroll down and insert the line:

```bash
export PATH=$PATH:/usr/local/go/bin
```

Save the file and refresh the session by simply executing the SU command and passing the value of the user variable:

```bash
su $USER
```

Then execute the command go version.

```bash
go version
```

Great, go is installed.

If you install on Windows, there will be a wizard installer, and you can proceed as usual—next, next.

## Introduction

Now that we have the ability to run Go binary files, I would like to clarify this issue.

Many people refer to this binary file as a compiler,
but that is not entirely accurate.

### Go Toolchain

To be more precise, the `go` binary - is a command shell or interface to the entire set of Go developer tools, or the `Go Toolchain`.

This reflects the philosophy of the language: all of the developer's basic tools are provided through a single common interface and are available out of the box.

If, for example, we look at the world of PHP developers, there is:

- a separate interpreter,
- a separate Composer package manager, separate packages for testing and analyzing code that require Composer and have their own dependencies.

In other words, `PHP Toolchain` does not have a common management interface and is developed by different teams, so the approach adopted in GO can be safely considered one of its advantages.

Let's try to structure the Go Toolchain a little, and organize all the tools.

For example, we can build a hierarchy of commands in GO.
Let's paste it into the terminal and see:

```bash
go  
├─ Basic operations with code  
│  ├─ build  
│  ├─ run  
│  ├─ test  
│  ├─ install  
│  ├─ list  
│  ├─ clean  
│  └─ doc  
│  
├─ Working with modules  
│  ├─ mod  
│  └─ get  
│  
├─ Formatting and generation  
│  ├─ fmt  
│  └─ generate  
│  
├─ Analysis and quality  
│  ├─ vet  
│  └─ fix  
│  
├─ Tools and debugging  
│  ├─ tool  
│  └─ telemetry  
│  
└─ Service commands  
   ├─ bug  
   ├─ env  
   ├─ version  
   └─ work
```

Here we see that after calling the Go binary itself, we can pass it various commands.
For myself, I divided them into the following groups:

- Basic operations with code
- Working with modules
- Formatting and generation
- Analysis and quality
- Tools and debugging
- Service commands

In other words, we have a shell or interface layer expressed in a set of commands, and we have real utilities that are called under the hood when these commands are executed.

For example, a programmer is provided with the command interface `go build -o result_file_name` under the hood, real utilities located in the `ls -la /usr/local/go/pkg/tool/linux_amd64/` path will be called, for example,

- compile,
- link,
- and so on.

All these utilities are included in the archive provided by the official website. I am talking about the archive that I downloaded to install Go at the beginning of the video.

In other words, the main point of all the above - is that the archive contains a set of basic tools and a GO binary, which is an interface to them.

To see all the commands available through the `go` interface, use:

```go
go help
```

To see the description of a specific command, such as `build`, you can run:

```go
go help build
```

Alternatively, you can find descriptions of the commands on the [official website](https://pkg.go.dev/cmd/go).

In general, I think we have achieved a basic understanding of this issue and can move on.

### Main project entities

The next step was to look at Go from the perspective of an average abstract project.

And in the process, I formed the following picture.

The module acted as a kind of whole unit. And since this is the theoretical part, I will move on to the slides.

#### About modules

(Slide #1)

A module is a unit of build versioning and dependency management.

Now let's try to understand the essence of this definition.

First theoretically, then practically.

(Slide #2)
In general, this is a directory with a Go mod file that stores module metadata, such as its name and dependencies.

The module includes all subdirectories from the go mod file to the first other go mod file encountered.

In fact, a project can consist of more than one module. In this case, the project will contain several directories with separate go mod files.
However, the more common option is one project = one module.

Although when developing a public project, if a second version appears, you may need to create a new directory with a go mod file. We will see this in practice.

The module must have a name. It is formed according to the following condition:

(Slide #3)

- If the module is published for external use →  you need a URL accessible for go get, for example, (`GitHub, Bitbucket, Gitlab` or something like `internal.company.local/myservice`).

(Slide #4)

- But if the module is local → any name is acceptable.

That is, at the start, we must answer the question: "Will another go project ever import this module?"

If:

❌ No → you can write any name: crm, myapp, service.
✔️ Yes → use a URL path such as `github.com/user/service`

Now let's touch on the concept of an application. A module can include one or more applications.

#### About applications

Applications, in turn, consist of packages.
At least one - is main or main + a set of packages from our module or third parties.
Applications become separate binaries, i.e., a separate binary is = to a separate application.

Let's visualize this a little:

(Slide #5)

```.
├── go.mod
├── go.sum
├── cmd/
│   ├── api/      (application 1)
│   └── worker/   (application 2)
└── internal/
```

Here we see a directory containing a file called `go.mod`.
This is a module.
It contains a directory called `cmd`, which contains two applications: api and worker.
Each of these applications will be compiled into a separate binary.
In other words, each of them is a separate `main` package with its own starting point in the form of a `main` function.

#### About imports and their rules

Now let's talk about importing packages and the rules that govern how it works.

##### About importing public packages

(Slide #6)
When it comes to external public packages, it is the package itself that is imported, but the module acts as a dependency.
In other words, the key point to understand is that importing always works at the package level, while downloading works - at the module level.

(Slide #7)
This means that when we write in the code:

```go
import "github.com/user/project/utils"
```

(Slide #8)
And then execute the command:

```bash
go mod tidy
```

We are saying that we want to import the package `utils`, but in fact, go will install it as a dependency and download the entire module from `github.com/user/project`.

##### About importing local packages

Let me remind you that this was a look at working with imports in relation to public external packages.

If we need to import our own packages from our local project, then the import is simply done through the module name.

(Slide #9)
For example, if we have a `crm` module:

```go
module crm
```

Then importing an internal package is done as follows:

```go
import "crm/service"
```

##### About versions and pseudo-versions

Now let's talk about versions.
When importing public packages, we often encounter situations where there are several versions available.
And we need to understand the rules governing this process.
This is necessary at least in order to import external packages and either specify the version we need ourselves or understand which one will be pulled in by default.
And, of course, this is necessary for those cases when we ourselves are creating a public project and plan to have versions for it.

(Slide #10)
So, when we DON'T specify which version we need, i.e., after the keyword import comes `github.com/user/project/utils` but there is no `V2`or `V3`in the path,`go mod tidy` will proceed with one of the following options:

(Slide #11)

1) If V1 of the imported module exists → the latest available version from the first version (v1.x.x) range is downloaded.
2) If V1 is not available but V0 is →  the latest version from the zero version (v0.x.x) range is downloaded.
3) If neither V0 nor V1 is available → a pseudo-version is downloaded.

In this particular example, the pseudo-version will be a snapshot of the last commit of the default branch. Usually, this is main or master, marked with a date and hash as the version.

(Slide #12)
That is, the formed dependency will look like this:

```go
require github.com/user/proj v0.0.0-<YYYYMMDDHHMMSS>-<commit_hash>
```

Module name, zero version, date, and commit hash.

(Slide #13)
As an addition, I will say that a pseudo-version may appear even when the external module already has versions, but when executing   go get  , the branch name or commit was specified instead of the version tag.

```go
go get github.com/user/project@dev
```

In this case, the pseudo-version will point to the last commit of the dev branch.
We will also see this in practice.

So, if we return to our three-point rule and illustrate it in more detail, we get the following picture.

(Slide #14)
Let's say that an external module has the following list of versions:

- 0.1.14
- 0.5.99
- 0.99.99
- 1.1.12
- 1.5.0
- 1.9.99
- 2.1.22

When specifying an import without a version type V2, version 1.9.99 will be downloaded and installed as a dependency, namely the latest version within the first one.

But what if we are working with a module that has many versions and we need a specific one, for example, 2.2.33 ?

(Slide #15)
In this case, you first need to run go get in the following format.:

```bash
go get github.com/user/proj/v2@v2.2.33
```

(Slide #16)
This will result in a dependency in the go.mod file of the form:

```bash
require github.com/user/proj/v2 v2.2.33
```

(Slide #17)
Then we can simply use the import:

```go
import "github.com/user/proj/v2/utils"
```

I will pause here for a moment and draw your attention to the fact that we have now introduced a new way of managing dependencies.
This is `go get`.

That is, instead of the sequence described at the beginning of the conversation about importing and its rules, where first you import the package into the code and then execute `go get tidy`, you can simply take a different approach.

(Slide #18)
First, get the module by executing go get and the module address:

```bash
go get github.com/user/project
```

This will download the module according to the three rules described above.

(Slide 319)
And add a go mod file dependency of the type shown on this slide:

```bash
require github.com/user/project v1.X.X
```

or

```bash
require github.com/user/project v0.X.X
```

or

```go
require github.com/user/proj v0.0.0-<YYYYMMDDHHMMSS>-<commit_hash>
```

(Slide #20)
After that, we can import the package from the previously obtained module into the code.

```go
import "github.com/user/project/utils"
```

In other words, it's the same thing, but we install the packages ourselves, rather than the `go mod tidy` command, which simply refers to the existing imports in the code.

The main thing to understand here is that these two approaches have the same rules for working with imports.

Последнее, что добавлю, — это то, что модули при выполнении:
The last thing I would like to add is that when executing:

```bash
go get
go mod tidy
```

The modules will be located in the path stored in the GOMODCACHE variable.
We will see this in the practical section.

Perhaps I will delve deeper into this topic in the future, as there are still a number of interesting questions, but I deliberately chose not to explore them further.
It seems that they can wait a little longer.
So now let's move on to the practical part.

### Practice

But I will begin this practical part with a summary and the resulting project structure.

So, let's summarize:

1. We installed GO.
2. We understood that the Go binary - is an interface over a set of utilities, and all of this comes out of the box.
3. We looked at it from the perspective of a typical project. From this, it became clear, that:

   - we have a module (this is essentially a directory with a go mod file);
   - module can contain one or more applications;
   - each application is a separate main package, from which a separate binary is obtained.
4. We understand that a module can have a name and that there are rules for forming it.
5. Here I touched on imports.
   At first glance, this transition seems a little abrupt, as if it were out of context.
   Well, the thing is, while trying to understand the logic behind packages and modules, I realized that this issue needs to be addressed comprehensively.
   Let's take another look at the whole picture.

   We have a module with an application in it. This is the main package. The main package can import other packages. That is, in turn, they can also import other packages.

   Packages intended for import can be part of our module and reside in directories such as internal or pkg, or they can be pulled from the network.

   It is also important to understand that when we import an external package, Go downloads its entire module to disk, specifically to the directory - specified in the GOMODCACHE variable.

   It is important to note that packages may have versions.
   Which generates certain rules governing the import of external and local packages and determines how our module will be structured if we are developing, for example, a library that will have versions.

That is precisely how I came to the conclusion that this is the understanding that is needed here.

Now let's structure the practice.

For visualization, I will insert block headers into the terminal.

Block 1. One project = one module + several applications
Block 2. Importing at the package level, downloading at the module level
Block 3. How Go chooses a version if we don't explicitly specify it
Block 4. Pseudo-versions: when importing the dev branch
Block 5. Installing a specific version

So, in the first block, we will see how one project is equal to one module and how it can contain several applications.
The second block will demonstrate that we import a package in the code, but the entire module is actually pulled in.
In the third block, we will see how Go behaves when we import packages without specifying the version we need, and we will touch on the additional concept of pseudo-versions.
In the fourth block, as an addition, we will see how pseudo-versions can appear if we import by branch rather than by tag.
And in the last, fifth block, we will see how to import a specific version from the list of available ones.

Okay, let's move on to the implementation.

#### Block 1. One project = one module + several applications

Our first goal will be to experience the module as a project unit and individual binaries as an application.
There will be no logic in the functionality of this project, only calls to dummy functions to see that the scheme works as we expect.
We currently only have an empty first-contact directory.

This is the conditional directory for our project.
To create the project structure, I will run a bash script.

Essentially, I just scripted what I could have done manually throughout the explanation.
In other words, I just took a slightly different approach.
I will show the script or fragments of it and explain what it does, and then - we will run it and analyze the result.

In general, let's create a file called `block1.bash`
and paste the code into it.

If you look at the code we just pasted, the first thing I do is:

```bash
#initialize the module with a regular local name
go mod init first-contact
#create a set of directories
mkdir -p cmd/api
mkdir -p cmd/worker
mkdir -p internal/service
```

What I am doing now is not a canonical pattern for GO project architecture, although such a pattern does exist, but I think it is worth discussing as a separate topic.
I just take fragments that fit my demonstration.

For example, the cmd directory as I create it in the script is a pattern accepted in the community, but not mandatory.
The internal directory, on the other hand, has special functionality. Packages located in it are only accessible within our module, or more precisely, within the parent directory and its subdirectories, but are closed to the outside world.
The name of the Internal directory is a reserved mechanism and has a special meaning.

```bash
#create a service file and write the code there
cat << 'EOF' > internal/service/service.go
package service

import "fmt"

func SayHello(name string) {
    fmt.Printf("Hello, %s!\n", name)
}
EOF
```

We can see here that the main file of the package has the same name as the package directory.

And in the code itself, we see the package name, the import of the package from the standard Go library, and the function. But there is no main function here.

The next step is to create the file cmd/api/main.go and also write the code there:

```bash
cat << 'EOF' > cmd/api/main.go
package main

import (
    "first-contact/internal/service"
)

func main() {
    service.SayHello("from API")
}
EOF
```

Here we create a main package for the api application and import the service package from our module.
In the body of the main function, we simply call the function from the service package.

Then we create a second application in the cmd/worker/main.go and write the code there as well.

```bash
cat << 'EOF' > cmd/worker/main.go
package main

import (
    "first-contact/internal/service"
)

func main() {
    service.SayHello("from Worker")
}
EOF
```

In the code, we create a main package for the worker application and import the service package from our module into the body of the main function.
We also call a function from the service package.

The next step is to build these applications.
That is, we first sketched out the structure, and then immediately built it into a binary.
To do this, we execute the following three commands:

```bash
mkdir -p bin
go build -o bin/api ./cmd/api
go build -o bin/worker ./cmd/worker
```

Let's run this script.
To do this, execute bash block1.bash in the terminal `bash block1.bash`.

After executing the script, you can see that the entire structure described earlier has been created.

That is, we have two directories with entry points into the application code (api and worker) and one internal service package.
There is also a go mod file.

If we also look at the bin directory, we will see that we now have two separate binaries.

Each one - is a separate application in its final form.

And now, of course, we can run these applications in the terminal, for example, by simply executing the call at `./bin/api`.

Let's run this binary and see the string 'Hello from API' as a result.
This means that everything worked correctly. In other words, we have achieved the goal set for the first block.
Let's move on.

Final version of the script:

```bash
#!/usr/bin/env bash

set -e

# 1. Initialize the module
go mod init first-contact

# 2. Create directories
mkdir -p cmd/api
mkdir -p cmd/worker
mkdir -p internal/service

# 3. Create the file internal/service/service.go
cat << 'EOF' > internal/service/service.go
package service

import "fmt"

func SayHello(name string) {
    fmt.Printf("Hello, %s!\n", name)
}
EOF

# 4. Create the file cmd/api/main.go
cat << 'EOF' > cmd/api/main.go
package main

import (
    "first-contact/internal/service"
)

func main() {
    service.SayHello("from API")
}
EOF

# 5. Create the file cmd/worker/main.go
cat << 'EOF' > cmd/worker/main.go
package main

import (
    "first-contact/internal/service"
)

func main() {
    service.SayHello("from Worker")
}
EOF

# 6. Build the applications
mkdir -p bin
go build -o bin/api ./cmd/api
go build -o bin/worker ./cmd/worker

echo "Done! The files are created and compiled."
```

#### Block 2. Importing at the package level, downloading at the module level

The goal of the next block will be to show that we import a package in the code, but the entire module is downloaded.

To do this, we need to add a simple external import to the already created module (for example, in the api application).

Let's open the file first-contact/cmd/api/main.go and add the import:

```go
"github.com/google/uuid"
```

And then we will do it:

```bash
go mod tidy
```

This will result in a dependency appearing in the go.mod file:

```go
require github.com/google/uuid v1.6.0
```

But, as we already know from the theoretical section, to see where the modules downloaded according to the dependency are physically located, you can run:

```bash
go env GOMODCACHE
```

In my case, it is `/home/vysmv/go/pkg/mod`

To find out exactly where the module is physically located, execute the following command:

```bash
go list -m -json github.com/google/uuid
```

And let's take the path from the Dir field and run ls:

```bash
ls -la /home/vysmv/go/pkg/mod/github.com/google/uuid@v1.6.0
```

Then we will see all the module files.

This means that the block's goal has been achieved: we saw that we are importing a package in the code, but the entire module is downloaded.

This means we can move on.

#### Block 3. How Go chooses a version if we don't explicitly specify it


Here, our goal will be to demonstrate the rules for selecting a version (v1 → v0 → pseudo-version).

To begin with, next to our current module, I will create a directory called dvm, which stands for “demo version of the module,” and open it in VS Code:

```bash
mkdir ~/dvm && code ~/dvm
```

Now we have our locally developed first-contact module and a demo version of the public dvm module.

We will import its package into our tests.

In this block, I will also follow the path of executing a script, but in a slightly more sophisticated way.

The demonstration of this block consists of six steps, and the script will ask us if we want to continue to the next step.

That is, we launched and analyzed the first step, pressed `y` in the terminal to continue, performed the next step, and so on.

Let's create a file `block3.bash`
and insert the code there.

The logic here will be as follows:

We have an ask function that checks whether I want to continue.

Next is the local_clear function, but more on that later.

There is code for each step.

And if you scroll down, you will see each step being called via the ask function.

Now let's move on to analyzing the bash script, running it, and analyzing the results after each step.

**Step one: Create the module structure and push it to Github.**

Первым делом я инициализирую модуль:

```bash
go mod init github.com/vysmv/dvm
```

Next, let's create a package called utils, and inside it, a file called utils.go, and paste the following code into it:

```bash
mkdir -p ~/dvm/utils
cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}
EOF
```

In the code, we simply declare the utils package,

import fmt, and declare the Hello function.

Since we will have examples with versions, I created a repository for the dvm project:

https://github.com/vysmv/dvm

Therefore, I will execute the following commands to initialize the repository and push the current structure:

```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@personal_gh:vysmv/dvm.git
git push -u origin main
```

All commands here are standard, except for the line `git remote add origin git@personal_gh:vysmv/dvm.git`.

Normally, `github.com` is used instead of `personal_gh`.

This is because I set up GitHub to work with two accounts and specifically configured the process.

If you would like to see how I set it up, I will add a link to this video in the pinned comment.

And since this is not related to the main topic, let's move on.

Run the script and enter your password (twice).

Let's see what we've got.
We have a dvm module with one utils package, and it's pushed to GitHub.
Let's check it out by refreshing the repository page https://github.com/vysmv/dvm.
If we look at the go.mod file, the module name will be listed as a URL path.
This means it can be accessed from outside.

**Step two: import the package when there are no versions of the external dvm**

Here, I will add an import to the first-contact module's api application.
To do this, I overwrite the file ~/first-contact/cmd/api/main.go with:

```bash
cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/utils" ####new line
)

func main() {
    service.SayHello("from API")
}
EOF
```

Essentially, I'm just adding a new line to import our utils package from the dvm module.

After that, I'll execute the following set of commands:

```bash
export GOPROXY=direct
rm -rf ~/go/pkg/mod/cache/download/github.com/vysmv/dvm
cd ~/first-contact
go clean -modcache
export GOPRIVATE=github.com/vysmv/*
go mod tidy
```

Here, I set the value of the GOPROXY=direct variable so that modules are downloaded directly from GitHub rather than through a proxy.

I also delete everything from this directory `~/go/pkg/mod/cache/download/github.com/vysmv/dvm`, as I was experimenting before writing this and want to remove the artifacts.

I run `go clean -modcache` for the same purpose.

The variable GOPRIVATE with the value `github.com/vysmv/*` is needed to avoid conflicts with version hash sums during experiments.

Since you may create a repository and a set of the same tags several times during testing, this would lead to conflicts without this export.

Performing exports in the terminal only changes the parameters for the duration of the terminal session and will not break anything in the future.

So, for the duration of the tests, this is an acceptable solution.

Finally, I call `go mod tidy` to pull in the dependency we specified earlier in the import.

Great, now we can enter `y` to run the second step.

After that, we should see a dependency with a pseudo-version like this appear in the go.mod file of the first-contact module:

```go
require github.com/vysmv/dvm v0.0.0-20251206092652-0f827210d4d2
```

So, we have confirmed that if an external module does not have versions such as v1 or v2, we will get a pseudo-version.

Okay, that's it, we can move on, and next we have...

**Step three: import the package when the module has a version from the zero range**

To do this, we will update dvm to version 0.1.12 and re-import the package from dvm into the first-contact module.

But first, before each new action, I will run the `local_clear` function.

When I first looked at the script, I said I would mention it later.

Let's take a look at it.

This simply copies the line with the import in the main.go file in the first-contact/cmd/api/ directory, then deletes it, runs `go mod tidy`, and returns the line with the dependency.

This is necessary so that the previous dependency is deleted before each new launch.

In other words, a cleanup occurs. Otherwise, because it is already installed, some tests will not work as we expect, since the import and dependency already exist.

I will not explain the meaning of this function further, but simply call `local_clear`.

Next... let's add a new function (just overwrite the entire file) dvm/utils/utils.go:

```bash
cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {                      ### [new function]
    fmt.Println("Hello from utils!")
}
EOF
```

Essentially, we will simply add the NewFuncV0 function.

After that, we will execute commands that will create a new tag and push the changes:

```bash
cd ~/dvm
git add .
git commit -m "added new func for V0"
git push
git tag v0.1.12
git push --tags
```

Next, run `go mod tidy` in the first-contact module.

```bash
cd ~/first-contact
go mod tidy
```

Okay, let's do the third step and type `y` again. Then, type the password three times.

If we now look at the go.mod file for the first-contact module,

it will already show version 0.1.12:

```go
require github.com/vysmv/dvm v0.1.12
```

In general, our expectations have been confirmed, and we can move forward.

**Step 4: Import the package when the module has a version from the first range.**

The next step will be the fourth one, where we import the package when the module has a version from the first range.

Let's add version 1.0.0 to our dvm module.

To do this, we will overwrite the utils.go file in the /dvm/utils directory again:

```bash
cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() { #NEW FUNC
    fmt.Println("Hello from utils!")
}
EOF
```

Where, in fact, we will add another function. This time under the name NewFuncV1.

Then we will commit and push the changes.

Next, we will create a tag and push it.

```bash
# 2. git commit + tag v1.0.0
cd ~/dvm
git add .
git commit -m "added new func for V1"
git push
git tag v1.0.0
git push --tags
```

After that, we will execute:

```bash
local_clear

# 3. run go mod tidy
cd ~/first-contact
go mod tidy
```

Let's run the fourth step (you will need to enter your password several times here) and see the result.

In the go.mod file of the first-contact module, we will see the dependency of version 1.0.0.

```go
require github.com/vysmv/dvm v1.0.0
```

Okay, this test was also successful, meaning it went as expected.

So let's move on.

**Step 5: Create the second version of dvm and import the second version**

Next, we move on to step five.

Here, we create a second version of dvm and import the second version into first-contact.

First, we execute `local_clear`:

```bash
local_clear
```

Next, we create a new version, namely v2, for the dvm module.

To do this, we create a v2 directory in the root of the module.

In it, we initialize the module with a new name.

We write the code from the previous version of the package there, but change the package API.

That is, we change the name of one function.

```bash
# 1–5. create v2 module and utils.go
    mkdir -p ~/dvm/v2/utils

    cd ~/dvm/v2
    go mod init github.com/vysmv/dvm/v2

    cat <<EOF > ~/dvm/v2/utils/utils.go
package utils

import "fmt"

func HelloNewAPI() { #NEW API
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {  
    fmt.Println("Hello from utils!")
}
EOF
```

That is, we change the function name from Hello to HelloNewAPI.

Then we commit, push the changes, and tag v2 on the root of the repository:

```bash
cd ~/dvm
git add .
git commit -m "added v2"
git push
git tag v2.0.0
git push --tags
```

After that, update the file \~/first-contact/cmd/api/main.go:

```bash
cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/v2/utils" #NEW IMPORT
)

func main() {
    service.SayHello("from API")
}
EOF
```

That is, we install a new import github.com/vysmv/dvm/v2/utils.

And finally, we execute:

```bash
cd ~/first-contact
go mod tidy
```

And if we run this step... (here, as usual, you need to enter your passwords), we will see that a dependency on the second version has appeared in the go.mod module of the first-contact module.

```go
require github.com/vysmv/dvm/v2 v2.0.0
```

So, we have completed this step and seen how to create a second version and import it into the first-contact project.

This means we can continue.

**Step 6: Import without specifying the version from a multi-version module**

And now for the final, sixth step.

Here, we perform an import without specifying a version from a multi-version module.

Here we confirm that if the module has a version greater than the first range (i.e., second or higher) and there are zero and first versions, then the last one from the first range will be downloaded.

To do this, we first perform a cleanup.

```bash
local_clear
```

Then, update the main.go file in the `~/first-contact/cmd/api` directory so that the import does not specify a version, and run `go mod tidy` in the `~/first-contact` directory:

```bash
# 1. rewrite main.go back to v1 import
    cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/utils" #NEW IMPOTR
)

func main() {
    service.SayHello("from API")
}
EOF

# 2. run go mod tidy
cd ~/first-contact
go mod tidy
```

After that, let's run step six...

As a result, we will see the following in the go.mod file of the first-contact module:

```go
require github.com/vysmv/dvm v1.0.0
```

In other words, it worked according to the logic described in the three points.

This completes all six steps of the third block,

and we can move on to the fourth block.

The complete script code for the third block:

```bash
#!/usr/bin/env bash

set -e

ask() {
    read -p "$1 (y/n): " ans
    if [ "$ans" = "y" ]; then
        return 0
    else
        return 1
    fi
}

local_clear() {
    local file="$HOME/first-contact/cmd/api/main.go"
    local workdir="$HOME/first-contact"
    local saved_line

    # Read and save line 5
    saved_line="$(sed -n '5p' "$file")"

    # Remove line 5
    sed -i '5d' "$file"

    # Run go mod tidy in ~/first-contact
    cd "$workdir"
    go mod tidy

    # Restore the original line 5
    sed -i "5i\\$saved_line" "$file"
}

#######################################
# STEP 1
#######################################
step1() {
    echo "=== STEP 1 ==="
    cd ~/dvm

    # 1. go mod init
    go mod init github.com/vysmv/dvm

    # 2. create utils package
    mkdir -p ~/dvm/utils
    cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}
EOF

    # 3. initialize git
    git init
    git add .
    git commit -m "first commit"
    git branch -M main
    git remote add origin git@personal_gh:vysmv/dvm.git
    git push -u origin main
}

#######################################
# STEP 2
#######################################
step2() {
    echo "=== STEP 2 ==="

    # 1. rewrite main.go
    cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/utils"
)

func main() {
    service.SayHello("from API")
}
EOF

    # 2. Go proxy & tidy operations
    export GOPROXY=direct
    rm -rf ~/go/pkg/mod/cache/download/github.com/vysmv/dvm
    cd ~/first-contact
    go clean -modcache
    export GOPRIVATE=github.com/vysmv/*
    go mod tidy
}

#######################################
# STEP 3
#######################################
step3() {
    echo "=== STEP 3 ==="
    local_clear

    # 1. update utils.go
    cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}
EOF

    # 2. git commit + tag v0.1.12
    cd ~/dvm
    git add .
    git commit -m "added new func for V0"
    git push
    git tag v0.1.12
    git push --tags

    # 3. run go mod tidy inside first-contact
    cd ~/first-contact
    go mod tidy
}

#######################################
# STEP 4
#######################################
step4() {
    echo "=== STEP 4 ==="

    # 1. update utils.go for V1
    cat <<EOF > ~/dvm/utils/utils.go
package utils

import "fmt"

func Hello() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}
EOF

    # 2. git commit + tag v1.0.0
    cd ~/dvm
    git add .
    git commit -m "added new func for V1"
    git push
    git tag v1.0.0
    git push --tags

    local_clear
    # 3. run go mod tidy
    cd ~/first-contact
    go mod tidy
}

#######################################
# STEP 5
#######################################
step5() {
    echo "=== STEP 5 ==="
    local_clear

    # 1–5. create v2 module and utils.go
    mkdir -p ~/dvm/v2/utils

    cd ~/dvm/v2
    go mod init github.com/vysmv/dvm/v2

    cat <<EOF > ~/dvm/v2/utils/utils.go
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}
EOF

    # 6. git commit + tag v2.0.0
    cd ~/dvm
    git add .
    git commit -m "added v2"
    git push
    git tag v2.0.0
    git push --tags

    # 7. update first-contact main.go
    cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/v2/utils"
)

func main() {
    service.SayHello("from API")
}
EOF

    # 8. run go mod tidy
    cd ~/first-contact
    go mod tidy
}

#######################################
# STEP 6
#######################################
step6() {
    echo "=== STEP 6 ==="
    local_clear

    # 1. rewrite main.go back to v1 import
    cat <<EOF > ~/first-contact/cmd/api/main.go
package main

import (
    "first-contact/internal/service"
    "github.com/vysmv/dvm/utils"
)

func main() {
    service.SayHello("from API")
}
EOF

    # 2. run go mod tidy
    cd ~/first-contact
    go mod tidy
}

#######################################
# RUN STEPS
#######################################

if ask "Run step #1?"; then step1; fi
if ask "Run step #2?"; then step2; fi
if ask "Run step #3?"; then step3; fi
if ask "Run step #4?"; then step4; fi
if ask "Run step #5?"; then step5; fi
if ask "Run step #6?"; then step6; fi

echo "Done!"
```

#### Block 4. Pseudo-versions: when importing the dev branch

The goal of the fourth block will be to see how we get a pseudo-version if we execute something like `go get github.com/user/project@dev`.

That is, we will try to pull the module by branch name rather than by tag.

Let's also apply the script to bash here.
To do this, let's create a file `block4.bash` in the dvm project and paste the code there.

Let's take a look at the script.

First, we create a dev branch for dvm.

```bash
cd "$HOME/dvm"
git checkout -b dev
```

Then, we overwrite the utils.go file in `$HOME/dvm/v2/utils`, adding the `NewFuncForDev` function:

```bash
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() { #NEW FUNC
    fmt.Println("Hello from utils!")
}
EOF
```

Next, we will commit and push:

```bash
git add .
git commit -m "added func for dev"
git push --set-upstream origin dev || true
```

After that, we will clean up and update the import line in `~/first-contact/cmd/api/main.go`,

and then pull the module through `go get`, but on the branch:

```bash
local_clear

sed -i '5s|.*|    "github.com/vysmv/dvm/v2/utils"|' \
    "$HOME/first-contact/cmd/api/main.go"

cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@dev
```

That's it.

Let's run the script and enter the passwords.

As a result, the go.mod file of the first-contact module will contain a pseudo-version like this:

```go
require github.com/vysmv/dvm/v2 v2.0.1-0.20251206145623-862197a8af06
```

In other words, we have identified another scenario in which a pseudo-version may appear.

In summary, this section is complete, and we can proceed.

Full version of the script:

```bash
#!/usr/bin/env bash

set -e

local_clear() {
    local file="$HOME/first-contact/cmd/api/main.go"
    local workdir="$HOME/first-contact"
    local saved_line

    # Read and save line 5
    saved_line="$(sed -n '5p' "$file")"

    # Remove line 5
    sed -i '5d' "$file"

    # Run go mod tidy in ~/first-contact
    cd "$workdir"
    go mod tidy

    # Restore the original line 5
    sed -i "5i\\$saved_line" "$file"
}

#######################################
# DEV BRANCH UPDATE
#######################################

echo "=== CREATE AND UPDATE DEV BRANCH ==="

# 1. Checkout dev branch in ~/dvm
cd "$HOME/dvm"
git checkout -b dev

# 2. Rewrite utils.go for v2
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() {
    fmt.Println("Hello from utils!")
}
EOF

# 3. Commit and push
git add .
git commit -m "added func for dev"

# 4. Push new branch and set upstream
git push --set-upstream origin dev

# 5. Run local_clear
local_clear

# 5.1 Rewrite line 5 in main.go to v2 import
sed -i '5s|.*|    "github.com/vysmv/dvm/v2/utils"|' \
    "$HOME/first-contact/cmd/api/main.go"

# 6. Go get dev branch in ~/first-contact
cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@dev

echo "Done."
```

#### Block 5. Installing a specific version

The fifth block is installing a specific version.

Let's look at this situation.

Suppose dvm has versions v2.0.0, v2.2.33, and v2.5.66, and we want to understand how to install v2.2.33 specifically.

To do this, as usual, we will use a script.

Let's create a script called `block5.bash` and paste the code into it.

Let's see what we have here.

First, let's switch to the main branch:

```bash
cd "$HOME/dvm"
git switch main
```

Then we will add a new function to the file dvm/v2/utils/utils.go.

It will be called NewFuncV2\_2\_33.

```bash
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_2_33() { #NEW FUNC
    fmt.Println("Hello from utils!")
}
EOF
```

After that, we will commit, push, and create a new tag, and then push it.

```bash
git add .
git commit -m "added new func for v2.2.33"
git push
git tag v2.2.33
git push --tags
```

Then I do the same thing, but for another version (2.5.66).

I update the utils.go file for the second version, and then push the changes and the new tag.

```bash
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_2_33() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_5_66() { #NEW FUNC
    fmt.Println("Hello from utils!")
}
EOF

# 5. Commit, push, tag v2.5.66
git add .
git commit -m "added new func for v2.5.66"
git push
git tag v2.5.66
git push --tags
```

And finally, I will perform a cleanup and `go get` with a specific version.

```bash
local_clear
cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@v2.2.33
```

Let's run the script.

Enter the passwords...

As a result, we will see a dependency of the following type in the go.mod file of the first-contact module:

```go
require github.com/vysmv/dvm/v2 v2.2.33
```

And if we open the main.go file in the first-contact/cmd/api directory and look at the methods available in the utils package, we will see that the `NewFuncV2_2_33` function is available, but `NewFuncV2_5_66` is not.

Great. The goal of this block has also been achieved.

The final version of the script:

```bash
#!/usr/bin/env bash

set -e

local_clear() {
    local file="$HOME/first-contact/cmd/api/main.go"
    local workdir="$HOME/first-contact"
    local saved_line

    # Read and save line 5
    saved_line="$(sed -n '5p' "$file")"

    # Remove line 5
    sed -i '5d' "$file"

    # Run go mod tidy in ~/first-contact
    cd "$workdir"
    go mod tidy

    # Restore the original line 5
    sed -i "5i\\$saved_line" "$file"
}

#######################################
# MAIN BRANCH RELEASE FLOW
#######################################

echo "=== SWITCH TO MAIN ==="

# 1. Switch to main branch
cd "$HOME/dvm"
git switch main

#######################################
# v2.2.33
#######################################

echo "=== PREPARE v2.2.33 ==="

# 2. Rewrite utils.go for v2.2.33
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_2_33() {
    fmt.Println("Hello from utils!")
}
EOF

# 3. Commit, push, tag v2.2.33
git add .
git commit -m "added new func for v2.2.33"
git push
git tag v2.2.33
git push --tags

#######################################
# v2.5.66
#######################################

echo "=== PREPARE v2.5.66 ==="

# 4. Rewrite utils.go for v2.5.66
cat <<EOF > "$HOME/dvm/v2/utils/utils.go"
package utils

import "fmt"

func HelloNewAPI() {
    fmt.Println("Hello from utils!")
}

func NewFuncV0() {
    fmt.Println("Hello from utils!")
}

func NewFuncV1() {
    fmt.Println("Hello from utils!")
}

func NewFuncForDev() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_2_33() {
    fmt.Println("Hello from utils!")
}

func NewFuncV2_5_66() {
    fmt.Println("Hello from utils!")
}
EOF

# 5. Commit, push, tag v2.5.66
git add .
git commit -m "added new func for v2.5.66"
git push
git tag v2.5.66
git push --tags

#######################################
# CONSUMER UPDATE
#######################################

# 6. Run local_clear
local_clear

# 7. Get v2.2.33 in first-contact
cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@v2.2.33

echo "Done."
```

## Summary

So, we're done—all that's left is to record what we've achieved.

If we remove all the details, commands, and special cases, Go's modules and imports are built around the following idea.

A module is a unit of delivery and versioning.

Modules are downloaded, cached, and selected by version.

A package is a unit of compilation and import.

In code, we always work with packages, but behind the scenes, Go operates with modules.

An import is a logical dependency, and the go mod tidy and go get commands are mechanisms that bring the logical model into a consistent physical state on disk.

Versions are not just numbers, but a contract between the module author and its users.

Now we have everything we need to continue diving deeper and putting together the puzzle of this technology in our minds.

To be honest, I don't know yet what the next video will be about — I don't have a pre-prepared outline. This playlist is coming together naturally as I learn.

Anyway, if you found this video interesting, don't forget to like and subscribe.

Thanks for watching. Bye!
