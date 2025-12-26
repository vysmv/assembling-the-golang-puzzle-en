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
.
├── go.mod
├── go.sum
├── cmd/
│   ├── api/      (application 1)
│   └── worker/   (application 2)
└── internal/

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
So, when we DON'T specify which version we need, i.e., after the keyword import comes `github.com/user/project/utils` but there is no `V2`or`V3`in the path,`go mod tidy` will proceed with one of the following options:

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

#### Блок 2 Импорт на уровне пакетов, скачивание на уровне модулей

Целью следующего блока будет показать, что в коде мы импортируем пакет, а скачивается весь модуль.

Для этого в уже созданном модуле (например, в приложении api) нужно добавить простой внешний импорт.
Давайте откроем файл first-contact/cmd/api/main.go и добавим импорт:

```go
"github.com/google/uuid"
```

И выполним после этого:

```bash
go mod tidy
```

Это приведёт к тому, что в файле go.mod появится зависимость:

```go
require github.com/google/uuid v1.6.0
```

Но, как мы уже знаем из теоретического блока, чтобы посмотреть, где физически лежат скачанные по зависимости модули,
можно выполнить:

```bash
go env GOMODCACHE
```

В моем случае это `/home/vysmv/go/pkg/mod`

А чтобы точно узнать, где физически лежит модуль, выполним следующую команду:

```bash
go list -m -json github.com/google/uuid
```

И возьмём путь из поля Dir и выполним ls:

```bash
ls -la /home/vysmv/go/pkg/mod/github.com/google/uuid@v1.6.0
```

То увидим все файлы модуля.

То есть цель блока достигнута: мы увидели, что импортируем мы в коде пакет, но скачивается весь модуль.
Значит, можем идти дальше.

#### Блок 3. Как Go выбирает версию, если мы её явно не указываем в импорте

Тут нашей целью будет продемонстрировать правила выбора версии (v1 → v0 → псевдоверсия).

Для начала рядом с нашим текущим модулем я создам директорию dvm, то есть «демо версии модуля», и открою её в VS Code:

```bash
mkdir ~/dvm && code ~/dvm
```

Теперь у нас есть наш локально разрабатываемый модуль first-contact и демо-версия публичного модуля dvm.
Его пакет мы и будем импортировать в наших тестах.

В этом блоке я тоже пойду путём выполнения скрипта,
но чуть более хитро.

Демонстрация этого блока состоит из шести шагов, и скрипт будет спрашивать у нас, хотим ли мы продолжить следующий шаг.
То есть запустили и разобрали первый шаг и нажимаем в терминале `y`, чтобы продолжить, и выполняем следующий шаг и т. д.

Давайте создадим файл `block3.bash`
и вставим туда код.

Логика тут будет такая:
у нас есть функция ask, которая проверяет, хочу ли я продолжать.

Далее функция local_clear, но о ней позже.

Есть код каждого шага.

И если проскроллить вниз, то там будет вызов каждого шага через вызов функции ask.

А теперь перейдём к разбору bash-скрипта, запуску и последующему анализу результата после каждого шага.

**Первый шаг: Создадим структуру модуля и запушим ее на Github.**

Первым делом я инициализирую модуль:

```bash
go mod init github.com/vysmv/dvm
```

Далее создадим пакет utils, а в нём файл utils.go и вставим туда код:

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

В коде мы просто объявляем пакет utils,
импортируем fmt и объявляем функцию Hello.

Так как у нас будут примеры с версиями, то я создал для проекта dvm репозиторий:

https://github.com/vysmv/dvm

Поэтому выполню следующие команды для инициализации репозитория и запушу текущую структуру:

```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@personal_gh:vysmv/dvm.git
git push -u origin main
```

Тут все команды стандартные, кроме строки `git remote add origin git@personal_gh:vysmv/dvm.git`.
Стандартно вместо `personal_gh` идёт `github.com`.
Это потому, что я настраивал себе работу с GitHub на два аккаунта и специфически конфигурировал процесс.
Если вам хотелось бы посмотреть, как настраивал я, то ссылку на это видео добавлю в закреплённый комментарий.
И так как это не имеет отношения к основной теме, то идём дальше.

