# Go Study Notes



### About Go

The `Go` programming language had its inception in 2007 at Google when the three computing veterans Rob Pike, Robert Griesemer, and Ken Thompson decide to create a new system programming language for the types of engineering challenges that they were facing in Google.

[To Do]

##### Compiled

`Go` is compiled ahead of time to _native machine code_, like `C/C++`, or `Rust`and unlike `Java`, `JavaScript`, or `.Net` (which have intermediate code executed by a _"virtual machine"_). 

`Go` programs do however require runtime language services such as stack management, garbage collection, concurrency etc. In `Go` these are provided by the `runtime` library (which is part of the _standard library_). This gets included into every `Go` program. This is analogous to `libc` in `C`.

[To Do]

###### Go Playground

Online code playground for **`Go`** => `https://play.golang.org`

#### Standard Library

The `Go` programming language comes with a very broad and powerful **standard library** that provides capabilities/utilities for most programming tasks that we can think of (from networking, to cryptography, to IO). We can navigate to it online at `https://golang.org/pkg`. Here we can explore, search and even run examples (in the online `Go` _playground_).



### `GOROOT`

When we install `Go` it comes with the **Go Toolchain**, which are essentially a set of programs that enable us to develop `Go` programs locally. The most important of these is the `go` tool. This is used to **build**, **fetch**, **install packages** etc. (it is similar to `npm` though lacking the built in dependency management capability. In latest versions of `Go` that is augmented by the `Modules` system with the `mod` tool).

By default `Go` is installed in the **`/usr/local/go`** path. This is the **root** of your `Go` installation and is referenced by the `$GOROOT` environment variable (`Go` environment variable). 

The `$GOROOT`  is used by the `go` tool to find the compiler (located at `$GOROOT/pkg/tool/$GOOS_$GOARCH`, so on an `Ubuntu` machine it might be `/usr/local/go/pkg/tool/linux_amd64/go`) . It is also the location where the **standard library** is located (`$GOROOT/pkg/$GOOS_$GOARCH`, so again on a `Linux` machine it might be `/usr/local/go/pkg/linux_amd64`). 

_Note: similarly the `$GOOS` and `$GOARCH` are `Go` environment variable that refer to the target operating system and CPU architecture._

The `go` tool itself will be in the `$GOROOT/bin/` path. `$GOROOT` is kind of similar to `$JAVA_HOME` for `Java`.

From version `1.0` of `Go` onward the `$GOOS`, `$GOARCH` etc. are automatically set by the installation script. And for later, more current versions the `$GOROOT` environment variable is also set by default. In fact we can see the `Go` environment variables with the `env` command.

```bash
$ go env
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/home/alex/.cache/go-build"
GOENV="/home/alex/.config/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOINSECURE=""
GOMODCACHE="/home/alex/go/pkg/mod"
GONOPROXY=""
GONOSUMDB=""
GOOS="linux"
GOPATH="/home/alex/go"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
GCCGO="gccgo"
AR="ar"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build371365250=/tmp/go-build -gno-record-gcc-switches"
```

If we want a specific environment variable just do add that at the end (for example `$ go env GOROOT`).

It is recommended that we do NOT change the default value of the `$GOROOT`, unless there is a real need as this can lead to confusion with target libraries and compiler versions getting mixed up. The only situation we might want to override the default value of `$GOROOT` is if we choose to install `Go` in a path different from the default (`/usr/local/go` on `Linux` or `C:\go` on `Windows`). In that case we should change the `$GOROOT` to point to the path of installation, so that the `go` tool can find the required tools and packages.

A typical `$GOROOT`  would look like:

```bash
$ tree `go env GOROOT` -L 2
/usr/local/go
├── api
│   ├── # data for Go's API checker
│   └── README
├── AUTHORS
├── bin
│   ├── go # This is the "go tool"
│   └── gofmt # Go format tool
├── CONTRIBUTING.md
├── CONTRIBUTORS
├── doc
│   └── # documentation
├── favicon.ico
├── lib
│   └── time
├── LICENSE
├── misc
│   └── # misc libraries
├── PATENTS
├── pkg
│   ├── include
│   ├── linux_amd64 # This is where the standard library is
│   ├── linux_amd64_race
│   └── tool # The compiler will be within this
├── README.md
├── robots.txt
├── SECURITY.md
├── src
│   └── # source files
├── test
│   └── # tests
└── VERSION
```

### `GOPATH` and Code Organisation

The `go` tool is strongly convention based, and requires us to organise our code in a specific structure. 

- It expects our code to exist in a **`workspace`** (which is really just a directory within which all our `Go` projects are located). 
- Within the **`workspace`** we should have a **`src`** for all the source code. It would also have **`bin`** and **`pkg`** for compiled binaries (executables and libraries).
  - **`src`** - This directory is the location for `Go` source code files (our own or what we pull from others).
  - **`pkg`** - This is where `Go` will place all the non-executable package "object files", also called "package archives" therefore they have "**`.a`**" extension. It is similar to the "**`.o`**" object files in **`C/C++`**, used at the "linking" stage to form the final executable.
  - **`bin`** - Contains compiled executable programs. When we run `go install` on a program/package directory, the `go` tool will install the executable to this `bin` directory.
- Within **`src`** we we would have directories for version controlled repositories (`Git` repos for example).
- Each **repo** would have **package** directories, where package is the compiled unit of code that provides encapsulation, modularity, reuse etc.
- Each **package** directory contains one or more `Go` source code files, and all files within a directory belong to a single package. The directory name would normally be the **package** name.
- When referencing a **package** from another one, the "import path" would be the directory path.

The `$GOPATH` environment variable specifies the path to the **`workspace`**. By default it is `$HOME/go` (or `%USERPROFILE%\go` on `Windows`). In a typical `Go` development environment all `Go` projects (a project will be a **package**) reside in a single **`workspace`**. This is different from other programming languages/platforms (such as `Node.js`, `Java`, `.Net` etc.) where each project can have its own **`workspace`** totally independent of any global directory structure for the whole language/platform.

The `$GOPATH`  environment variable serves TWO purposes, namely:

- It is the location for all dependencies fetched by `go get` command. So just after installation of `Go` and the related tools, even without any of our own projects our `$GOPATH` may look like:

  ```bash
  $ tree `go env GOPATH` -L 2
  /home/alex/go
  ├── bin
  │   ├── dlv
  │   ├── fillstruct
  │   ├── gocode
  │   ├── gocode-gomod
  │   ├── godef
  │   ├── godoctor
  │   ├── golint
  │   ├── gomodifytags
  │   ├── go-outline
  │   ├── gopkgs
  │   ├── goplay
  │   ├── gorename
  │   ├── goreturns
  │   ├── go-symbols
  │   ├── gotests
  │   ├── guru
  │   └── impl
  └── pkg
      ├── mod
      └── sumdb
  ```

- Resolving the **`import` path** for packages that are not part of the **standard library**. Whatever fully qualified path to a **package** we specify in our **`import`** directive, the `go` tool will try to find that within the `$GOROOT/src` OR `$GOPATH/src`. If we place our **package** directory outside of these it won't be able to find them (and we will get a _cannot find package_ error).

  Using an example to make it clearer, let us suppose that we create a `Go` project in some random directory (that is not the `$GOPATH`), and suppose the directory tree looks like:

  ```bash
  ~/tech/go-study$ tree -L 5 		# a non $GOPATH dierctory
  .
  ├── bin
  └── src							# source dir for Go project
      └── github.com				# repository dir as per convention
          └── alex				# repo path
              ├── hello			# package 'hello' (executable)         
              │   └── hello.go	# Go code for 'hello' -> references 'strutil'
              └── strutil			# package 'strutil' (lib package)
                  └── strutil.go	# Go code for 'strutil'
  ```

  - Here we have a `hello.go` program in a `hello` package that references another package `strutil`.

  - `strutil` is a simple program with one function that can reverse a `string`

    ```go
    // package name
    package strutil
    
    // function to reverse string
    func Reverse(s string) string{
    	r := []rune(s)
    	// some logic to reverse
    	return string(r)
    }
    ```

    

  - The code in `hello.go` uses the`Reverse` function in `strutil`, therefore will need to reference (`import`) it.

    ```go
    package main
    
    // import necessary packages
    import (
    	"fmt" // format output package from std. lib.
    	strl "github.com/alex/strutil" // custom strlib package as alias 'strl'
    )
    
    func main(){
    	fmt.Println("Hello Go!")
    	fmt.Println(strl.Reverse("Hello Go!")) // use Reverse function
    }
    ```

    

  - We try to import the `strutil` package with its path (w.r.t. to the parent directory) - `github.com/alex/strutil`, and assign it an alias `strl` (for brevity). Finally we use the `Reverse` function of the package to print a reverse of our string.

  - If we now try to build this with `go build`, we will get an error:

    ```bash
    $ go build hello.go
    hello.go:7:2: cannot find package "github.com/alex/strutil" in any of:
    	/usr/local/go/src/github.com/alex/strutil (from $GOROOT)
    	/home/alex/go/src/github.com/alex/strutil (from $GOPATH)
    ```

    **Evidently the `go` tool is searching for the `strutil` package relative to `$GOROOT/src` and `$GOPATH/src`.**

  - The problem here is that my project is not in a `Go` **`workspace`**. It is in a path `~/tech/go-study` that is outside of it. There are TWO ways to fix this problem:

    - Move the code to the `Go` `workspace` (the path `$GOPATH` points to, which is by default `~/go`). Traditionally `Go` developers keep all their `Go` projects within this directory hierarchy.

    - We can append another path to the `$GOPATH` env variable. The `$GOPATH` env variable need not be a single path, it can be a list of paths that the `go` tool can search through. So we could add our local `Go` projects directory (`~/tech/go-study`) to `$GOPATH` by editing the **`.bashrc`** file (in `Ubuntu`):

      ```bash
      ...
      export GOPATH=~/go
      export GOPATH=$GOPATH:~/tech/go-study
      ```

      

    - Now if reload our shell (or open a new shell) and try to build `hello.go` we should be successful, as now our project directory is also a `workspace`.

      ```bash
      $ source ~/.bashrc
      $ go build hello.go
      $ ./hello
      Hello Go!
      !oG olleH
      ```

      

    - So it is possible to have more than ONE `workspace` for `Go`, and we can use that to setup our local environment. However there are a few things to remember here:

      - The `go get` tool will only use the FIRST path in the `$GOPATH` to download tools and packages.
      - This approach does not scale well. We cannot keep on arbitrarily creating `Go` projects in any directory we want and then adding that `$GOPATH`. It would soon become unwieldy.
      - If we use non conventional directory structures, others who try to use our package may run into issues.  

    - This is where **`Go` Modules** can help us break free of this constraint, and is now the recommended way to organise our `Go` code.

### Go Modules

Prior to **modules** we only had the concept of **packages** and `GOPATH` to help us manage _dependencies_ used by our _Go_ project. Those dependencies could be _local dependencies (first-party dependencies)_ that we wrote locally as different **packages** that we _imported_, OR they could be _third-party dependencies (packages written by someone else)_ that we _imported_ from some _VCS repository_ (like `github.com`).

In this configuration `GOPATH` served two purposes:

- The **`go`** _tool_ would try to locate all `imported` packages relative to the `GOPATH/src` (or `GOROOT/src`). 
- All _dependencies_ fetched by the **`go get`** command would be saved into `GOPATH`.

This early approach was driven mostly by the giant _monorepo_ style at _Google_. In the broader community however, this had some disadvantages, mainly:

- All _Go_ projects needed to be in the `GOPATH`. We could not checkout a project into a directory of our choice, like we would do in most other languages.
- Also we could always checkout only one version of a package(and its dependencies) at any one time.

_Go 1.11_ introduced the concept of **Modules** to better manage project dependencies. A **module** in _Go_ is physically _a collection of Go **packages** stored in a filesystem **tree** with a **`go.mod`** file at its root._

The **`go.mod`** defines the **module path** and lists the **dependencies** needed for the project. It is similar in some ways to **`package.json`** in _Node.js_ (though there are many differences as well).

 **Modules** are no longer limited to `GOPATH`, they can be in any directory. To start a **module** we use the **`go mod init`** command followed by the _module name_ in a directory of our choice. This will create a **`go.mod`** file with basic information such as the _name of the module_ and the _version of Go_.

Let us create a simple project that should help work through the basic concepts of _Go_ **modules**. The application prompts the user to input a number into the terminal (`stdin`) and then checks and responds if it is a **prime number** or _not_. It will be organised as two **packages** - an _application_ that serves as the frontend (interacts with the user), and a _library_ that encapsulates the logic/mathematics of prime checking. Both these **packages** will be in a _parent directory_. This directory structure can be anywhere in our filesystem, as we like to organise our projects. Our filesystem tree will look like.

```bash
$ tree
.
└── checkprime				# parent directory
    ├── checkprimeapp		# frontend app - package (binary)
    │   └── primeapp.go
    └── primecalc			# library package used by frontend
        └── primecalc.go
```

