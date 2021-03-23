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

  - The problem here is that my project is not in a `Go` **`workspace`**. It is in a path `~/tech/go-study` that is outside of it. There are TWO was to fix this problem:

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

<To Do>

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