Запускаем скрипт и вводим пароль (два раза).

Давайте посмотрим, что получилось.
У нас есть модуль dvm с одним пакетом utils, и он запушен на GitHub.
Давайте это проверим, обновив страницу репозитория https://github.com/vysmv/dvm.
А если посмотреть файл go.mod, то имя модуля будет указано как URL-путь.
То есть он может быть доступен извне.

**Второй шаг: имортируем пакет когда у внешнего dvm нет версий**

Тут я добавлю импорт в приложение api модуля first-contact.
Для этого перезаписываю файл ~/first-contact/cmd/api/main.go на:

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

По сути, просто добавляю новую строку с импортом нашего пакета utils из модуля dvm.

И после этого выполню следующий набор команд:

```bash
export GOPROXY=direct
rm -rf ~/go/pkg/mod/cache/download/github.com/vysmv/dvm
cd ~/first-contact
go clean -modcache
export GOPRIVATE=github.com/vysmv/*
go mod tidy
```

Тут я устанавливаю значение переменной GOPROXY=direct, чтобы скачивание модулей происходило напрямую с GitHub, а не через прокси.
И удаляю всё из этой директории `~/go/pkg/mod/cache/download/github.com/vysmv/dvm`, так как до записи я экспериментировал и хочу удалить артефакты.
`go clean -modcache` я выполняю с той же целью.

А вот переменная GOPRIVATE со значением `github.com/vysmv/*` нужна для того, чтобы не возникало конфликтов с хэш-суммами версий на время экспериментов.
Так как вы можете несколько раз во время тестов создавать репозиторий и набор одних и тех же тегов, это будет приводить к конфликтам без этого экспорта.
Выполнение экспортов в терминале меняет параметры только на период сессии терминала
и ничего вам не сломает в будущем.
Так что на период тестов — приемлемое решение.

И в завершение вызываю `go mod tidy`, чтобы подтянуть зависимость, которую мы ранее указали в импорте.

Отлично, теперь можем ввести `y`, чтобы запустить выполнение второго шага.

После этого мы должны увидеть, что в файле go.mod модуля first-contact появится зависимость с псевдоверсией типа:

```go
require github.com/vysmv/dvm v0.0.0-20251206092652-0f827210d4d2
```

То есть мы подтвердили, что если у внешнего модуля нет версий типа v1 или v2, то мы получим псевдоверсию.

Хорошо, с этим всё, и можем идти дальше, а дальше у нас…

**Третий шаг: имортируем пакет когда у модуля есть версия из диапазона нулевых**

Для этого мы обновим dvm до версии 0.1.12 и выполним повторный импорт пакета из dvm в модуль first-contact.

Но сначала, перед каждым новым действием, я буду запускать функцию `local_clear`.
При первом осмотре скрипта я сказал, что упомяну о ней позже.
Давайте на неё посмотрим.

Это просто копирование строки с импортом в файле main.go в директории first-contact/cmd/api/, а потом её удаление, выполнение `go mod tidy` и возвращение строки с зависимостью назад.
Нужно это, чтобы перед каждым новым запуском у нас удалялась прошлая зависимость.
То есть происходила очистка. Иначе из-за того, что она уже установлена, некоторые тесты не отработают так, как мы ожидаем, так как импорт и зависимость уже имеются.
Дальше я не буду объяснять значение этой функции, а просто вызывать `local_clear`.

Далее… добавим новую функцию (просто перезапишем весь файл) dvm/utils/utils.go:

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

По сути, просто добавим функцию NewFuncV0.

А после этого выполним команды, которые создадут новый тег и запушат изменения:

```bash
cd ~/dvm
git add .
git commit -m "added new func for V0"
git push
git tag v0.1.12
git push --tags
```

Далее выполним `go mod tidy` в модуле first-contact.

```bash
cd ~/first-contact
go mod tidy
```

Хорошо, давайте выполним третий шаг и введём ещё раз `y`. А после введём три раза пароль.

Если теперь посмотрим на файл go.mod у модуля first-contact,
там уже будет версия 0.1.12:

```go
require github.com/vysmv/dvm v0.1.12
```