_Remember at this point we have not specified ore created any modules_.

Also for the sake of avoiding unnecessary complexity & noise we are deviating from the idiomatic _Go "convention"_ of naming **packages** with `<version control system>.<repository>.<package name>`. Normally our **packages** may be more like `github.com/alex/checkprime/primecalc`. This is a good practice in actual projects but not necessary for the tools to work. In fact for demonstrating the concepts it might be better to avoid it for now.

Now we can write some code in our _Go_ source files to make provide this capability.

```go
//checkprime/primecalc/primecalc.go
package primecalc

import "math"

// IsPrime - naive algorithm to check divisibility
// till square root of num
func IsPrime(num int) bool {
	prime := true
	fnum := float64(num)
	gmid := int(math.Ceil(math.Sqrt(fnum)))
	for d := 2; d <= gmid; d++ {
		if math.Mod(fnum, float64(d)) == 0 {
			prime = false
			break
		}
	}
	return prime
}
```

**Package** `primecalc` exports a single function `Isprime` that has a simple implementation to check if the argument passed in is a prime number or not.

```go
// checkprime/checkprimeapp/primeapp.go
package main

import (
	"checkprime/primecalc"
	"fmt"
	"os"
	"strconv"
)

func main() {
	fmt.Println("Enter an integer to check if it is Prime or Not:")
	fmt.Print(">> ")

	var x string
	fmt.Scanln(&x)

	// try converting to integer
	i, e1 := strconv.Atoi(x)

	if e1 != nil {
		fmt.Printf("Invalid input : '%s'\n", e1.Error())
		os.Exit(1)
	}

	// if reached here, 'i' is a valid integer
	if primecalc.IsPrime(i) {
		fmt.Printf("%d is Prime.\n", i)
	} else {
		fmt.Printf("%d is Not Prime!\n", i)
	}

}
```

The _binary_ **package** `checkprimeapp` references (`imports`) the `primecalc` **package** and uses the exported function.

However even within the IDE we can see the **compiler error** - `"cannot find package "checkprime/primecalc" in any of: ... $GOROOT, from $GOPATH)"`. So without the benefit of **modules** the **`go`** _tools_ are still looking in the `$GOPATH` or `GOROOT` for the referenced packages.

To resolve this we simply make the _parent directory_ a _Go_ **module**. From within the _parent directory_ we use the `go mod init` command.

```bash
$ go mod init checkprime
go: creating new go.mod: module checkprime
```

Now we should see a **`go.mod`** file in that _parent directory_.

```bash
$ tree
.
└── checkprime
    ├── checkprimeapp
    │   └── primeapp.go
    ├── go.mod				# module definition file
    └── primecalc
        └── primecalc.go
```

Initially our **`go.mod`** file only contains very basic information.

```go
// chechkprime/go.mod
module checkprime

go 1.15
```

Just by doing that, our compile error goes away. Now the **`go`** _tools_ can find our _referenced package_ (`"checkprime/primecalc"`). _Note_ that the **package reference** is _with respect to_ the **module**.

Now we can **`run`** or **`build`** our main application and we should get an executable binary that does what we programmed it to do.

```bash
$ cd checkprimeapp && go build
$ ls
checkprimeapp  primeapp.go
```

```bash
$ ./checkprimeapp 
Enter an integer to check if it is Prime or Not:
>> 23
23 is Prime.
```

So, now we know how to reference _local dependencies/packages_ using **modules**, and how it does not need the `GOPATH`.

What about _external (third-party) dependencies_? let us enhance our example to prettify our printing to the console by making it colourful. To do this we can use a **module** written by someone else (**`github.com/fatih/color`**) instead of solving for that ourselves. 

To do this we can do a **`go get github.com/fatih/color`** and it should do the following:

- Pull down the **module** source from the _GitHub_ repository to **`$GOPATH/mod`**

  ```bash
  # downloaded module
  $ tree -L 3 ~/go/pkg/
  /home/abhi/go/pkg/
  ├── mod
  │   ├── cache
  │   │   ├── download
  │   │   └── lock
  │   ├── github.com
  │   │   ├── acroca
  │   │   ├── cosiner
  │   │   ├── cpuguy83
  │   │   ├── cweill
  │   │   ├── fatih			# the module we pulled down
  │   │   ├── go-delve
  ```

- Add a **`require`** entry in the **`go.mod`** file for the dependency

  ```go
  module checkprime
  
  go 1.15
  
  require github.com/fatih/color v1.12.0		// dependency including the version
  ```

- Create a **`go.sum`** file with the checksum information for the dependencies. This will normally have two lines, one is a hash of the _content_ and the other is a hash of the _transitive dependencies_. This file can be checked in or distributed with your code to ensue that when someone else who uses your code _gets_ these transitive dependencies they are not tampered with and are indeed the ones you included in your original build.

- 



### `go` commands `run`, `build`, `install`

These are the most common `go` commands that we use while writing and testing out `Go` code.

- **`run`** - We execute `go run` within the package directory on a `Go` source file, provided that it is an executable package, and this will build the binary and execute the `main` (entry point) code. However this is ephemeral and the the binary is not persisted.

- **`build`** - The `go build` command will compile the specified package (default is the current package/directory), and all the packages it depends on, then invokes the _linker_ to generate an executable binary and puts it in the current directory.

  If we ant to specify an output path we can do so with the `-o` flag.

  ```bash
  $ go build -o=/tmp/foo	.	# build the '.' current package and output to '/tmp/foo'
  ```

  From `Go 1.10` onward the `go build` tool _caches_ the build result in the _build cache_ which will be reused appropriately by the tool chain. This dramatically speeds up the build process. We can see the _build cache_ path with `go env GOCACHE` environment variable.

  If we want to _force_ a rebuild of all packages ignoring the _cache_ we can use the `-a` flag. Sometimes we need to do this when `Go` cannot detect the changes to the dependencies automatically, like with `cgo` imports (for `C` code). Also we can clear the _cache_ with the `go clean -cache` command

  ```bash
  $ go build -a 
  $ go clean -cache
  ```

  Another useful option is the `-x` switch that shows all the commands and steps executed to generate the build (this is like _verbose_ option in some other languages).

  Lastly a related command that can help us to see all the "dependencies" for our package is the `go list` command.

  ```bash
  $ go list -dep . | sort -u
  errors
  fmt
  github.com/alex/hello
  github.com/alex/strutil
  internal/bytealg
  internal/cpu
  internal/fmtsort
  internal/oserror
  internal/poll
  internal/race
  internal/reflectlite
  internal/syscall/execenv
  internal/syscall/unix
  internal/testlog
  internal/unsafeheader
  io
  math
  math/bits
  os
  reflect
  runtime
  runtime/internal/atomic
  runtime/internal/math
  runtime/internal/sys
  sort
  strconv
  sync
  sync/atomic
  syscall
  time
  unicode
  unicode/utf8
  unsafe
  ```

  _Most of these are default imported with runtime, `fmt` is explicitly imported from the 'standard library', and only the `github.com/alex/strutil` is our own dependency_.

- **`install`** - This will _build_ and deploy the package in the `$GOPATH/bin` or `$GOPATH/pkg` directory. If the package is a _main_ package (executable) it will be installed in the the `bin` and otherwise it will go to the `pkg` directory. A `go install` with our simple project might look like:

  ```bash
  $ tree
  .
  ├── bin
  │   └── hello						# installed main binary executable
  ├── pkg
  │   └── linux_amd64
  │       └── github.com
  │           └── abhilash-ponnachan
  │               └── strutil.a		# lib package 'archive' file
  └── src
      └── github.com
          └── abhilash-ponnachan
              ├── hello        		# main package  
              │   └── hello.go		# main package code
              └── strutil				# lib package that 'main'depends on
                  └── strutil.go		# lib package code
  ```

  _The `.a` extension for the package in the `pkg` stands for 'archive' file, which is just an 'object' file like in `C/C++`._

### `gofmt`  - formatting `Go` code

`Go` is very prescriptive about the _"style"_ of the code and it has a standard that is considered idiomatic. The philosophy behind this is to encourage a common generally accepted standard for development in large teams (without getting into subjective preferences). We can apply the standard `Go` formatting to our source code with the `gofmt` command. 

```bash
$ gofmt -w hello.go		# -w flag will overwrite the source file after formatting
$ gofmt -w -l util/		# format all files in `util/` dierctory and '-l' to list the cahnged files
```

With IDEs such as 'VS Code' the `gofmt` can work automatically when we save the file to keep the code in standard format continuously.

We can also use the `go fmt` command, which does the same thing.

```bash
$ go fmt				# format all source code in the current directory
```



## Go Basics

### Variables

- In `Go` we can declare a variable using the `var` keyword, followed by the name and then the type (type comes after variable name unlike `C` syntax).

- We can also initialise the variable with a value along with the declaration.

  ```go
  var age int					// decalre variable with type
  age = 23					// initialize value
  
  var name string = "Alan"	// initialize with declaration
  
  var greeting = "Hello"		// type will be infered, avoid specifying type if Go can infer
  ```

  

- Note that `Go` is able to _infer_ the type from the assigned value, so it is not needed to specify the type if we are assigning a value. In fact the `go` tool will complain with a _warning_ that we do not have to explicitly specify type when `Go` can infer it.

- The shorthand notation for _declaring_ a variable and _initialising_ it is using the `:=` operator. Note, in this form we do not use the `var` keyword, nor the type. Note that this works only for local (block) scoped variables.

  ```go
  score := 130				// initilaize and declare local variables with shorthand notation
  ```

- `Go` does not allow us to _declare_ variables that are not used in the code (the compiler will throw an error). This helps keep the code clean. There may be rare situations where we might need to have a variable which may not be used (generally when importing packages that have only _init_ functions for example). In such situations we can use the _'blank identifier'_ (`_`) (_on the LHS of assignment_).

  ##### Multiple Declarations

- `Go` allows us to declare (and also assign values) multiple variables in in a single line (or statement). We can do this using the `:=` operator or the `var` keyword.

  ```go
  greeting, name, age := "Hello", "Alan", 23		// declare 3 new variables and assign values
  
  greeting, age = "Hola", 25						// note we use '=', as no new variables just changing values
  
  age, active := 27, true							// note we use ':=' as we have at least one new variable 'active'
  ```

  If we want to declare multiple variables in a single statement outside of _block scope_ we can use the `var` keyword along with the variables in `()`.

  ```go
  var (
      name string
      greeting string
      age int
  )
  name, greeting, age = "Alan", "Hola", 27
  ```

  Of course we could have initialised the variables along with the declaration.

  ```go
  var (
      name string = "Alan"
      greeting string = "Hello"
      age int = 23
  )
  ```

  If we want it all in one line we could separate the variables using `;`.

  ```go
  var (
      name string = "Alan"; greeting string = "Hello"; age int = 23
   )
  ```

  However this is not considered clean idiomatic `Go` code, and if we do `go fmt` then it will reformat it to split them neatly across lines.

- If we have multiple variables with the same _type_ we do not need to repeat the _type_, we can just specify that once after the variables (clubbed together).

  ```go
  var name, greeting string, age int
  ```

- This ability for multiple declarations and assignments in `Go` comes with an interesting shorthand syntax for _"swapping"_ variable values.

  ```go
  func main() {
    x, y := 10, 20
    fmt.Println("x =", x, ", y =", y) // x = 10, y = 20
    // swap in one line
    y, x = x, y
    fmt.Println("x =", x, ", y =", y) // x = 20, y = 10
  }
  ```

  Note, how we swapped the values of the variables without using an intermediate variable!

- Like most other languages, in `Go` we have to define a variable before it can be used.

### Constants

We use the `const` keyword to declare constants in `Go`. Constants are determined at compile-time, and they cannot be modified at run-time. This allows the compiler to catch certain bugs earlier on.

In `Go` we have to explicitly initialise the value of a constant, we cannot leave it to be set to its _zero value_. 

Declaring a _constant_ is very much similar to how we declare _variables_ in syntax. We can specify the type or leave it to be inferred by `Go`.

```go
const n, d = 22, 7
const pi = n * 1.0 / d
fmt.Printf("%d, %d, %f\n", n, d, pi);			// 22, 7, 3.142857
```

If we wanted to declare all the three constants together we could try putting it all in one line `const n, d, pi = 22, 7, n * 1.0 / d`, but this would not compile as the constants `n` and `d` cannot be determined on the right-hand-side one the same line of declaration. However this can be achieved using the **block declaration** syntax, by simply enclosing the constants within parentheses `()` and separating each with a new line.

```go
// block-declaration of constants
const (
    n = 22
    d = 7
    pi = n * 1.0 / d
)
fmt.Printf("%d, %d, %f\n", n, d, pi);
```

### Scope



### Types

`Go` is a _statically typed_ language, which means the type of a variable has to be determined before compile and cannot change thereafter. Many of the mainstream languages like `C\C++`, `Java`, `C#` are all statically typed. Some other popular languages such as `JavaScript`, `Python`, `Ruby` are _dynamically typed_ and the type (memory structure) of the data referenced by variables is determined and bound at runtime.

