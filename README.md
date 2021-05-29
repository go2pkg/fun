# go2pkg/fun

Functional programming experiment with Go2 Generic, which is planned for being
[released in go1.18 beta](https://blog.golang.org/generics-proposal).

Currently, the code is written with `.go2` extension and built by running
`go2go`. When Go generic is available in standard commands, file extension will
be renamed from `.go2` to `.go`.

## Using go2

`go2go` is a command provided by Go team to experiment with Go2. It's a
transition tool for working with Go2 when it's still in development. When Go2 is
officially released, `go2go` will be no longer needed: developers should be able
to use standard go commands to build and execute Go2 directly without `.go2`
extension.

### Install go2go

To install `go2go`, follow the following steps:

1. Clone the go repository into a local directory (called `goroot`):
   ```sh
   g clone https://go.googlesource.com/go goroot
   ```

2. Checkout the `dev.go2go` branch:
   ```sh
   cd goroot
   git checkout dev.go2go 
   ```

3. Navigate to the `src` directory and compile the Go SDK:
   ```sh
   cd src
   ./all.bash
   ```

   When everything is ok, the output should look like following (tested on Linux
   and Mac using go1.16):

   ```
   Building Go cmd/dist using /usr/local/go. (go1.16.3 darwin/amd64)
   Building Go toolchain1 using /usr/local/go.
   Building Go bootstrap cmd/go (go_bootstrap) using Go toolchain1.
   Building Go toolchain2 using go_bootstrap and Go toolchain1.
   Building Go toolchain3 using go_bootstrap and Go toolchain2.
   Building packages and commands for darwin/amd64.
   
   ##### Testing packages.
   
   ...
   
   ALL TESTS PASSED
   ---
   Installed Go for darwin/amd64 in /path/to/goroot
   Installed commands in /path/to/goroot/bin
   *** You need to add /path/to/goroot/bin to your PATH
   ```

4. Set `GOROOT` env:

   ```sh
   export GOROOT=/path/to/goroot
   ```

5. Finally, create an alias for quickly calling it:
   ```sh
   alias go2="/path/to/goroot/bin/go tool go2go"
   ```

### Use go2go to run code

Now, let's try using `go2go` to run your code.

1. Create a sample `.go2` file with following code:

   ```go
   // sample.go2
   package main
   
   import (
       "fmt"
       "strconv"
       "strings"
   )
   
   func Map[T1, T2 any](s []T1, f func(T1) T2) []T2 {
       s2 := make([]T2, len(s))
       for i, si := range s {
           s2[i] = f(si)
       }
       return s2
   }
   
   func main() {
       s1 := Map([]int{0, 1, 2, 3}, strconv.Itoa)
       s2 := Map(
           strings.Split("3,2,1,0", ","),
           func(s string) int {
               i, _ := strconv.Atoi(s)
               return i
           },
       )
       fmt.Println(s1)
       fmt.Println(s2)
   }
   ```

2. And run it:

   ```sh
   go2 run ./sample.go2
   ```

   If everything works, it should output:

   ```
   [0 1 2 3]
   [3 2 1 0]
   ```

### Use go2go to build package

You can also build the `.go2` package to generate corresponding `.go` files, by
calling `go2 build` inside the package directory:

```sh
git clone https://github.com/go2pkg/fun
cd fun/mathz
rm *.go                   # remove generated files  
go2 build
```

This will generate corresponding `.go` files.

## Read more

- [A Proposal for Adding Generics to Go](https://blog.golang.org/generics-proposal) from The Go Blog
- [Type Parameters Proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md) by Ian Lance Taylor
