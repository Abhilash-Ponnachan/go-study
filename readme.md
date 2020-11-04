# Go Study Notes



### About Go



#### Go Playground

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
- Within the **`workspace`** we should have a **`src`** for all the source code. It would also have **`bin`** and **`lib`** for compiled binaries (executables and libraries).
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