`Go` gives us the benefits of `static typing` such as compile time type-checks that can catch type related errors ahead of time, as well as certain performance optimisations that the compiler can do (since the data type does not change), all without getting the noise of type declarations in the way. It has good _type inference_.

##### Basic Types

The _"basic data types"_ in `Go` are **integers** (`int`), **floating point numbers** (`float`), **strings** (`string`), and **boolean** (`bool`). These are the _"primitive types"_ from which other composite types are built as needed.

- **Integers**

  _Integers_ in `Go` range from **8 bits** to **64 bits**, and they can be **signed** or **unsigned** (this is typical of most programming languages). We can specify the exact memory type of the _integer_ we want using `int8`, `int16`, `int32`, `int64` or their unsigned variants `uint8`, `uint16`, `uint32`, `uint64` . Commonly though we would simply use the `int` or `uint` types which refer to the _32_ bit or _64_ bit version depending of the default word size of our CPU and OS.

  `Go` is _strongly typed_ and will complain if we try to assign values to variables that do not have an exact data type match.

  ```go
  var a int8 = 127
  var b int16
  
  b = a			// Error : cannot use type int8 as value in assignment to int16 !!
  ```

  For dealing with _binary_ data or _characters_ we would use `byte` and `rune` which are really just `uint8` and `uint32` respectively.

  ```go
  var a uint8 = 127
  var b byte
  
  b = a			// This is OK
  ```

- **Floating Point**

  Like almost any other programming language `Go` support _'Real numbers'_ using floating point notation (`IEEE 754` standard). We specify this type using `float64` or `float32`.

  ```go
  var n, d float32 = 22.0, 7.0
  p := n / d
  fmt.Println("p =", p, reflect.TypeOf(p))		// p = 3.142857 float32
  ```

  Here we specified the type of `n` and `d` variables as `float32` and the division resulted in assigning `p` as `float32` as well.

  _Note: How we determined the 'type' of the variable at runtime using 'reflection', with the `reflect` package. We had to import that package and it is part of the 'standard library'._

  If we had not specified the 'type' of the operands explicitly and let `Go` infer all of it it would default depending on the architecture/OS.

  ```go
  var n, d = 22.0, 7.0
  p := n / d
  fmt.Println("p =", p, reflect.TypeOf(p))		// p =  3.142857142857143 float64
  ```

  We have to keep in mind that _'Real number'_ operations with computers are not exactly accurate, as the numbers are represented ultimately as `Binary` and in this process we lose some precision, therefore the resulting operations on these lose accuracy. 

  ```go
  a, b := 0.1, 0.2
  fmt.Println(a, " + ", b, " == 0.3 ->", (a + b) == 0.3)			// 0.1  +  0.2  == 0.3 -> false
  fmt.Println(a, " + ", b, " = ", a + b)							// 0.1  +  0.2  =  0.30000000000000004
  ```

  With `float64` we cannot get `0.1 + 0.2` as `0.3` exactly.

  In this situation _'reducing the precision'_ actually results in correct behaviour!!

  ```go
  var a, b float32 = 0.1, 0.2
  fmt.Println(a, " + ", b, " == 0.3 ->", (a + b) == 0.3)			// 0.1  +  0.2  == 0.3 -> true
  fmt.Println(a, " + ", b, " = ", a + b)							// 0.1  +  0.2  =  0.3
  ```

  We can also _cast_ from one numeric type to the other if needed by prefixing the value with the required type in parentheses.

  ```go
  a, b := 0.1, 0.2
  fmt.Println(a, " + ", b, " == 0.3 ->", float32(a + b) == 0.3)		// 0.1  +  0.2  == 0.3 -> true
  fmt.Println(a, " + ", b, " = ", float32(a + b))						// 0.1  +  0.2  =  0.3
  ```

  In short we have to be mindful of the fact that we cannot exactly represent _'Real numbers'_ accurately and if very high accuracy is needed (like in financial or scientific computing), it is better to use specialised libraries (packages).

- **Strings**

  In this section we will take a quick high-level look at `string` data type, enough to help us use it in our code examples. However there is a lot more to _Strings_ in `Go` if we wish to really understand it in detail, especially how it is encoded in memory and what that means when we deal with _Unicode_ text. We shall look that later, as in order to do that we need to cover composite data structures like _Arrays_ and _Slices_ before that. For now we shall look at _string literals_ and simple _string operations_.

  _String literals_ in `Go` are declared by enclosing characters with _double quotes_ (`""`) or _back quotes_(`` ` ` ``).

  - _Double quotes_ (`""`) : _String literals_ within `""` allow us to specify escape characters (such as `\t` or `\n` etc.) within it.
  - _Back quotes_ (`` ` ` ``) : Whatever is specified within `` ` ` `` is treated exactly as it is (literally), which means we cannot apply escape characters.

  ```go
  var a string 
  a = "Tab\tseperated"
  b := "New\nline"
  c := `No\tescape\nat "All"`
  fmt.Println(a)			// Tab seperated
  fmt.Println(b)			// New
  						// line
  fmt.Println(c)			// No\tescape\nat "All"
  ```

  - **Length**

    We can use the builtin `len` function to get the length of a `string`.

    ```go
     a := "This string has 30 characters."
     fmt.Println(len(a)) 						// 30
    ```

    However we have to be careful when using `len` because it really returns the _"number of bytes"_ in the string! Which implies that since `Go` uses **UTF-8** encoding, for characters that use multi-byte (beyond common **ASCII** range), the `len` function can be confusing. For example.

    ```go
     b := "∑"
     fmt.Println(len(b)) 						// 3 !!
    ```

    Here `∑` is only one character but since it requires **3 bytes** () to represent in **UTF-8**, `len` will return **3** !!

    We shall examine this in detail later when we learn about how `Go`represents _strings_ and how to deal with multi-byte characters.

  - **Concatenation**

    Like in most other languages we can use the **`+`** operator to concatenate two _strings_. The result will be a _new string_. Again similar to other languages _strings_ are **immutable** (that is the sequence of bytes associated with a _string_ cannot be changed). We can of course assign a new _"value"_ to an existing _string variable_ though.

    ```go
    a := "The quick " 
    b := "brown fox"
    
    b = a + b        // cat 'a' & 'b' -> 'b'
    fmt.Println(b)   // The quick brown fox
    ```

  - **Substring**

    We can access part of a _string_ using `0` based index for the positions surrounded by square brackets (**`[`_`start-index`_`:`_`end-index`_`]`**). After we learn **arrays** and **slices**, we will understand the mechanism better, but for now it is helpful to just know the syntax to achieve this. Like many other languages `Go` allows us to ignore the left or/and right boundaries if we are starting the beginning or going to the end respectively.

    ```go
    a := "ABCDEFGHIJKLMNOPQRSTUVWXYZ" 
     
    fmt.Println(len(a))    // 26
    fmt.Println(a[0:3])    // ABC
    fmt.Println(a[:3])     // ABC
    fmt.Println(a[1:3])    // BC
    fmt.Println(a[3:15])   // DEFGHIJKLMNO
    fmt.Println(a[24:26])  // YZ
    fmt.Println(a[24:])    // YZ
    ```

    Again this way of directly accessing a _substring_ using direct **slice** of **bytes** will fail if we have **"multi-byte"** _Unicode_ characters. We shall see how to handle them safely with **"runes"** later.

    ```go
    a := "£∑"              // Non ASCII cahacters
    
    fmt.Println(len(a))    // 5
    fmt.Println(a[0:1])    // �
    fmt.Println(a[3:])     // �
    ```

  - **Formatting**

    To format _strings_ in `Go` we use the `Printf` (and `Sprintf`) functions, from the `fmt` package. These functions take a _"template string"_ as the first parameter and the value to be formatted (to _string_) as the second parameter. The _"template string"_ will contain _"format specifier verbs"_ (like `%d`, `%f` etc.). 

    `Printf` prints to _standard I/O_ and `Sprintf` returns a formatted `string` value. This is very much like language such as `C/C++` and `Rust`. Note that the formatted print functions do not automatically append a _new line_, if we need can add a `\n` special character in the _"template string"_. The best way to understand this syntax and format specifiers is to see a few examples:

    ```go
    // format text 'integers' : verb %d, %b, %o, %x
    i := 13
    fmt.Printf("%d\n", i)       // 13
    fmt.Printf("%+d\n", i)      // +13 (with sign)
    fmt.Printf("[%+4d]\n", i)   // [  13] (left pad space)
    fmt.Printf("[%-4d]\n", i)   // [13  ] (trailing pad space)
    fmt.Printf("[%04d]\n", i)   // [0013] (pad '0')
    fmt.Printf("%08b\n", i)     // 00001101 (binary)
    fmt.Printf("%o\n", i)       // 15 (octal)
    fmt.Printf("%x\n", i)       // d (hex)
    fmt.Printf("%X\n", i)       // D (Hex)
    fmt.Printf("%#x\n", i)      // 0xd (Hex)
    
    // format text 'float' : verb %f, %e
    f := 13.00530
    fmt.Printf("%f\n", f)       // 13.005300
    fmt.Printf("%.2f\n", f)     // 13.01 (precision 2, rounding)
    fmt.Printf("[%8.2f]\n", f)  // [   13.01] (widthd 8, precision 2)
    fmt.Printf("%e\n", f)       // 1.30053e+01 (scientific notation)
    fmt.Printf("%g\n", f)       // 13.0053 (as needed, necessary digits ony)
    
    // format text 'char' : verb %c, %q, %U
    c := 'A'
    fmt.Printf("%c\n", c)       // A
    fmt.Printf("%q\n", c)       // 'A'
    fmt.Printf("%U\n", c)       // U+0041 (unicode)
    fmt.Printf("%#U\n", c)      // U+0041 'A'
    
    // format text 'bool' : verb %t
    fmt.Printf("%t\n", 1 == 3)  // false
    
    // format text 'string' (or byte slice) : verb %s
    s := "café"
    fmt.Printf("%s\n", s)       // café
    fmt.Printf("[%6s]\n", s)    // [  café] (pad leading space)
    fmt.Printf("[%-6s]\n", s)   // [café  ] (pad trailing space)
    fmt.Printf("%q\n", s)       // "café"
    fmt.Printf("%x\n", s)       // 636166c3a9 (hex dump of bytes)
    fmt.Printf("% x\n", s)      // 63 61 66 c3 a9 (space separated hex dump of bytes)
    
    // format text 'pointers' : verb %p
    ps := &s
    fmt.Printf("%p\n", ps)       // 0xc000010200 (pointer in hex)
    
    // special verbs: verb %v,%T
    fmt.Printf("%v\n", ps)      // 0xc000010200 (default format)
    fmt.Printf("%#v\n", ps)     // (*string)0xc000010200 (Go-syntax format)
    fmt.Printf("%T\n", ps)      // *string (type of variable)
    ```

    We can use `Sprintf` in the same way to format `string` values without printing. This serves the purpose of _"string interpolation"_ in many other languages.

    ```go
    r := fmt.Sprintf("sum of %d and %f is %g", i, f, float64(i) + f)
    fmt.Println(r)              // sum of 13 and 13.005300 is 26.0053
    ```

  - **Comparison**

    _Strings_ can be compared using the standard comparison operators `==`, `>`, `<`, `!=`, `<=`, `>=`. It will do a _byte_ by _byte_ comparison of the two _strings_ in lexicographical order (the order in which words appear in a _dictionary_).

    ```go
    a, b, c, d := "ABC", "ACC", "acc", "ABCc"
    
    fmt.Println(a > b)       // false
    fmt.Println(b >= c)      // false
    fmt.Println(b == c)      // false
    fmt.Println(a <= d)      // true
    ```

  - **Conversion**

    The _standard library_ comes with the **`strconv`** package, that provides conversion functions to convert to and from _string_ to almost all other data types. the common functions are:

     - `Atoi` (_string_ -> _integer_), `ParseBool`, `ParseFloat`, `ParseInt`, `ParseUnit`; they convert from `string` to the corresponding types values. These conversions can of course fail if the `string` does not represent a valid value in the target type, therefore these functions typically return two value - one is an _error_ (which will be empty if conversion is successful, OR specif the conversion error if not), the other is that actual value (assuming the conversion succeeded, otherwise this value is to be ignored). This is a typical pattern for providing return values from functions that could potentially fail.

       ```go
       sx, si, sf, sb := "34F", "23", "13.33", "true"
       
       // try convert 'sx' to int
       x, err := strconv.Atoi(sx)
       // test if conversion is successful
       if err != nil {
           fmt.Println(sx, "is not an integer")   // 34F is not an integer
       } else{
           fmt.Println("sx = ", x)
       }
       
       // we know 'si' is int so we can ignore 'err'
       i, _ := strconv.Atoi(si)
       fmt.Println(i + 1)                       // 24
       
       // try pasre 'sf' to float 64
       f, _ := strconv.ParseFloat(sf, 64)
       fmt.Println(f + 1.0)                     // 14.33
       
       // try parse 'sb' to bool
       fmt.Println(strconv.ParseBool(sb))       // true <nil>
       ```

     -  `Itoa` (_integer_ -> _string_), `FormatBool`, `FormatFloat`, `FormatInt`, `FormatUnit`; they convert from the specified type to `string`. Since `string` can represent anything these functions do not need to explicitly handle failure of conversion.

       ```go
       i, f, b := 23, 13.33, true
       
       fmt.Println("i = " + strconv.Itoa(i))   					// i = 23
       fmt.Println("f = " + strconv.FormatFloat(f, 'f', 5, 64)) 	// f = 13.330000
       fmt.Println("b = " + strconv.FormatBool(b))   				// b = true
       ```