В общем, наши ожидания подтвердились, и можно идти дальше.

**Четвёртый шаг: импортируем пакет, когда у модуля есть версия из диапазона первых**

Следующим шагом будет четвёртый, где мы импортируем пакет, когда у модуля есть версия из диапазона первых.
Давайте добавим нашему модулю dvm версию 1.0.0.

Для этого выполним ещё одну перезапись файла utils.go в директории /dvm/utils:

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

Где, по сути, ещё раз добавим функцию. На этот раз под именем NewFuncV1.

А после сделаем commit и запушим изменения.
Потом создадим тег и запушим его.

```bash
# 2. git commit + tag v1.0.0
cd ~/dvm
git add .
git commit -m "added new func for V1"
git push
git tag v1.0.0
git push --tags
```

После чего выполним:

```bash
local_clear

# 3. run go mod tidy
cd ~/first-contact
go mod tidy
```

Давайте запустим четвёртый шаг (тут нужно ввести несколько раз пароль) и посмотрим на результат.

В файле go.mod модуля first-contact мы увидим зависимость версии 1.0.0.

```go
require github.com/vysmv/dvm v1.0.0
```

Хорошо, этот тест тоже успешный, то есть прошёл в соответствии с ожиданием.
А значит, идём дальше.

**Пятый шаг: создаём вторую версию dvm и импортируем вторую версию**

Дальше нас ждёт пятый шаг.
Тут создаём вторую версию dvm и импортируем вторую версию в first-contact.
Первым делом мы выполняем `local_clear`:

```bash
local_clear
```

И далее создаём новую версию, а именно v2, для модуля dvm.
Для этого в корне модуля создаём директорию v2.
В ней инициализируем модуль с новым именем.
И записываем туда код прошлой версии пакета, но меняем API пакета.
То есть изменяем имя одной функции.

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

То есть меняем имя функции с Hello на HelloNewAPI.

А после этого коммитим, пушим изменения и ставим тег v2 на корень репозитория:

```bash
cd ~/dvm
git add .
git commit -m "added v2"
git push
git tag v2.0.0
git push --tags
```

После этого обновляем файл ~/first-contact/cmd/api/main.go:

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

То есть устанавливаем новый импорт github.com/vysmv/dvm/v2/utils.

И в завершение выполняем:

```bash
cd ~/first-contact
go mod tidy
```

И если мы запустим этот шаг… (тут, как обычно, нужно вводить пароли), то увидим, что в go.mod модуля first-contact появилась зависимость на вторую версию.

```go
require github.com/vysmv/dvm/v2 v2.0.0
```

То есть мы выполнили задачу этого шага и увидели, как создать вторую версию и импортировали её в проект first-contact.
А значит, можем продолжить.

**Шестой шаг: выполняем импорт без указания версии из многоверсионного модуля**

И теперь последний, шестой шаг.
Тут мы выполняем импорт без указания версии из многоверсионного модуля.

Тут мы подтвердим, что если у модуля есть версия больше диапазона первых (то есть вторая или выше) и при этом есть нулевые и первые, то скачается последняя из диапазона первых.

Для этого первым делом выполним очистку.

```bash
local_clear
```

А потом обновим файл main.go в директории `~/first-contact/cmd/api` так, чтобы импорт был без указания версии, и выполним `go mod tidy` в директории `~/first-contact`:

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

После этого давайте запустим на исполнение шестой шаг…

Как результат, в файле go.mod модуля first-contact увидим:

```go
require github.com/vysmv/dvm v1.0.0
```

То есть это отработало в соответствии с описанной в трёх пунктах логикой.

На этом мы закончили все шесть шагов третьего блока
и можем переходить к четвёртому блоку.

Полный код скрипта для третьего блока:

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

#### Блок 4. Псевдоверсии: при импорте ветки dev

Целью четвёртого блока будет увидеть, как мы получаем псевдоверсию, если выполним что-то типа `go get github.com/user/project@dev`.
То есть попытаемся подтянуть модуль по имени ветки, а не по тегу.

Давайте тут также применим скрипт на bash.
Создадим для этого файл `block4.bash` в проекте dvm
и вставим туда код.

