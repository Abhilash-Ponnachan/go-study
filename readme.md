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
     fmt.Println(len(b)) 						// 3
    ```

    Here `∑` is only one character but it requires **3 bytes** () to represent in **UTF-8**, `len` will return **3** !!

  - **Concatenation**

  - **Substring**

  - **Formatting**

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

#### Loops