- **Boolean**

  A _Boolean_ type is probably the simplest data type with either of two values `true` or `false`. It supports the standard _logical operations_, namely _and_ (`&&`), _or_ (`||`), _not_ (`!`).

  ```go
  var a bool = true
  var b = false
  c := true
  
  fmt.Println("a && b =", a && b)					// false
  fmt.Println("a || b =", a || b)					// true
  fmt.Println("!a =", !a)							// false
  fmt.Println("a || b && c =", a && b || c)		// true
  ```

  _Note: `Go` does not have a built-in 'logical XOR' operator, though we can express it using a combination of other operators._

  ```go
  // XOR = (v1 OR v2) AND NOT(v1 AND v2)
  fmt.Println("a XOR c =", (a || c) && !(a && c))   	// fasle
  // XOR = (v1 NOT EQUAL v2) for Boolean
  fmt.Println("a XOR c =", (a != c))   				// false
  ```

##### Zero Values

All variables in `Go` are initialised to their corresponding _zero values_.

### Control Flow

#### Conditionals

- **`if/else`**

  The syntax for `if/else` conditional check in `Go` is very similar to other `C` style languages:

  ```go
  if condition1 {
      // do something
  } else if condition2 {
      // do something else
  } else if condition3 {
      // do yet another thing
  } else {
      // otherwise do this
  }
  ```

  Notice that there is no need for parentheses `()` for the _"conditions"_. `Go` is also very struct about the format of the brackets `{}` for the blocks.

  Let us write some example code that reads a value from standard I/O and checks if it for range boundaries.

  ```go
  package main
  
  import (
    "fmt"
    "strconv"
    "os"
  )
  
  func main() {
    // prompt message to enter a number
    fmt.Println("Type in a number between 1 and 100.")
  
    // read string from stdio
    var inp string
    fmt.Scanln(&inp)
  
    // try convert to int
    val, err := strconv.Atoi(inp)
  
    // check if input text is integer else exit
    if err != nil {
      fmt.Printf("'%s' is not an integer!\n", inp)
      os.Exit(1)
    }
    
    // if reached here input text is int
    // do range checks and set result message string
    var msg string
    if val < 0 {
      msg = "is too small!"
    } else if val <= 40 {
      msg = "can be higher."
    } else if val <= 60 {
      msg = "just about right."
    } else if val <= 80 {
      msg = "can be smaller."
    } else {
      msg = "is too high!"
    }
    // print result message to stdio
    fmt.Printf("'%d' %s\n", val, msg)
  }
  ```

- **`switch-case`**

  When we need to check if the value of something is one of many different cases we can do that more simply using the `switch-case` construct, rather than repeated long winded `if/else` statements.

  Here we are checking if an expression is any of the values specified in the `case` statements.

  ```go
  func main() {
  	// prompt message to select one option
  	fmt.Println("Select one option: 'A', 'B', 'C' or 'D'.")
  
  	// read string from stdio
  	var inp string
  	fmt.Scanln(&inp)
  
  	// check which option was input
  	var msg string
      // switch-case to check for options
  	switch inp {
  	case "A":
  		msg = "USA"
  	case "B":
  		msg = "UK"
  	case "C":
  		msg = "France"
  	case "D":
  		msg = "Netherlands"
  	default:
  		msg = "<nowhere>"
  	}
  	// print result message to stdio
  	fmt.Printf("Welcome to '%s'.\n", msg)
  }
  ```

  Instead of checking against explicit values, if we needed to check against multiple logical conditions, that is possible too. We simply ignore the initial _"expression"_ to the `switch` and just specify the _"conditions"_ in the `case` statements. We could rewrite the previous `if/else` example using `switch` with conditional cases:

  ```go
  var msg string
  // switch with conditional cases
  switch {
      case val < 0:
      	msg = "is too small!"
      case val <= 40:
      	msg = "can be higher."
      case val <= 60:
      	msg = "just about right."
      case val <= 80:
      	msg = "can be smaller."
      default:
      	msg = "is too high!"
  }
  ```

  `Go` diverges from most other `C` style languages when in comes to `switch-case` in that the evaluation does not automatically _"fall through"_ to the next case. In `C/C++`, `C#`, `Java` etc. we specify a `break` command to stop _falling through_ to next case. In `Go` if want this behaviour we will need to explicitly specify that with the `fallthrough` keyword. This can cause hard to reason bugs and is best avoided. If we seem to have to use `fallthrough` there is mostly a better way to solve the problem.

#### Loops

In `Go` we use the **`for`** loop to repeat a block of operations a number of times. The general syntax is similar to other `C` style languages.

```go
for init; condition; post {			// Note that there are no '()' unlike 'C/C++'
    // block to repeat
}
```

- The **init** part, sets up the initial state before the loop begins. This part is _optional_.
- The **condition** is checked before each iteration to determine if the loop should continue or exit. This part is _optional_ too in the syntax, but if we do not have an exit condition anywhere (at least in the code block), then it can result in an infinite loop.
- The **post** part executes after each iteration. This is _optional_ as well.

Unlike other languages, in `Go` we have only the **`for`** loop syntax. However with the optional parts we can set it up in different ways to achieve the effect of `while .. do` and `do .. while` constructs (usually found in other languages).

The simplest for of the **`for`** loop is something like this:

```go
for i := 1; i <= 5; i++ {	// init i = 1, check if i <= 5, increment i after iteration
    fmt.Printf("%d\t", i)
}
// 1	2	3	4	5
```

We can achieve a **`while`** loop simply by leaving out the optional parts and handling them in outside and/or inside the block.

```go
i := 1
for i <= 5 {	// only condition check to continue
    fmt.Printf("%d\t", i)
    i++
}
```

Similarly we can set it up for a **`do..while`** loop (the condition is checked at the end of the iteration).

```go
i := 1
for {
    fmt.Printf("%d\t", i)
    i++
    // explicitly check condition at end of loop and 'break' out.
    if i > 5{
        break
    }
}
```

### Collections - Arrays, Slices, Maps

##### Arrays

An **array** is the most basic _collection_ data structure in `Go` (and in most other languages). In `Go` an **array** is a _contiguous_ sequence of elements of the _same type_ and having _fixed length_. We can specify and **array** type by stating the length (number of elements it can hold) and the type of the element.

```go
var nums [4]int // array of length 4 to hold integers
```

This will allocate the required space in memory and give us an **array** variable to work with it.

We access **array** elements using the `[<0 based index>]`, like in most other languages.

```go
var a [3]string
a[0] = "A"
a[1] = "B"
a[2] = "C"
// print array, length and capacity
fmt.Println(a, len(a), cap(a))	// [A B C] 3 3
a[0] = "Z"
fmt.Println(a)	// [Z B C]
```

We can initialise the **array** as a literal while declaring it by specifying the elements within `{}`. As a convenience, with **array literals** we can use `...` instead of specifying the length within the brackets. _The reason we can't leave it empty `[]` is because that syntax is used by **Slices** as we shall see later._

```go
a := [3]string {"A", "B", "C"}
// OR
a := [...]string {"A", "B", "C"}
```

**Arrays** in **Go** have _"value semantics"_, that is when they are assigned or passed as parameters to function, they get **copied** rather than getting passed as a _reference_. In other words an **array variable** in **Go** is a "value" (unlike in **C**, where the **array variable** is really a "pointer" to the first element).

```go
a := [3]string {"A", "B", "C"}
b := a
b[0] = "Z"
fmt.Printf("array 'a' = %v\n", a) // array 'a' = [A B C]
fmt.Printf("array 'b' = %v\n", b) // array 'b' = [Z B C]
```

Changing the value of `b` doe snot change the value of `a`, they are separate copies in memory. Whilst this may be useful in some situations, this is an expensive operation and not something you would want to do inadvertently. _As we will see soon this is one of the reasons why we normally use **slices** instead of **arrays** as they have "reference semantics"._

We cam use  **`for`** to loop through an **array** (or any iterable collection for that matter), using the **`range`** keyword. It produces an _iterator_ which returns an _index_ and the _item_ in the **array**, which we can assign directly to two variables (if we do not need the _index_ we can ignore that using **`_`**).

```go
a := [...]string{
    "sun",
    "mon",
    "tue",
    "wed",
    "thu",
    "fri",
    "sat",
    }
for i, d := range a {
    fmt.Printf("Day %d is %s\n", i, d)
}
```

_Note: how in when we break out the array initialisation to multiple lines (for readability) we have the give a "comma on the last line", this is intentional to make it easy to comment out or cut-paste sections without having to rearrange commas!_

**Arrays** in _Go_ are **one dimensional**, but we can combine **arrays of arrays** to get **multi-dimensional** arrays. In the example below we use an **array of arrays** to get a 3 X 4 matrix.

```go
 mat := [3][4]int {
    {11, 12, 13, 14},
    {21, 22, 23, 24},
    {31, 32, 33, 34},
  }
// print matrix
for _, r := range mat{
    for _, e := range r{
        fmt.Printf("%d ", e)
    }
    fmt.Println()
}
/*
11 12 13 14 
21 22 23 24 
31 32 33 34 
*/
```



##### Slices

The most common collection type used in _Go_ is the **slice**, which is a more flexible and dynamic type compared to **arrays**. A **slice** is really an abstraction over some underlying **array** (_backing array_). When we declare a **slice** variable it defines a descriptor with a internal _pointer_ to some starting location within its _backing array_, a _length_ (which is the number of elements referred in the slice) and a _capacity_ (the number of elements in the _backing array_ from the starting point of the  **slice** ).

The syntax for declaring a **slice** is almost same as that for an **array**, except that we do not specify a _fixed length_ when declaring it.

```go
// declaring a slice literal
a := []string{"aplha", "bravo", "charlie", "delta"}
```

This will create a **slice** and a _backing array_ to hold the values.

If we want to declare a **slice** variable without initialising it with values, we can do that using the **`make`** function.

```go
c := make([]string, 4, 4)
c[0] = "alpha"
fmt.Println(c) // [alpha    ]
```

The `make` function takes 3 parameters - _"a type"_, _"length"_ of collection, and an _"optional capacity"_. This will create the _underlying array_ and assign that to the **slice** variable. If _capacity_ is omitted, it defaults to _length_.

_Note: How the first parameter is "a type" and not "a value" - so the declaration of this function is using a "meta type" to denote that (it has the signature `func make([]T, len, cap) []T`). We shall see how to do this later, but now it is worth noting._

Very often a **slice** is formed by _"slicing"_ an existing **slice** or **array**. It uses a "half-open-range" specifier syntax separated by semicolon. This means that the left index is _included_ and the right index is _excluded_ (_this is similar to **Python**_). For example the range `[2:5]` implies a range - _starting at_ index `2`, and going on till index `4` (i.e. the indexes `2`, `3`, `4`). We can note that the number of elements is the difference of the _right index_ - _left index_.

```go
a := []string{"alpha", "bravo", "charlie", "delta"}
fmt.Println(len(a))   // 4
fmt.Println(cap(a))   // 4
fmt.Println(a)        // [alpha bravo charlie delta]

b := a[1:3]
// slice of 'a' from index '1' to '2'
fmt.Println(len(b))   // 2
fmt.Println(cap(b))   // 3
fmt.Println(b)        // [bravo charlie]
```

Note:

 - **slice** `b` has two elements starting at index `1` of the original **slice** `a`

 - **`len`** of **slice** `b` is **`2`** - which is evident, it has two elements

 - **`cap`** of **slice** `b` is **`3`** - which is not that evident, this is because the underlying **array** has a capacity of **`4`** and we are starting our **slice** `b` at index `1`, so the number of elements left till end of **array** capacity is **`4 -1 = 3`**.

The _range indexes_ for a slice are optional, if the _start index_ is omitted it defaults to `0` (the beginning of the source), and if the _end index_ is omitted it defaults the _end of the source_.

```go
fmt.Println(a[:3])  // [alpha bravo charlie]
fmt.Println(a[1:])  // [alpha bravo charlie]
fmt.Println(a[:])   // [alpha bravo charlie delta]
```

**Slices** have _reference semantics_, i.e. assignment or passing them as parameters doe snot result in a _copy_, rather it gets a _reference_ to the original **slice** and so changes to the target will change the source.