Посмотрим на скрипт.
Первым делом мы создаём ветку dev для dvm.

```bash
cd "$HOME/dvm"
git checkout -b dev
```

Потом перезаписываем файл utils.go в `$HOME/dvm/v2/utils`, добавив при этом функцию `NewFuncForDev`:

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

Далее сделаем коммит и запушим:

```bash
git add .
git commit -m "added func for dev"
git push --set-upstream origin dev || true
```

После этого выполним очистку и обновим строку импорта в `~/first-contact/cmd/api/main.go`,
а затем подтянем модуль через `go get`, но по ветке:

```bash
local_clear

sed -i '5s|.*|    "github.com/vysmv/dvm/v2/utils"|' \
    "$HOME/first-contact/cmd/api/main.go"

cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@dev
```

Это всё.
Давайте запустим скрипт на исполнение и введём пароли.

В итоге в файле go.mod модуля first-contact будет псевдоверсия такого вида:

```go
require github.com/vysmv/dvm/v2 v2.0.1-0.20251206145623-862197a8af06
```

То есть мы увидели ещё один путь, при котором может появиться псевдоверсия.
В общем, этот блок завершён, и можем идти дальше.

Полная версия скрипта:

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

#### Блок 5. Установка конкретной версии

Пятый блок — это установка конкретной версии.
Посмотрим на такую ситуацию. Допустим, у dvm есть версии v2.0.0, v2.2.33 и v2.5.66,
и мы хотим понять, как устанавливать конкретно v2.2.33.

Для этого, как обычно, воспользуемся скриптом.
Создадим скрипт block5.bash и вставим туда код.
Давайте посмотрим, что мы тут имеем.

Первое — переключимся на ветку main:

```bash
cd "$HOME/dvm"
git switch main
```

Потом добавим новую функцию в файл dvm/v2/utils/utils.go.
Это будет NewFuncV2_2_33.

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

После чего сделаем коммит, запушим и сделаем новый тег, а затем запушим его.

```bash
git add .
git commit -m "added new func for v2.2.33"
git push
git tag v2.2.33
git push --tags
```

А потом делаю то же самое, но для ещё одной версии (2.5.66).
Обновлю файл utils.go второй версии, а после запушу изменения и новый тег.

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

И в завершение выполню очистку и `go get` с конкретной версией.

```bash
local_clear
cd "$HOME/first-contact"
go get github.com/vysmv/dvm/v2@v2.2.33
```

Давайте запустим скрипт.
Введём пароли…

Как результат, мы увидим в файле go.mod модуля first-contact зависимость вида:

```go
require github.com/vysmv/dvm/v2 v2.2.33
```

И если мы откроем файл main.go в директории first-contact/cmd/api и там посмотрим на методы, доступные в пакете utils, то увидим,
что функция `NewFuncV2_2_33` доступна, а `NewFuncV2_5_66` — нет.
Отлично. Цель этого блока тоже достигнута.

Финальная версия скрипта:

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

## Резюме

Итак, мы полностью закончили — осталось лишь зафиксировать то, что у нас получилось.

Если убрать все детали, команды и частные случаи, то Go в плане модулей и импортов строится вокруг следующей идеи.

Модуль — это единица доставки и версионирования.
Именно модули скачиваются, кешируются и выбираются по версиям.

Пакет — это единица компиляции и импорта.
В коде мы всегда работаем с пакетами, но за кулисами Go оперирует модулями.

Импорт — это логическая зависимость,
а команды go mod tidy и go get — это механизмы, которые приводят логическую модель в согласованное физическое состояние на диске.

Версии при этом — это не просто номера, а контракт между автором модуля и его пользователями.

Теперь у нас на руках есть всё, что нужно, чтобы продолжить погружаться глубже и собирать пазл этой технологии в своей голове.
Честно говоря, я пока не знаю, о чём будет следующее видео — у меня нет заранее подготовленной схемы. Этот плейлист рождается естественным образом, по мере изучения.

В общем, если данное видео показалось вам интересным, то не забывайте ставить лайк и подписываться.
И буду рад видеть вас в своём Telegram.

Спасибо, что досмотрели. Всем пока!