```go
  a := []string{"alpha", "bravo", "charlie", "delta"}
  fmt.Println(a)        // [alpha bravo charlie delta]

  b := a[1:3]
  // slice of 'a' from index '1' to '2'
  fmt.Println(b)        // [bravo charlie]
  b[0] = "Beta"         // modify 'b'

  fmt.Printf("'b' = %v\n", b) //'b' = [Beta charlie]
  fmt.Printf("'a' = %v\n", a) // 'a' = [alpha Beta charlie delta]
```

When we do need a _copy_ of a slice, we can do that using the **`copy`** function.

```go
a := []int{10, 20, 30}
fmt.Println(a)    // [10 20 30]

b := make([]int, 3, 3)
copy(b, a)
fmt.Println(b)    // [10 20 30]

c := make([]int, 5, 7)
copy(c, a)
fmt.Println(c)    // [10 20 30 0 0]

d := make([]int, 1, 3)
copy(d, a)
fmt.Println(d)    // [10]

e := []int {-1, -2, -3, -4, -5}
copy(e, a)
fmt.Println(e)   // [10 20 30 -4 -5]
```

If we **`copy`** to a _destination slice_ bigger (in **`len`**) than the _source slice_, then the extra elements in the _destination_ will remain unchanged.

If the _destination_ is smaller, then only as many elements as the _length_ of the _destination_ can fit will be copied over.

**`copy`** function will overwrite the corresponding elements in the _destination slice_.

**Iterating** over the items in a **slice** is the same as we do for **arrays**.

```go
for i, l := range e {
    fmt.Printf("e[%d] = %d\n", i, l)
  }
```

###### Resizing Slices

The main advantage of **slices** is that they are a dynamic collection and can be resized easily. So let us make a **slice** with **`1`** length and **`3`** capacity. Then try to set values into it.

```go
 a := make([]int, 1, 3)
 a[0] = 10
 a[1] = 20 // panic: runtime error: index out of range [1] with length 1

 fmt.Println(a[1]) // panic: runtime error: index out of range [1] with length 1
```

So we cannot _set_ a value to an index **`>=`** the **`len`** of the **slice**. Similarly we cannot __access__ an element at an index **`>=`** the **`len`**.

To **add** values beyond the **length** of the **slice** we use the **`append`** function.

```go
a := make([]int, 1, 3)
fmt.Printf("a = %v; len = %d; cap = %d\n",a, len(a), cap(a))
// a = [0]; len = 1; cap = 3

a = append(a, 10, 20)	// append 10 & 20 to this slice
fmt.Printf("a = %v; len = %d; cap = %d\n", a, len(a), cap(a))
// a = [0 10 20]; len = 3; cap = 3
// Note: 
//		- values get added after the 'last index'
//		- length increases
//		- capacity has remianed same

// append more than capacity
a = append(a, 30)
fmt.Printf("a = %v; len = %d; cap = %d\n", a, len(a), cap(a))
// a = [0 10 20 30]; len = 4; cap = 6
// Note:
//		- length increases
//		- capacity has doubled
```

The **`append`** function adds elements after the last index (**`len - 1`**), and increases the **`len`** by the number of items added. If we add beyond the _capacity_ , it automatically allocates a new **array** with double the _capacity_ and copies the elements over. This is all handled behind the scene by the function, and we get a new **slice**.

_Note: another interesting observation about the **`append`** function is that its second parameter takes a variable number of arguments. This capability is called **variadic** functions or arguments and is similar to other languages, for example **\*\*`kwargs`** in **`Python`** and **`...args: int[]`** in **`Typescript`**. We will see how to do this in **Go** later._

##### Maps

A **map** in **Go** is an _unordered_ collection of _key-value_ pairs. It is a common data structure, and often called by many names such as **dictionaries**, **associative arrays**, **hashes**, **hash-tables** in other languages such as **`Python`**, **`C/C++`** or **`Ruby`**. It serves as an in-memory _look up_, that allows us to store **values** against **keys** and, then access them in _constant time_ ( **O(1)** _time complexity_ ).

The **keys** can be any data-type that supports equality check, so _strings_, _integers_, _floating-point_ are all fine, but a _array_ cannot be a **key**.

We have a couple of ways to declare and create **maps**:

- Use the `make` function to create a type of `map` with _key_ as `string` and _value_ as `int`. The data-type of the _key_ is specified within `[]`.

```go
// a map of string keys and int values
scores := make(map[string]int)
// set values in the map
scores["Alice"] = 78
scores["Betty"] = 82
scores["Cindy"] = 89
```

- Use the _shorthand_ version (without `make`), mostly with **map** _literals_.

```go
// a map of string keys and int values with initialization
scores := map[string]int {
    "Alice": 78, 
    "Betty": 82,
    "Cindy":89,
}
```

- Of course we can use the _shorthand_ version even of we do not have values to initialise, and later set the key-values.

```go
// a map of string keys and int values initialized empty
scores := map[string]int{}

// set values later
scores["Alice"] = 78
```



To access a _value_ from a **map** we use the _key_ between `[]` (just like most other programming languages).

```go
p := "Alice"
fmt.Printf("%s has a score of %d\n", p, scores[p] )
// Alice has a score of 78
```

If no _value_ exists for the _key_ we are attempting to lookup (i.e. _hash miss_), then **Go** will return the **default value** (also called **zero value**) for the _value data-type_.

```go
p := "Jane"
fmt.Printf("%s has a score of %d\n", p, scores[p] ) 
// Jane has a score of 0
```

This is not always desirable, as we may need to know if the _key_ exists or not, and if it does then get the _value_ (sometimes the **zero value** might be a legitimate value so we cannot use that logic to check).  **Go** syntax allows us to return multiple values from a function, so the **map** _value_ reference operation actually returns _two_ items, the _first_ is the _value_ at that _key_ (or the **zero value** if the _key_ does not exist), and the _second_ is a _Boolean_ indicating if the _key_ was found or not. So in a single operation we can _check_ for a _key_ and _get_ the _value_ if it exists.

```go
p := "Jane"
s, ok := scores[p]		// returns -> value, boolean
if ok {
    fmt.Printf("%s has a score of %d\n", p, s )
} else {
    fmt.Printf("%s does not exist on record.", p)
}
// Jane does not exist on record.
```

_Note_: **Go** gives a shorthand for declaring and using variables directly in one step with the `if` statement. The scope of the variables will be within the `if - else` block.

```go
p := "Jane"
if s, ok := scores[p]; ok {		// declare 's' & 'ok' and use them in one step with the 'if' statement
      fmt.Printf("%s has a score of %d\n", p, s )
} else {
    fmt.Printf("%s does not exist on record.", p)
}
// Jane does not exist on record.
```



To _iterate_ over the items in a **map** we use the **`range`** keyword just like we did with **arrays** and **slices**.

```go
scores := map[string]int {
    "Alice": 78, 
    "Betty": 82,
    "Diana": 88,
    "Cindy":89,
}

for k, v := range scores{
    fmt.Printf("%s scored %d marks.\n", k, v)
}
/*
Cindy scored 89 marks.
Alice scored 78 marks.
Betty scored 82 marks.
Diana scored 88 marks.
*/
```

However, since **maps** are _unordered_, the iteration will not necessarily return a sorted order. To do that we could get the _keys_ into a _sorted_ **slice** and then iterate over the **slice** to access the **map** values.

```go
import (
  "fmt"
  "sort"
)

func main() {
    scores := map[string]int {
        "Alice": 78, 
        "Betty": 82,
        "Diana": 88,
        "Cindy":89,
    }

    var sortedNames []string
    for name, _ := range scores{
        sortedNames = append(sortedNames, name)
    }
    sort.Strings(sortedNames)
    // now we have a slice of sorted names

    for _, name := range sortedNames{
        // iterate through the sorted slice and use as key
        // access value from map using the sortd keys
        fmt.Printf("%s scored %d marks.\n", name, scores[name])
    }
}
/*
Alice scored 78 marks.
Betty scored 82 marks.
Cindy scored 89 marks.
Diana scored 88 marks.
*/
```

_Note: Here we import and use the **`sort`** library to help sort the **string slice**._

### User-Defined Data Types

Almost always, when programming and trying to represent entities in code we have to go beyond the _basic data types_, and their _collections_. To do this we can define our own data type, which are _composites_ of the _basic data types_.

In _object oriented programming_, the ability to represent _entities_ as _objects_ which have their _state_ maintained in _fields_ with _methods_ that can operate on this _state_ is ubiquitous.

_Go_ provides constructs such as **types**, **interfaces**, and **methods** to define our own data types and encapsulate actions that can be performed on them. It is however different from the classical _class based inheritance_ model we are normally familiar with from _C++_ and _Java_. 

##### Detour to Pointers

To pass around _variables_ to _user defined types_ we often have to use **pointers**. A **pointer** in _Go_ is just like what we have in _C/C++_, it is a _variable_ that holds the _address_ (memory location) of _another variable_. It is an indirect way to refer to another variable and we can easily change what the _pointer_ is pointing to by changing its _value_ to a _new location_ in memory. The advantages will become apparent as we go further.

We will see how to _define_, _assign_, and _reference_ **pointer** variables with and example.

```go
// defining a pointer 
var p *int
// 'p' is pointer to integer type

var i int = 13

// point 'p' to 'i'
p = &i
// 'p' holds address of 'i'

// access the value pointed to by 'p' with 'de-referencing'
fmt.Printf("Value pointed by 'p' = %d\n", *p)
// Value pointed by 'p' = 13

// change 'i'
i = 17

fmt.Printf("New value pointed by 'p' = %d\n", *p)
// New value pointed by 'p' = 17

// content of pointer varible 'p'
fmt.Printf("Content of pointer 'p' = %v\n", p)
// Content of pointer 'p' = 0xc0000b6010

```

###### Pointer Arithmetic

Unlike _C/C++_, we do not have _pointer arithmetic_ in _Go_. This makes it a much more memory safe language, as well as enables better _garbage collection_ implementation. That being said, _Go_ gives an "escape hatch" to use _unsafe_ mode and manipulate **pointers** at our own risk. To do this we have to import the "_unsafe_" module, and use the functions it provides.

```go
package main

import "fmt"
// import 'unsafe' operations library
import "unsafe"

func main() {

    nums := [...]int{10, 20, 30}

    // obtain an 'unsafe pointer' to 1st element
    pnum := unsafe.Pointer(&nums[0])

    // get size of the element data type
    nsize := unsafe.Sizeof(nums[0]) // or int(0)

    for i := 0; i < len(nums); i++ {
        // cast 'unsafe pointer' -> 'int pointer' & dereference
        item := *(*int)(pnum)

        // print ponter and value
        fmt.Printf("Value @ %v -> %d\n", pnum, item)

        // increment pointer by size and cast back as 'unsafe'
        pnum = unsafe.Pointer(uintptr(pnum) + nsize)
    }
    
}
/*
Value @ 0xc000012160 -> 10
Value @ 0xc000012168 -> 20
Value @ 0xc000012170 -> 30
*/
```

We can now modify the **pointer** by casting it as "_unsafe pointers_". Evidently this is quite difficult, and error prone. Therefore this is not idiomatic _Go_, and only to be used in extreme situations where we need this level of **pointer** manipulation and we know what we are getting into.



With that refresher to **pointers** covered, we can get back to our discussion on _user defined data types_.

##### Structs

In _Go_ we use **structs** to define composite data types. We can group together a bunch a data elements and give that entity a name, this is akin to the concept of an _object_ in other languages (particularly like _JavaScript_).

###### Defining Structs

To define a **struct** we use the **`type`** keyword followed by and **identifier** for the **struct name**, then the **`struct`** keyword and the **fields** (or data elements) within curly braces **`{}`**.

```go
// define struct 'rect' with fields 'height' & 'width' of type int
type rect struct{
  height int
  widht int
}
```

###### Initialising Structs

To initialise a **struct** object specify the values for the fields within **`{}`**, after the **struct** name. We can do this in two ways - **named initialisation** where we specify the _field name_ followed by **`:`** and the _value_, or **ordered initialisation**, if we know the order of the fields where we simply specify the field _values_ in the correct order.

```go
// named initialisation
r1 := rect{width: 23, height: 19}

// ordered initialisation
r2 := rect{19, 23}
```

###### Accessing Fields

We can use the dot **`.`** notation to access the fields of a **struct**.

```go
r1 := rect{width: 23, height: 19}
fmt.Printf("rect has width = %d & height = %d\n", r1.width, r1.height)
```

We can initialise an _Empty_ **struct** and then set the values if we wish to, the omitted fields will have the default _zero_ values depending on its data type.

```go
// empty initialisation
r1 := rect{}

fmt.Printf("rect has width = %d & height = %d\n", r1.width, r1.height)
// rect has width = 0 & height = 0

// set field values
r1.height, r1.width = 19, 23

fmt.Printf("rect has width = %d & height = %d\n", r1.width, r1.height)
// rect has width = 23 & height = 19
```

###### Anonymous Structs

If we want a temporary object for a local scope, it is possible to define an **anonymous struct**, that does not have a type name. We can define and initialise it in one step and use it the local scope.

```go
 // anonymous struct expression
s1 := struct {
    name string
    score int
}{
    "Alan",
    78,
}

// can declare in a single line if needed
s2 := struct {name string; score int}{
    "Betty", 89,
}

if s1.score > s2.score {
    fmt.Printf("%s scored more than %s.\n", s1.name, s2.name)
} else if s1.score < s2.score {
    fmt.Printf("%s scored more than %s.\n", s2.name, s1.name)
} else{
    fmt.Printf("%s and %s scored the same.\n", s1.name, s2.name)
}

// Betty scored more than Alan
```

**Anonymous structs** have _type compatibility_ with other **structs** as long as they have the same structure. This is because _Go_ has a **structural typing** approach in most situations, we shall see this more when we see **interfaces**.

```go
type rect struct{
    height int
    width int
}

type block struct{
    height int
    width int
}

func main() {

    // anonymous struct expression
    o := struct {
        height int
        width int
    }{
        10,
        15,
    }

    r := rect{10,15}
    b := block{10, 15}

    var comp string

    // compare 'rect' and anonymous struct
    if r == o {
        comp = "Same"
    } else
    {
        comp = "Different"
    }
    fmt.Printf("'r' and 'o' are %s.\n", comp)

    // compare 'block' & anonymous struct
    if b == o {
        comp = "Same"
    } else
    {
        comp = "Different"
    }
    fmt.Printf("'b' and 'o' are %s.\n", comp)

    /*
  Compile error - cannot cmpare struct rect with block
  if r == b {
    ...
  } else
  */
}
// 'r' and 'o' are Same.
// 'b' and 'o' are Same.
```

###### Nested Structs

Of course we can have **structs** nested within other **structs**.

```go
type Address struct{
    city string
    country string
}

type Person struct{
    name string
    age int
    // nested struct field
    address Address
}

func main() {

    p1 := Person{
        "Alan",
        27,
        // initialise nested struct values
        Address{
            "Madrid",
            "Spain",
        },
    }

    fmt.Printf("%s, aged %d lives in %s - %s.\n", 
               p1.name, p1.age, p1.address.city, p1.address.country)
    // Alan, aged 27 lives in Madrid - Spain.
}
```

###### Promoted Struct Fields

In the case of **nested structs**, we can define them so that their _fields_ get promoted to be accessed without having to qualify them with the internal _struct field names_. We do this by simply omitting the _field name_ for the **nested struct** field.

```go
type Address struct{
    city string
    country string
}

type Person struct{
    name string
    age int
    // nested promoted struct (anonymous) field
    Address
}

func main() {

    p1 := Person{
        "Alan",
        27,
        Address{
            "Madrid",
            "Spain",
        },
    }

    fmt.Printf("%s, aged %d lives in %s - %s.\n", 
               // access 'city' & 'country' fields dierctly
               p1.name, p1.age, p1.city, p1.country)
    // Alan, aged 27 lives in Madrid - Spain.
}
```

Of course we have to be careful to avoid **anonymous nesting** of two **structs** that might have any fields with the same name! The compiler will error that as an _ambiguous field_.

###### Function Fields

The fields of a **struct** is not restricted to being just data, they can be **functions** as well. The _type_ of the _field_ can be a **function type**.

```go
// define a 'type' for our function
type product func(arect rect) int 

// define struct type 
type rect struct {
    width int
    height int
    // field 'area' is a function type
    area product
}

func main() {

    // declare and initialise a rect struct
    r1 := rect{
        width: 23,
        height: 13,
        // define a function for this field
        area: func(arect rect) int{
            return arect.width * arect.height
        },
    }

    // invoke function field of sruct
    fmt.Printf("Area of rect = %d.\n", r1.area(r1))
    // Area of rect = 299.
}
```

Note that the **function field** is just a plain function, and not a **method** (it does not have any _implicit reference_ to the **struct instance** itself). We are passing in the arguments that we need.

Also we have the flexibility to define whatever function body we need when we declare and initialise the **struct instance**. We can change the value of the field to a different function (with the same signature) if we need.

If we want to reuse the same logic, we can declare a function and assign it to each **instance** of the **struct**.

```go
// define struct type 
type rect struct {
  width int
  height int
  // define function type in-place
  area func(rect) int
}
// define common function
func product(arect rect) int{
  return arect.width * arect.height
}

func main() {

  r1 := rect{
    23,
    13,
    // specify the 'product' function as the value
    product,
  }

  r2 := rect{
    56,
    43,
    // specify the 'product' function as the value
    product,
  }
  // invoke function field of sruct
  fmt.Printf("Area of rect = %d.\n", r1.area(r1))

  fmt.Printf("Area of rect = %d.\n", r2.area(r2))
  // Area of rect = 299.
}
```

There are shortcuts to define the **function type** directly with the **struct definition**, if we do not want to explicitly define a separate **function type**.

```go
// define struct type 
type rect struct {
    width int
    height int
    // define function type in-place
    area func(rect) int
}
```

Or we can define the entire **struct** definition to be **anonymous**.

```go
r1 := struct {width int; height int;
              // define a func type field on-the-fly
              area func(rect) int
             }{
    23,
    13,
    // declare the function impelementation
    func(arect rect) int{
        return arect.width * arect.height
    },
}
```

###### Pointer to Struct

Often we will want to deal with **pointers to struct**, we can do that simply by using the **`&`** to assign the address/reference to a **struct** to a **pointer variable**.

```go
r2 := &rect{
    46,
    26,
    product,
}

// access the fields by 'dereferencing' strct pointer
fmt.Printf("Width of rect = %d.\n", (*r2).width)

// Shorthand - direct access with struct pointer
fmt.Printf("Height of rect = %d.\n", r2.height)

// func 'product' needs param of type 'rect'
//   not 'pointer to rect' so need to 'dereference'
fmt.Printf("Area of rect = %d.\n", r2.area(*r2))
// Area of rect = 1196.
```

_Go_ allows us to directly access _fields_ of **struct pointer** variables without having to _dereference_ them.

Another way to create **struct pointers** is using the **`new`** keyword.

```go
r3 := new(rect)
r3.width, r3.height = 13, 23
r3.area = product
```

This is often used when we do _not initialise_ the **struct** as a literal, rather dynamically set the values.

###### Methods

Previously we saw how to make use a **function field** within a **struct**, but that does not make it a **method**. In order to define **methods** on **structs**, we define a **function** and then "**bind**" that to a **struct**. We do that by specifying the **struct name** as the **_receiver_** of the **function call** within parentheses **`()`** directly after the **`func`** keyword (it is like the **`self`** parameter in other languages like _Python_). 

```go
// define struct type 
type rect struct {
  width int
  height int
}

// method on struct 'rect'
// rect is the receiver/self-param
func (arect rect) area() int{
  return arect.width * arect.height
}

func main() {

  r1 := rect{
    width: 13,
    height: 23,
  }
 
  // invoke method on 'rect'
  fmt.Printf("Area of rect = %d.\n", r1.area())
}
```

let us say we want to modify the **struct**, we could try to mutate the _fields_ with a method.

```go
// define struct type 
type rect struct {
  width int
  height int
}

// method on struct 'rect'
// rect is the receiver/self-param
func (arect rect) area() int{
  return arect.width * arect.height
}

// method to increase the dimensions
func (arect rect) grow(width, height int){
  arect.width += width
  arect.height += height
}

func main() {

  r1 := rect{
    width: 13,
    height: 23,
  }
  // print current dimensions
  fmt.Printf("Width = %d; Height = %d\n", r1.width, r1.height)
  // Width = 13; Height = 23

  // increase size of rect
  r1.grow(10, 10)
  // print dimensions again
  fmt.Printf("Width = %d; Height = %d\n", r1.width, r1.height)
  // Width = 13; Height = 23	!!!
}
```

Looks like that did **not** work! The dimensions of the **`rect`** remained the same.

This is because **structs** in _Go_ have "**value semantics**" (like everything else in _Go_ in fact). When a **struct** is passed as an argument, the parameter receives a **copy** of the **struct**, and whatever changes we do to that **copy** within the function body is lost once we are out of the function scope.

When we wan to modify a **struct** in a method we should pass it as a **pointer**.

```go
// method to increase the dimensions
// bind to 'rect pointer'
func (arect *rect) grow(width, height int){
  arect.width += width
  arect.height += height
}

func main() {

  r1 := rect{
    width: 13,
    height: 23,
  }
  // print current dimensions
  fmt.Printf("Width = %d; Height = %d\n", r1.width, r1.height)
  // Width = 13; Height = 23

  // increase size of rect
  // actually gets called on a reference to rect
  r1.grow(10, 10)
  // print dimensions again
  fmt.Printf("Width = %d; Height = %d\n", r1.width, r1.height)
  // Width = 23; Height = 33
}
```

The only change we needed to do was in the **method definition** to change the **_receiver_** from **`rect`** to **`*rect`**.

Besides the need to modify the fields, it is generally a good practice to define the **method**, on the **pointer** to the **struct** as it is more efficient at runtime, since the whole **struct** does not need to be copied over, just the **pointer**.

###### No Constructors

For those of us coming from **class based OOP** languages, we should be aware that **structs** do **not** have the concept of a "**constructor**". If we want some initialisation to happen when a **struct** instance is created, we have to write our "**factory function**" within which we **create** the **struct**, **initialise** it with whatever we need and then **return** it (as a pointer).

```go
// function to create and return rect pointer
func newRect(width, height int) *rect{
  return &rect{ width, height }
}

func main() {
  // call function to create, initialise
  // and return rect pointer
  r1 := newRect(13, 23)

  fmt.Println(r1)
  // &{13 23}
  
}
```

###### Comparing Structs

_Go_ allows us to compare **structs** directly. Two **structs** are considered equal if all of their corresponding fields are equal.

```go
func main() {
  // call function to create, initialise
  // and return rect pointer
  r1 := newRect(13, 23)
  
  r2 := rect{13, 23}

  r3 := newRect(13, 23)

  fmt.Println(*r1 == r2)
  // true
  
  fmt.Println(*r1 == *r3)
  // true

  fmt.Println(r1 == r3)
  // false !! - comparing pointers (not the referenced structs)

  r4 := struct{width int; height int}{
    13,
    23,
  }
  fmt.Println(r2 == r4)
  // true - structural typing

  r5 := struct{height int; width int}{
    13,
    23,
  }
  fmt.Println(r2 == r5) // compiler error - type mismatch
}
```

As long as the fields are in the same order (layout), and type and have the same values **structs** are considered equal. The **name** of the **struct** is not relevant in this comparison, as _Go_ takes the approach of _structural typing_ (as opposed to _nominal typing_).

###### Exporting Structs

_Go_ uses a simple **convention** for specifying **visibility**, if an identifier starts with a "**Capital**" letter then it is **visible** outside its **package**. So if a **struct** name starts with a "**Capital**" letter then it is considered "**Exported**" from the **package** and can be accessed from outside. Similarly if the **fields** of the **struct** start with "**Capital case**" then these **fields** get "**Exported**" as well, otherwise they are not **visible** from outside the **package**.

```go
package login

// exported struct
type User struct {
	Id   int    // exported field
	Name string // exported field
	age  int    // private field
}

// local scoped struct
type address struct {
	city    string // cannot have exported fields
	country string
}

// use in another package

package main

import (
    "fmt"
    // import our package
    login "demo/login"
)

func main(){
    // Can access exported struct and fields - only
	u := login.User{Id: 1001, Name: "Alan"}

	fmt.Println(u)
}
```

This is a very simple pattern relying on the **naming convention** and does not require any **keywords**. We shall see more about **packages** and **modules** in a later section.

### Functions

###### Function Definition

In _Go_ we define **functions** using the **`func`** keyword (line **`def`** in _Python_, or **`fun`** in _Kotlin_). This is followed by the _name_ of the **function** and a set of _parameters_ in **`()`**, followed by the _return_ type and finally the _body_ enclosed within **`{}`**.

```go
func add(a int, b int) int{
  return a + b
}

// call the function
s := add(13, 34)
```

###### Omit Parameter Type (repeating)

If a _set of parameters_ have the same **type**, then we can just bunch them together (separated by **`,`**s) and specify the **type* _just once_. 

```go
func add(a, b int) int{
    return a + b
}
```

###### Multiple Return Values

_Go_ allows us to return _multiple values_ at once from a function. To specify these we have to enclose the _return types_ in **`()`** separated by **`,`**s.

```go
func sumdiff(a, b int) (int, int){
  return a + b, a - b
}

// call the function
s, d := summdiff(13, 34)
```

###### Named Return Parameters

We can specify a **name** for the **return parameters/variables** with the definition, then it becomes easier to assign values to them within the function and simply do a **`return`** to return those values. This is a shortcut.

```go
func sumdiff(a, b int) (s, d int){
    s, d = a + b, a - b	
    // Note we use '=' instead of ':='
    // We don't want to redeclare the varibles!!
  return
}
```

###### Recursive Functions

_Go_ allows us to invoke functions recursively (from within itself).

```go
// recursive factorial
func fact(n int) int {
    if n <= 0 {
        return 1
    } else {
        return n * fact(n - 1)
    }
}

// invoke factorial
fmt.Println(fact(20)) // 2432902008176640000
```

###### Variadic Functions

There are some situations where we cannot know beforehand the number of arguments we need to pass in. It may vary depending on the situation. A common example in most languages is the **`Printxx`** function, that takes any number of arguments and prints them separated (or formatted). Such function are called **variadic functions**. In _Go_ we can define a **variadic function** by "**prefixing `...`**" before the **parameter's type**. This is similar to **`*args`** & **\*\*`kwargs`** in _Python_ and exactly same syntax as in _TypeScript_!

```go
// variadic function that can take 
// indefinite number of int arguments
func sum(nums ...int) int{
  s := 0
  for _, e := range nums{
    // inside the function the variadic parameter
    // is accessed as a slice
    s += e
  }
  return s
}

// invoking variadic argument function
fmt.Println(sum(10, 20, 30, 40, 50))	// 150
```

If we want to invoke this with an existing **slice** or **array** use the **`...`** with the **argument** to **spread** it out, same syntax as _TypeScript_ where it is called "**spread operator**".

```go
// call with existing slice
nums := []int{10, 20, 30, 40, 50}
// spread out the slice
fmt.Println(sum(nums...))				// 150
```

The definition of **`Println`** in _Go_ is.

```go
func Println(a ...interface{})
```

This uses the concept of an "**empty interface type**" to be able to pass in any **type** of value. We will examine this later when we get to **interfaces**.

We can also mix, **non-variadic** (fixed parameters) along with **variadic** parameters as long as the **variadic** parameter comes last. This is how the **`Printf`** function is defined (the first parameter is **`format`**).

```go
func Printf(format string, a ...interface{})
```

###### Defer a Function

The **`defer`** statement **"defers"** the execution of the qualified function (call) till the enclosing function returns.

```go
func greet(){
  defer fmt.Println("deferred Hello!")
  fmt.Println("Hello from function.")
}
// Hello from function.
// deferred Hello!
```

The deferred call gets executed after the **`greet`** function completes.

The **deferred callas** are executed even if the enclosing function exits with a **panic**!!

```go
func greet(){
  defer fmt.Println("deferred Hello!")
  panic("error") // forced painic exit
  fmt.Println("Hello from function.")
}
// deferred Hello!
// panic: error
```

The **deferred call** completes then we see the **panic** error.

**Deferred calls** are executed in a **Last-In-First-Out (LIFO/Stack)** fashion. Also the parameters to the calls are evaluated immediately but the invocations happen later. It is like the calls are pushed on to a stack and then popped and invoked after the enclosing function returns. 

```go
func countDown(from int){
  for i := from; i > 0; i-- {
    // count downwards and call Println
    defer fmt.Println(i)
  }
  fmt.Println("Counting down from...")
}

// call function
countDown(5)

/*
Counting down from...
1
2
3
4
5
*/
```

We can see that the resulting **deferred calls** happen in reverse order to how we stacked them.

A **deferred call** has the ability to modify the **return value** of the outer function at the very last moment (even after the outer function **return** has executed). To do this the outer function should have a **named return parameter**, and we need to define an inner function body that can access and modify the **return parameter** of the outer function.

```go
func modifyReturn() (result string){
  // anonymous function with defer
  defer func(){
    // access named return param
    // of outer func & modify it
    result = "modified result"
  }() // invoked immediately
  return "original result."
}

// invoke the func and print the return
fmt.Println(modifyReturn())

// modified result
```

**Deferred calls** is not something very common in other programming languages and might be hard pressed to see its usefulness. In _Go_ it is typically used to make **clean up calls** (especially given that it will execute even if the outer function panics).

```go
func copyfile(srcName, dstName string) (written int64, err error){
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    // deferred call to close file 
    defer src.Close()
    
    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    // deferred call to close file
    defer dst.Close()
    
    return io.Copy(dst, src)
}
```

###### Parameters by Value & by Reference

In _Go_ all **function arguments** are really "**pass by value**", that is the function receives a copy of the variable/argument. Which implies that the changes we make to the argument within the function body will be lost once the function returns. If we want to modify the **original value**, we will have to pass in a **pointer** to the value(this means the function signature has to be such that it can accept **pointers**). The variables to some data types such as **slices** and **maps** are implicitly treated like "**pointers**" (they as **reference types**), so we do not explicitly have to make them pointers when when passing them to functions (as a complement to that scenario, if we do need to pass a **copy** of a **reference** data type we will have to use some **`copy`** function).

```go
func main(){
  u1 := user{ name: "Alice", active: false }
  fmt.Printf("Before toggleUser call, user.active = %t\n", u1.active)
  toggleUser(u1)
  fmt.Printf("After toggleUser call, user.active = %t\n", u1.active)
}

// func to change user status
func toggleUser(auser user){
  auser.active = !auser.active
  fmt.Printf("...Inside func, user.active = %t\n", auser.active)
}

/* Output
Before toggleUser call, user.active = false
...Inside func, user.active = true
After toggleUser call, user.active = false
*/
```

As we can see the modifications we did inside the function **`toggleUser`** is lost once we returned from it.

```go
func main(){
  u1 := user{ name: "Alice", active: false }
  fmt.Printf("Before toggleUser call, user.active = %t\n", u1.active)
  toggleUser(&u1)	//pass in address of the variable
  fmt.Printf("After toggleUser call, user.active = %t\n", u1.active)
}

// func to change user status
// accptes pointer to user
func toggleUser(auser *user){
  // modifying value pointed to by the pointer
  auser.active = !auser.active	
  fmt.Printf("...Inside func, user.active = %t\n", auser.active)
}

/* Output
Before toggleUser call, user.active = false
...Inside func, user.active = true
After toggleUser call, user.active = true
*/
```

Now the original value of the **`user`** **`struct`** variable is changed (instead of the **argument copy**).

```go
func main(){
  vals := [5]int{1, 2, 3, 4, 5}
  fmt.Printf("Before calling doubleValues: %v\n", vals)
  doubleValues(vals)
  fmt.Printf("After calling doubleValues: %v\n", vals)
}

// func to double values in an array
func doubleValues(values [5]int){
  for i := 0; i < len(values); i++{
    values[i] = 2 * values[i]
  }
}

/* Output
Before calling doubleValues: [1 2 3 4 5]
After calling doubleValues: [1 2 3 4 5]
*/
```

**Arrays** as we see are **passed by value**. If we want to modify the **array** we can pass it in as a **pointer**, but in practice we would simply pass in a **slice** of the **array**. 

```go
func main(){
  vals := [5]int{1, 2, 3, 4, 5}
  fmt.Printf("Before calling doubleValues: %v\n", vals)
  // invoke func with full slice to array
  doubleValues(vals[:])
  fmt.Printf("After calling doubleValues: %v\n", vals)
}

// func to double values in an array
// accepts a slice parameter
func doubleValues(values []int){
  // this will modify the underlying array
  for i := 0; i < len(values); i++{
    values[i] = 2 * values[i]
  }
}

/* Output
Before calling doubleValues: [1 2 3 4 5]
After calling doubleValues: [2 4 6 8 10]
*/
```

The only slight change that we did was to modify the function to accept a **slice** instead of an **array** and correspondingly call the function using a **full slice to the array**. With this the function will treat it like a **reference**, actually it receives a **copy** of the "**slice variable**", which points to the same underlying **backing array**, which indirectly gets modified via the **slice**.

This is the same behaviour we will see with **maps** as well.

**Passing by reference** is not just for modifying the original values (which can in fact be a bad practice to proliferate, it is better to adopt a **functional style** where have **pure functions** to the extent possible). There is a **cost to copy** values every time we invoke a function with arguments. If the variable is big in size and we are making a **pass by value** call, it will copy the entire data to the parameter in the function, which can be an expensive operation. **Passing by reference/pointers** can be a better option in such situations.

###### First-class Functions

_Go_ supports **first-class-functions**, i.e. functions are just like any other **value/data**, they can be **assigned to variables**, **passed as arguments** to other functions, or **returned as values** from functions. In this respect it is similar to other languages (like _JavaScript_) that support some **functional programming concepts**, which enable us to write powerful and elegant code.

**Assign function** to variable.

```go
func add(a int, b int) int{
  return a + b
}

func prod(a, b int) int{
  return a * b
}

/// ... assign these functions to some variable

op := add
fmt.Printf("'op (%d, %d) = %d'\n", 3, 5, op(3, 5))
// 'op (3, 5) = 8'

op = prod
fmt.Printf("'op (%d, %d) = %d'\n", 3, 5, op(3, 5))
// 'op (3, 5) = 15'
```

The `op` variable holds the "**function**", and we can invoke the function using it. We can also change the value of the variable to point to another function (_as long as the function signatures match_).

###### Anonymous Function

When we use a **first-class function**, we can define it on the fly (or in place) and ignore the name (if we do not need to access it by name). We could rewrite the above example as.

```go
// define an anonymous function
op := func (a, b int) int {
    return a + b
}
fmt.Printf("'op (%d, %d) = %d'\n", 3, 5, op(3, 5))
// 'op (3, 5) = 8'

// another anonymous function
op = func (a, b int) int {
    return a * b
}
fmt.Printf("'op (%d, %d) = %d'\n", 3, 5, op(3, 5))
// 'op (3, 5) = 15'
```

###### Function Type

Since **functions** are treated same as **values** in _Go_, every function has a "**type**" associated with it. We can see the type of our anonymous function like we do for regular **data** using the **`reflect`** module.

```go
op := func (a, b int) int {
    return a + b
}
fmt.Printf("Type of func 'op' = %v\n", reflect.TypeOf(op))
// Type of func 'op' = func(int, int) int

op = func (a, b int) int {
    return a * b
}
fmt.Printf("Type of func 'op' = %v\n", reflect.TypeOf(op))
// Type of func 'op' = func(int, int) int
}
```

Both functions have the same **type** viz. **`func(int, int) int`**.

We can declare a **custom type**, which is a **function type**, and then use that as the **type** for **variables** and **parameters**.

```go
// custom type of fucntion type
type intoperation func(int, int) int

// declare variable of function type
var op intoperation

// assign named fucntion to variable
op = add
fmt.Printf("Type of func 'op' = %v\n", reflect.TypeOf(op))
// Type of func 'op' = func(int, int) int


// assign anonymous function to variable
op = func (a, b int) int {
    return a * b
}
fmt.Printf("Type of func 'op' = %v\n", reflect.TypeOf(op))
// Type of func 'op' = func(int, int) int
```

###### Higher-Order Functions

**Higher-order functions** are functions that can _accept other functions_ as parameters (typical use case as _predicates_ to filter functions) or return _functions_ as a result (typically function factories).

The code snippet below shows an example of a function that accepts other functions and, in effect produces different results to the same input.

```go
// A generic function to calculate averages
// takes two fucntions as parameters
// 'comb' - func defining how to combine the values
// 'norm' - func defining how to normalize the result
func average(
  comb func(int, int) int, 
  norm func(int, int) float64, 
  nums ...int) float64 {
    // number of elements in args
    l := len(nums)
    r := 0.0
    if l > 0{
      c := nums[0]
      for i := 1; i < l; i++{
        // combine the elements as defined by 'comb'
        c = comb(c, nums[i])
      }
        // normalize the combine value
      r = norm(c, l)
    }
    return r
}

// ...
// code that uses the 'average' function

// calculate arithmetic mean - pass in 'add', and 'divide' funcs
aM := average(add, div, 1, 2, 3, 4, 5)
fmt.Printf("Arithmetic mean = %f\n", aM)
// Arithmetic mean = 3.000000

// geometric mean - pass in 'product' and 'nth root' funcs
gM := average(prod, 
              func(a, l int) float64{
              	return math.Pow(float64(a), 1.0 / float64(l))
              }, 
              1, 2, 3, 4, 5)
fmt.Printf("Geometric mean = %f\n", gM)
// Geometric mean = 2.605171
}

// ...
// helper functions used above
func add(a int, b int) int{
  return a + b
}
func prod(a, b int) int{
  return a * b
}
func div(a, l int) float64{
  return float64(a) / float64(l)
}
```

**`average`** is a very interesting function. It accepts two functions **`comb`** and **`norm`** that can be used to define how the `int` values passed in should be _combined_ and _normalised_. Then we are able to use the same function to calculate _arithmetic mean_ and _geometric mean_, simply by changing the way the values are _combined_ and _normalised_ - for _arithmetic mean_ we **`add`** the values and **`div`** by the number of values, whereas for _geometric mean_ we take the **`prod`** of the values and then take the **nth root`** of the result.

This gives as very powerful way to abstract away common code, break it up into reusable functions that can be combined in different ways.

Another common example would be to filter a collection dynamically based on different criteria.

```go
// function to filter int slice based on predicate
func filter(nums []int, pred func(int) bool) []int{
  var res []int
  for _, n := range nums{
    if pred(n){
      res = append(res, n)
    }
  }
  return res
}

// using 'filter' to get filter only odd numbers
ns := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
odds := filter(ns, func (x int) bool {
    return x % 2 != 0
})
// using 'filter' again to get only nums > 5
bigOdds := filter(odds, func(x int) bool{
    return x > 5
})
fmt.Printf("Big Odds are: %v\n", bigOdds)
// Big Odds are: [7 9]
```

We are able to use a common **`filter`** function to filter a **slice** of integers differently using different **predicates**.

###### Closures

A **closure** is a _function_ that is returned from another function. The function that creates and returns the _closure function_ can be thought of as a _function factory_ (there is nothing inherently special about that it, just a _higher order function_ that has a **function type** as a _return type_). The interesting thing about **closures** is that they can "**enclose**" _outer variables_ and retain a reference to them even after they are outside their original scope of declaration.

```go
// HOF returns a 'counting fucntion'
// return type is a function type
func getCounter(start int) func()int {
  i := start - 1
  // return an anonymous fucntion
  return func () int {
    // refernce outer variable
    i++
    return i
  }
}

// ...

// use 'getCounter' to get some counter function
cntr := getCounter(10)
fmt.Printf("counter = %d\n", cntr())	// counter = 10
fmt.Printf("counter = %d\n", cntr())	// counter = 11
```

When a **closure** encloses outer referenced variables it becomes a **statefull function**, it is no longer **idempotent**.

Whilst **closures** can be very handy tools, they have to be used carefully and possibly in _localised scope_. It is not a good practice to have long lived _statefull functions_ as the program behaviour will be harder to determine or troubleshoot. Sometimes the results may not be intuitive, especially if the enclosed variable/s are modified.

```go
// returns a slice of 'counter functions'
func counters(n int) []func() int{
  var cs []func() int

  for i := 1; i <= n; i++{
    cs = append(cs, func() int{
      // closure references loop variable 'i'
      return i
    })
  }
  return cs
}
// ...
// iterate though the slice of closures & invoke them
for _, f := range counters(5){
    fmt.Printf("f() = %d\n", f())
}
/*
f() = 6
f() = 6
f() = 6
f() = 6
f() = 6
*/
```

Well that is not quite what we had expected! Each **closure** instance in the **slice** references the same **loop variable**, which gets modified by the end of the loop to **`6`**. If we wanted each **closure instance** to have its own **enclosed variable**, we can modify the code slightly.

```go
// returns a slice of 'counter functions'
func counters(n int) []func() int{
  var cs []func() int

  for i := 1; i <= n; i++{
      // additional variable 'j' in loop scope
      j := i
      cs = append(cs, func() int{
      // closure references variable 'j'
      return j
    })
  }
  return cs
}
// ...
// iterate though the slice of closures & invoke them
for _, f := range counters(5){
    fmt.Printf("f() = %d\n", f())
}
/*
f() = 1
f() = 2
f() = 3
f() = 4
f() = 5
*/
```

Now each **closure** gets its own _copy_ of the enclosed variable (**`j`**).

### Interfaces

Similar to how we use **structs** for **encapsulation** in _Go_, we use **interfaces** to achieve **polymorphism**. A _Go_ **interface** is like in other languages, a **contract** that defines a set of **behaviours** as **function signatures**. Other types (like **structs**) can then **implement** the **interface** by providing **method implementations** for the **function signatures** specified in the **interface**. By doing so, we can treat that **struct** as **equivalent** to this **interface** (with respect to its supported behaviour). Since different **structs** can provide their own implementations for the **interface**, it gives us a mechanism for **polymorphic** behaviour.

Unlike most other languages (such as _Java_, _C#_, _Typescript_) where a **type** explicitly declares the intent of implementation clear (for example in _Typescript_ we might have `class StreamReader implements IReader`), in _Go_ the **type** simply has to implement the **functions** of the **interface** as **methods**, and _Go_ will figure out that the **type** is compatible with that **interface**.

```go
package main

import (
  "fmt"
  "math"
)

// an interface defining some shape behaviour
type Shape interface{
  Area() float64
}

type Rectangle struct{
  Length, Breadth float64
}

// implement the 'Shape' functoins for 'Rectangle'
func (r Rectangle) Area() float64{
  return r.Length * r.Breadth
}

type Circle struct{
  Radius float64
}

// implement the 'Shape' functoins for 'Circle'
func (c Circle) Area() float64{
  return math.Pi * c.Radius * c.Radius
}

// function - take two shape and find bigger
func BiggerShape(s1, s2 Shape){
  b := s1
  if s2.Area() > s1.Area(){
    b = s2
  }
  fmt.Printf("%v is bigger, with area = %f\n", b, b.Area())
}

func main() {
  c1 := Circle{Radius: 13}
  r1 := Rectangle{Length: 26, Breadth: 26}

  BiggerShape(c1, r1)
}
// {13} is bigger, with area = 530.929158
```

The function **`BiggerShape`** takes two **`Shape`** parameters and finds the bigger one based on the **`Area`**. Since both **`Rectangle`** and **`Circle`** implement the functions of the the interface **`Shape`**, instances of both these types are **"equivalent"** to a **`Shape`**, i.e. any operation that accepts a **`Shape`** will be able to accept either of these structs.

Note, that there is no _explicit notation_ indicating that **`Rectangle`** or **`Circle`** is a **`Shape`**. It is implied, and _Go_ can decipher that. This is similar to _TypeScript_, where any **type** that implements the **methods** of an **interface** is compliant with it.

To be compatible with an **interface** a type has to implement **all it's functions**, if any **function** is not implemented and we try to assign or pass it as a variable of that **interface type**, we will get a compile time error. If we modify our **`Shape`** interface to add another function, then to be "equivalent" to it, the **structs** will also have to implement those as methods.

```go
// interface defining some shape behaviours
type Shape interface{
  Area() float64
  Perimeter() float64
}

type Rectangle struct{
  Length, Breadth float64
}

// To be considered as 'Shape, struct 'Rectangle' has to implement both functions

// implement the'Shape' functoins for 'Rectangle'
func (r Rectangle) Area() float64{
  return r.Length * r.Breadth
}

// implement 'Perimeter' function 
func (r Rectangle) Perimeter() float64{
  return 2 * (r.Length + r.Breadth)
}
```

Since _Go_ avoids a **class** or **inheritance** based approach, it relies heavily on **interfaces** for defining types and contracts. We will look a couple of examples that are used the standard library.

##### `Stringer` Interface

The **`Stringer`** **interface** is defined in the **`fmt`** package. Whenever we pass a type to a **print** function, it invokes the **`String`** method that the type implements of this **interface**. This is how it know how to print the type value.

```go
// definition of 'Stringer'interface
type Stringer interface {
    String() string
}
// 'print' functions will call the 'String()' method
```

This is a common pattern, in most programming languages. For example the **`Object.prototype.toString()`** method in _JavaScript_, or the **`Object.ToString()`** method in _C#_. However in both these cases, it relies on **Protypal Inheritance** (_JS_) or **Class based Inheritance** (_C#_). In _Go_ it is simpler and more decoupled through the **`Stringer`** interface. 

We can use the **`Stringer`** interface to customise the way, objects of our types are printed. Let us get a more friendly way to display our **structs** from the above example.

```go
// implement 'String' func from 'Stringer' for 'Rectangle'
func (r Rectangle) String() string {
  return fmt.Sprintf("Rectangle { Length: %.2f, Breadth: %.2f }", 
        r.Length, r.Breadth)
}

// implement 'String' func from 'Stringer' for 'Circle'
func (c Circle) String() string {
  return fmt.Sprintf("Circle { Radius: %.2f }", c.Radius)
}

// function - take two shape and find bigger
func BiggerShape(s1, s2 Shape){
  b := s1
  if s2.Area() > s1.Area(){
    b = s2
  }
  fmt.Printf("%v is the bigger, with area = %f\n", b, b.Area())
}

func main() {
  c1 := Circle{Radius: 13}
  r1 := Rectangle{Length: 13, Breadth: 13}

  BiggerShape(c1, r1)

}

// output 
// Circle { Radius: 13.00 } is the bigger, with area = 530.929158
```

We can see that the **`Printf`** function now automatically calls the modified **`String`** methods of our **structs** to gives a custom formatted output.

###### The Empty Interface

Since _Go_ is statically typed language, we need a way to represent data for which we do not know the type ahead of time (a common example is the **`Printx`** functions). The way to do that is using the **`interface{}`** (_Empty Interface_) type. Since this does not have any **functions**, _all types_ in _Go_ can satisfy the **empty interface**. We can think of it as `java.lang.Object` in _Java_ or as the `any` type in _TypeScript_.

```go
// 'a1' of type 'empty interface'
var a1 interface{};

// set 'a1' to string
a1 = "Some string value!"
fmt.Println(a1)
// Some string value!

// set 'a1' to float
a1 = 22.0/ 7.0
fmt.Println(a1)
// 3.142857142857143

// set 'a1' to 'Circle'
a1 = Circle{Radius: 3}
fmt.Println(a1)
// Circle { Radius: 3.00 }
```

###### Type Assertion

_Go_ has a first class operation to check for **types** at runtime. This is especially useful when dealing with **`interface{}`** values. The syntax is **`variable_name.(Type)`** with which we can **assert** if a value is of a certain type or not.

```go
// print len & breadth of shape rect
func LengthBreadth(val Shape) {
  // type assertion, check shape is rectangle
  r := val.(Rectangle)
  // 'r' will be of type 'Rectangle'
  fmt.Printf("length = %.2f; breadth = %.2f\n",r.Length, r.Breadth)
}

// invoke function with a 'Rectangle' object
r1 := Rectangle{Length: 13, Breadth: 13}
LengthBreadth(r1)
// length = 13.00; breadth = 13.00
```

If the function receives a value of the type `Rectangle`, the **type assertion** operation checks and casts it to a variable of that type and then we can use it as a `Rectangle`.

One issue with this check however is that if the actual type of the value is not a `Rectangle` the program will `Panic` and exit.

```go
// invoke function with a 'Circle' object
c1 := Circle{Radius: 13}
LengthBreadth(c1)
// panic: interface conversion: main.Shape is main.Circle, not main.Rectangle
```

To handle this possibility we simply modify the left hand side of the assignment of the **type assertion** operation. The operation (like any other _Go_ operation that can potentially result in an error), supports **two** return values. The first is the **value** itself and the second is a **boolean** that indicates if the type assertion is true or false.

```go
func LengthBreadth(val Shape) {
  // type assertion with error chechk
  r, isR := val.(Rectangle)
  if isR {
    fmt.Printf("length = %.2f; breadth = %.2f\n",r.Length, r.Breadth)
  } else {
    fmt.Printf("Shape is not a Rectangle!\n")
  }
}
// invoke function with a 'Circle' object
c1 := Circle{Radius: 13}
LengthBreadth(c1)
// Shape is not a Rectangle!
```

Just by changing that assignment to two variables we can avoid the program from `Panic` and handle the error situation gracefully.

The interesting thing is that, the act of receiving two return variables instead of one modifies the behaviour of the operation! (It is not because we checked the **boolean**). We can see that if we simply replace the **`isR`** with a placeholder.

```go
func LengthBreadth(val Shape) {
  // type assertion with empty error chechk
  r, _ := val.(Rectangle)
  fmt.Printf("length = %.2f; breadth = %.2f\n",r.Length, r.Breadth)
}
// invoke function with a 'Circle' object
c1 := Circle{Radius: 13}
LengthBreadth(c1)
// length = 0.00; breadth = 0.00
```

Now we don't get a `Panic` but we get some unexpected error which can be more troublesome!! So always use the **assertion error** check pattern and handle the error conditions explicitly.

###### Type Switch

Since we may typically end up doing **type assertion** against multiple types, _Go_ provides a handy syntax with the **type switch** construct.

```go
// demo func - print info based on type
func PrintInfo(val interface{}){
  switch val.(type){
    case int:
      fmt.Printf("Type: int; Value = %d\n", val.(int))
    case float64:
      fmt.Printf("Type: float64; Value = %0.2f\n", val.(float64))
    case string: 
      fmt.Printf("Type: string; Value = %s\n", val.(string))
    case Shape:
      fmt.Printf("Type: Shape; Area = %0.1f\n", val.(Shape).Area())
    default:
      fmt.Printf("Unhandled type for value = %v\n", val)
  }
}

// invoke with varied types
PrintInfo(23)			// Type: int; Value = 23
PrintInfo(32.0)			// Type: float64; Value = 32.00
PrintInfo("Bonjour")	// Type: string; Value = Bonjour
PrintInfo(c1)			// Type: Shape; Area = 530.9
PrintInfo(true)			// Unhandled type for value = true
```



### Concurrency

