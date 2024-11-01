# Golang

## Modules

Go commands run in Module-aware mode or `GOPATH` mode

- Module-aware mode (default in Go 1.16+): go command uses `go.mod` files to find versioned dependencies, and it typically loads packages
  out of the module cache (`GOPATH/pkg/mod`)
- `GOPATH` mode: go command ignores modules; it looks in `vendor` directories and in `GOPATH` to find dependencies
  (If `GOPATH` is not set, it defaults to `~/go`)

### Vendoring

Vendoring is used to allow interoperation with *older* versions of Go, or to ensure that all files used for a build are stored in a single file tree. Potential security benefits as you don't pull in a complete module, just the files that you use.

`go mod vendor` command creates a `vendor` directory containing copies of all packages needed to build and test packages in the main module.

### Creating Modules

1. `mkdir paulcoghlan.com/hello`
2. `go mod init paulcoghlan.com/hello` -> `go.mod` file created
3. `go mod tidy` -> `go.sum` created and remote modules downloaded to `$GOPATH/pkg/mod`

See [https://go.dev/blog/using-go-modules]

- Go programmers typically keep all their Go code in a single workspace

## CLI

Use `./...` to wildcard sub directories:

```sh
go test ./...

staticcheck ./...
```

### Test

Test specific function: `go test -run  <function_name_regexp>`

## Lint

- `go vet` - built-in
- [https://staticcheck.io] `go install honnef.co/go/tools/cmd/staticcheck@latest`
- [https://github.com/golangci/golangci-lint]

## Types

- Basic (bool, number, string)
  - float32 provides approximately 6 decimal digits of precision
  - float64 provides about 15 digits, so prefer using them
    - Perform * and / before + and - for accuracy
  - `math.MaxInt` and `math.MinInt` are limits
  - strings are immutable
  - The built-in len function returns the number of bytes (not runes) in a string, and the index operation s[i] retrieves the i-th byte of string s
  - UTF-8 may use 1-4 bytes per rune
  - As strings are immutable, manipulting strings incrementally can involve a lot of allocation and copying - use bytes.Buffer instead
- Aggregate (arrays/structs)
  - Arrays are fixed length! so not commonly used
  - Slices point to an underlying array
- Reference (pointers, slices, maps, channels≈)
- Interface: note empty Interface type `interface{}`, can have any value assigned to it as no contracts specified
- `==` and `!=` work on all basic types, pointers, channels and structs.  Use `reflect.DeepEqual()` for everything else

### Type conversions

Convert explicitly with error handling:

```go
str, ok := value.(string)
if ok {
    fmt.Printf("string value is: %q\n", str)
} else {
    fmt.Printf("value is not a string\n")
}
```

### Type-checking

Check types dynamically, eg. is `sw` a `stringwriter`:

```go
    type stringWriter interface {
        WriteString(string) (n int, err error)
    }
    if sw, ok := w.(stringWriter); ok {
        return sw.WriteString(s) // avoid a copy
    }
```

Or in a switch using the `type` keyword:

```go
switch x.(type) {
    case nil:       // ...
    case int, uint: // ...
    case bool:
    case string:
    default:
}
```

## Strings

A raw string literal is written `...`, using backquotes instead of double quotes. Within a raw string literal, no escape sequences are processed;
the contents are taken literally

Because strings are immutable, building up strings incrementally can involve a lot of allocation and copying. 
In such cases, it’s more efficient to use the `bytes.Buffer` type.

To convert an integer to a string, one option is to use `fmt.Sprintf()` or `strconv.Itoa()`
To parse a string representing an integer, use the `strconv.Atoi()` or `ParseInt`/`ParseUint`

Remember that the `bytes` package offers the same operations as the `strings` package can help avoid extra byte/string conversions.

Substring operations may return string with same backing array. `strings.Clone()` returns a new string.

## Arrays

Go treats arrays like any other basic/aggregate type and does *copy* by value when calling a function (might be inefficient for large arrays).

## Naming

- Uppercase names exported
- Package names are lowercase single-word
  - Function names starting with an uppercase letter are exported: keep them to a minimum!
  - Use aliases to avoid name conflicts
- Go doesn't provide automatic support for getters and setters. *Don't* put `Get` into name, but *do* put `Set`!
- Use “camel case” when forming names by combining words; that is, interior capital letters are preferred over interior underscores.
- One-method interfaces are named by the method name plus an `-er` suffix
- The importer of a package will use the name to refer to its contents, so exported names in the package can use that fact to avoid repetition e.g.
  in `bufio` package we use `Reader`, not `BufReader`, because users see it as `bufio.Reader`
- Similarly, the function to make new instances of would normally be called `NewRing`,
  but since `Ring` is the only type exported by the `ring` package, and since the package is called ring, it's called just `New`

## Conditionals

Align happy-path with the left, so easiest to read.  Failure cases are aligned on the right!

When an if statement doesn't flow into the next statement—that is, the body ends in break, continue, goto, or return — the unnecessary else is omitted.

## Formatting

`gofmt` does all the formatting for you
Uses tabs, not spaces

In `a :=` declaration a variable `v` may appear even if it has already been declared, making it easy to use a single err value, for example, in a long if-else chain

## Equality

```go
var n1 = []byte{1, 2, 3}
bytes.Compare(n1, n2)
reflect.DeepEqual(n1, n2)
```

## Looping

```go
for init; condition; post { }

for ; while_var == true; { }
```

If you're looping over an array, slice, string, or map, or reading from a channel, a `range` clause can manage the loop.

If you only need the first item in the range (the key or index), drop the second:

```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

If you only need the second item in the range (the value), use the blank identifier, an underscore, to discard the first:

```go
sum := 0
for _, value := range array {
    sum += value
}
```

`break` - terminates inner *for* or *while* loop

`continue` - skips current iteration but *continues* loop

functions and methods can return multiple values

The return or result "parameters" of a Go function can be given names and used as regular variables

Go's `defer` statement schedules a function call (the deferred function) to be run immediately before the function executing the defer returns
a single deferred call site can defer multiple function executions

Deferred functions are executed in `LIFO` order

## Var

A `var` declaration creates a variable of a particular type, attaches a name to it, and sets its initial value. 
If the expression is omitted, the initial value is the zero value for the type, which is 0 for numbers, false for booleans, "" for strings, and `nil` for interfaces and reference types (slice, pointer, map, channel, function).

The zero-value mechanism ensures that a variable always holds a well- defined value of its type; in Go there is no such thing as an uninitialized variable.

### Local Variables

Local variables have dynamic lifetimes: a new instance is created each time the declaration statement is executed, and the variable lives on until it 
becomes unreachable, at which point its storage may be recycled.

Each function can potentially be the start or root of a path to the variable in question, following pointers and other kinds of references that ultimately lead to the variable. If no such path exists, the variable has become unreachable and can be recycled.

A compiler may choose to allocate local variables on the heap (reachable) or on the stack (not reachable).  e.g. if a variable is returned it is allocated
from the heap.

### Package-level Variables

During initialisation:

1. All the constant and variable declarations in the package are evaluated
2. The `init()` functions are executed

- Package level variables have the same lifetime as the program.
  - Keeping unnecessary pointers to short-lived objects within long-lived objects, especially global variables, will prevent the garbage collector from reclaiming the short-lived objects.
gety

## New

`new(T)` allocates *zeroed* storage for a new item of type T and returns its address
The *zero* value of each type may be used *without further initialization* as composite literal - an expression that creates a new instance each time it is evaluated
It's OK to return the address of a local variable; the storage associated with the variable survives after the function returns

- `make(T, size, capacity [optional])` serves a purpose different from `new(T)`. It creates slices, maps, and channels only, and it returns an *initialized (not zeroed)* value of type `T` (not `*T`)

## Pointers

- A pointer value is the address of a variable
- If a variable is declared `var x int`, the expression `&x` *(“address of x”)* yields a pointer to an integer variable, that is, a value of type `*int`
- The expression `*p` yields the value of that variable, an int, but since `*p` denotes a variable, it *may* also appear on the *left-hand side* of an assignment, in which case the *assignment updates the variable*.

## Scope

When the compiler encounters a reference to a name, it looks for a declaration, starting with the innermost enclosing lexical block and working up to the universe block.

If a name is declared in both an outer block and an inner block, the inner declaration will be found first. In that case, the inner declaration is said to *shadow* or *hide* the outer one, making it *inaccessible*.

## Assignment

### Tuple Assignment

- Right-hand evaluated before variables updated:

e.g. swap values:

```go
x, y = y, x

func (a ByAge) Swap(i, j int)      { 
    a[i], a[j] = a[j], a[i] 
}
```

## Slices

- Wrap arrays, used more than arrays
- *Danger*: slices *hold references to an underlying array*, and if you assign one slice to another, both refer to the same array
- `capacity` is the size of the underlying array
- `func append(slice []T, elements ...T) []T` - append the elements to the end of the slice and return the result.
  - Slice argument isn't updated so you need to assign the result of the function
  - `capacity` needs to be big enough or new array allocated (expensive for GC)
  - append works on `nil` slices
  - Danger: if the resulting slice has a length smaller than its capacity, append can mutate the original slice,
    so you might want to `copy()` or use full slice expression
- Declared using empty brackets vs. Arrays which have capacity part of type
- `[:2]` is first two items inclusive
- `[2:]` is third items onwards
- The size of an array is part of its type. The types `[10]int` and `[20]int` are distinct
- If you have a slice or pointers, set unused pointers to `nil` so they are GC-ed

## Structs

The name of a struct field is exported if it begins with a capital letter; this is Go’s main access control mechanism.
A struct type may contain a mixture of exported and unexported fields.

If a type exists only to implement an interface and will never have exported methods other than the interface, do not export the type itself

Exporting just the interface makes it clear the value has no interesting behavior beyond what is described in the interface.
It also avoids the need to repeat the documentation on every instance of a common method. The constructor should return an interface value
rather than the implementing type. e.g, both `crc32.NewIEEE` and `adler32.New` return the interface type `hash.Hash32`

### Embedding Structs

Use *anonymous* struct embedding to compose structures:

```go
 type Circle struct {
      Point
      Radius int 
  }
  type Wheel struct {
      Circle
      Spokes int 
  }
```

Outer type can then use methods from inner type.

## Maps

Key can be of any type for which the equality operator is defined, such as integers, floating point and complex numbers, strings, pointers, interfaces (as long as the dynamic type supports equality), structs and arrays
Need to use sync.Map for synchronised thread-safe access

- `len(map)` is 0 if map is `nil` or empty

## Constants

Enumerated constants are created using the `iota` enumerator

## init()

Each source file can define its own niladic init function to set up whatever state is required
init is called after all the variable declarations in the package have evaluated their initializers, and those are evaluated only after all the imported packages have been initialized.

## Pointer vs. Value methods

- Value methods can be invoked on pointers and values, but *pointer methods* can *only* be invoked on *pointers*.
- Convention is that if one method is pointer receiver, all should be pointer receivers.
- Use values if:
  - Receiver is basic type (`int`, `string`)
  - Receiver is small array or value type with non mutable fields (e.g. `time.Time`)
  - We don't want to mutate calling variable in method or a value type wui

### Copying Instances of type T

- It is safe to copy instances of type `T` if all methods are on `T`
  - Calling methods makes a copy of the `T` instance
  - If `T` has any `*T` pointer receivers them you should not copy instances of `T`
    - Pointer methods will update original and copy instance with unpredictable results
    - e.g. `bytes.Buffer`

## Interfaces

- A type can implement multiple interfaces
- Interfaces are satisified *implicitly*
- Decoupling: rely on abstraction instead of a concrete implementation - so implementation itself can be replaced with another - Liskov Substitution Principle, or the L in Robert C. Martin’s SOLID design principles
- Hiding: "interface wraps and conceals the concrete type `T` and value that it holds. Only the methods revealed by the interface type may be called, even if the concrete type has others"
- Empty Interface type `interface{}`, can have any value assigned to it as
no contracts specified! e.g `fmt.Println()`
  - In Go 1.18 use `any` instead of `interface{}`
  - Avoid using empty interfaces unless there is a good case, e.g. marshaling or formatting
- Variables are transparently converted to pointers if a pointer method is
invoked on a non-pointer.  However, if a value of type `T` does not possess all the methods that a `*T` pointer does, then as a result it might satisfy fewer interfaces.

- Interfaces have a *dynamic type* and a *dynamic value* both of which can change, e.g:

```go
    var w io.Writer             // type=nil, value=nil
    w = os.Stdout               // type=*os.File,value=os.Stdout
    w = new(bytes.Buffer)       // type=*bytes.Buffer,value=bytes.Buffer{}
    w = nil                     // type=nil, value=nil 
```

Two interface values are equal if:

- Both are nil, or
- Dynamic types are identical and their dynamic values are equal
- There is a small performance overhead when calling a method through an interface:
  - Requires a lookup to find the dynamic type an interface points to

Hints:

- You can test on interface type for `any`:

```go
v := any.(type) {
     case int:
        return strconv.Itoa(v)
    case float:
        return strconv.Ftoa(v, 'g', -1)
    }
```

- Avoid polluting code with too many abstractions (like Java)
- "The bigger the interface, the weaker the abstraction" Rob Pike
  - Fine-grained abstractions like `io.Reader` and `io.Writer` are examples:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

- Abstractions should be discovered, not created - only create when you need them
- Expose `struct`/concrete implementations on *producer* side, and let *client* decide whether `interface` is necessary
- "Be conservative in what you do, be liberal in what you accept from others" - Postel's law
  - Accept `interfaces` not `structs`
  - Return `structs`, not `interfaces`

- Use `%T` to print out the dynamic type.
- A nil interface value, is not the same as an interface value containing a pointer that happens to be nil.
  This means `if some-interface-value != nil` will be true, but the value can be nil!
  The solution is not to use interface values with comparisons!

### Embedding Interfaces

```go
// ReadWriter is the interface that combines the Reader and Writer interfaces (see above)
// so no method forwarding is necessary:
type ReadWriter interface {
    Reader
    Writer
}

// Similar to this, but subclassing means we need to forward methods:
type ReadWriter struct {
    reader *Reader
    writer *Writer
}

func (rw *ReadWriter) Read(p []byte) (n int, err error) {
    return rw.reader.Read(p)
}
```

Embedding vs. Subclassing behave differently:

- *embedding*, the receiver of `Foo` remains `X`

```go
type X struct {}
func (x X) Foo() {}

type Y interface { X }
```

- *subclassing*, the receiver of `Foo` becomes the subclass, `Y`

```go
type X struct {}
func (x X) Foo() {}

type Y struct { x X }
```

An expression may be assigned to an interface only if its type satisfies the interface.

### Type Aliases (extending structs from another package)

You can't define new methods on a non-local type (see [https://pkg.go.dev/golang.org/x/tools/internal/typesinternal#InvalidRecv]) so instead you `alias` the type and define new methods on that.

e.g.:

```go

type MyHttpRequest http.Request

func (r *MyHttpRequest) SignRequest(secret string, timestamp string) error {
    ...
}

myHttp := MyHttpRequest(*req)
```




### Mocking Interfaces For Unit Tests

The `New` method is the magic that converts A -> B:

```go
type A struct {}
type B interface {
    someMethod()
}

func NewA() B {
    return &A{}
}

func (a *A) someMethod() {}
```

You can then mock in tests with stretchr/testify (or similar):

```go
type MockA stuct {
    mock.Mock
}

func (c *MockA) someMethod() {
    c.Called()
}

func mytests(t * testing.T) {
    testObj := new(MockA)
    testObj.On("someMethod").Return()
    // Do test
    testObj.AssertExpectations(t)
}
```

## Sorting

There is built-in sort for numbers, strings:

```go
sort.Int(array)
sort.Strings(array)
```

You need to implement interface for custom types:

```go
type sort.Interface interface {
      Len() int
      Less(i, j int) bool // i, j are indices of sequence elements
      Swap(i, j int)
  }
```

eg.:

```go
type byLength []string

func (s byLength) Len() int {
    return len(s)
}
func (s byLength) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}
func (s byLength) Less(i, j int) bool {
    return len(s[i]) < len(s[j])
}
```

A good use-case for [##Generics]

## Generics

Main uses:

- Data structures: factor out the element type
- Functions working with `slices`, `maps`, and `channels` of any type

## Goroutines

Prefix a function or method call with the go keyword to run the call in a new goroutine. When the call completes, the goroutine exits, silently.

```go
    go func() {
        // do stuff in new goroutine
    }(<optional args>)
```  

### Goroutines arguments

Note that the example below prints out `ccc` because the same instance of `v` is used:

```go
    values := []string{"a", "b", "c"} 
    for _, v := range values {
        go func() {
            fmt.Println(v)
            done <- true
        }()
    }
```

Two solutions - create a new instance of `v` on each iteration or pass `v` as argument to closure (much better IMHO)

```go
    for _, v := range values {
        v := v // create a new 'v'.
        go func() {
            fmt.Println(v)
            done <- true
        }()
    }

    for _, v := range values {
        go func(u string) {
            fmt.Println(u)
            done <- true
        }(v)
    }
```

## Channels

Channels are allocated with make, and the resulting value acts as a reference to an underlying data structure. If an optional integer parameter is provided, it sets the buffer size for the channel.

```go
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files
```

Example using channel `c`:

```go
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
<-c   // Wait for sort to finish; discard sent value.
```

Send something *to* messages: `messages <- "buffered"`
Recieve something *from* `messages`: `msg := <-messages`

Accepts one channel for receives (pings) and a second for sends (pongs):

```go
func pong(pings <-chan string, pongs chan<- string) {
    msg := <-pings
    pongs <- msg
}
```

Close a channel `close(messages)` - `more == false`:

```go
for {
    j, more := <-messages
    if more {
        fmt.Println("received message", j)
    } else {
        fmt.Println("received all messages")
        done <- true
        return
    }
}
```

or loop:

```go
    for elem := range message {
        fmt.Println(elem)
    }
```

Non-blocking wait:

```go
    select {
    case msg := <-messages:
        fmt.Println("received message", msg)
    default:
        fmt.Println("no message received")
    }
```  

## WaitGroups

Allows you to wait for multiple goroutines to finish.

```go
var wg sync.WaitGroup
for i := 1; i <= 5; i++ {
    wg.Add(1)

    go func() {
        // tell wait group we are finished
        defer wg.Done() 
        worker(i)
    }()
}
wg.Wait()
```


## JSON

`json.Marshal(movies)` or `json.MarshalIndent` (humans)

## Methods

- A method is just a function with a receiver argument.
- Methods can be defined for any named type (except a pointer or an interface); the receiver does not have to be a struct.
- `Nil` is a valid receiver value!

### Embedding Methods

We use composition (has-a), not inheritance (is-a)!

e.g.:

```go
type A struct {}
type B struct {
    A
    another string
}
```

the compiler will automatically generate a wrapper method:

```go
func (a B) some-method-of-A() {}
```

...so we can do B.some-method-of-A() implicitly.

Also works for unmamed structs, e.g:

```go
var cache = struct {
    sync.Mutex
    mapping map[string]string 
}{
    mapping: make(map[string]string),
}
```

here `cache` has all of the methods from `sync.Mutex`.

### Method Values

If we have `(a *A) some-method-of-A(x)` then `a.some-method-of-A` is a *method value* and
we can write:

```go

blah := a.some-method-of-A
blah(x)
```

as shorthand.

A method expression is a function that can be called like a regular function, except that you also pass the object to act on as the first argument.
This is because it needs to know which object to use.

e.g. we can do:

```go
var op func(a, b) A
if add {
    op = A.Add
} else {
    op = A.Sub
}
x = op(something, something)
```

### Varadic methods

```go
func newCitizen(param ...string) citizen {
    switch len(param) {
        case 0:
            return citizen{"unknown", "unknown"}
        case 1:
            return citizen{param[0], "unknown"}
        default:
            return citizen{param[0], param[1]}
    }
}
```

## String Formatting

```text
- %d decimal integer
- %x, %o, %b integer in hexadecimal, octal, binary
- %f, %g, %e floating-point number: 3.141593 3.141592653589793 3.141593e+00 
- %t boolean: true or false
- %c rune (Unicode code point)
- %s string
- %q quoted string "abc" or rune 'c'
- %v any value in a natural format
- %T type of any value
- %% literal percent sign (no operand)
```

## Errors

Basic invokation `errors.New(text string)` or `fmt.Errorf(format string, a ...interface{}) error`, or can create richer error types:

```go
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e PathError) Error() string {
    return e.Path
}
```

- Error messages are frequently chained together, so message strings should not be capitalized and newlines should be avoided
- Expected errors should be designed as error values (sentinel errors): `var ErrFoo = errors.New("foo")`
  so you can check against them
- Unexpected errors should be designed as error types: `type BarError struct { ... }`, with `BarError` implementing the error interface
- Wrap errors with `fmt.Errorf("some error %w", err)`
  - Adds additional context to an error
  - Marking an error as a specific error
  - Use `errors.Is` instead of `==` to check wrapped errors, it can look at nested errors

## Functions

Functions are first-class values in Go: like other values, function values have types, and they may be assigned to variables or passed to or returned from functions.

Variadic funcions use `...`, eg. `func sum(vals ...int) int {`

## Panic

Use for unexpected errors.
Runtime error to abort program.
Handle with `defer` - all deferred functions called in reverse order

## Performance

- Java has 1:1 mapping threads to OS threads
- Goroutines use GOMAXPROCS `green` threads = virtual threads (i.e. Go runtime does sheduling),
  so you can run more goroutines on a typical system than you can threads
- Goroutines have growable segmented stacks
- Goroutines have a faster startup time than threads

## IO

### Read file

```go
dat, err := os.ReadFile("/tmp/dat")
os.ReadFile("/tmp/dat")
```

```go
f, err := os.Open("/tmp/dat")
b1 := make([]byte, 5)
n1, err := f.Read(b1)
```

```go
r4 := bufio.NewReader(f)
b4, err := r4.Peek(5)
```

### Read and parse input

```go
    reader := bufio.NewReaderSize(os.Stdin, 16 * 1024 * 1024)

    // Read Int
    tTemp, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
    checkError(err)
    t := int32(tTemp)

    // Read Line
    for tItr := 0; tItr < int(t); tItr++ {
        line := readLine(reader)
        input := strings.Split(strings.TrimSpace(line)), " ")
```

### Read HTTP (limited) response

```go
    httpResponse, err := w.client.Do(req)
    if err != nil {
        return err
    }
    defer httpResponse.Body.Close()
    data, err := io.ReadAll(io.LimitReader(httpResponse.Body, 1*1024*1024))
```

### Write file

Always close after creating:

```go
f, err := os.Create("/tmp/dat2")
check(err)
defer f.Close()
```

```go
d1 := []byte("hello\ngo\n")
err := os.WriteFile("/tmp/dat1", d1, 0644)
check(err)

w := bufio.NewWriter(f)
n4, err := w.WriteString("buffered\n")
```

## Sprintf

```
  %d
  %x, %o, %b
  %f, %g, %e
  %t
  %c
  %s
  %q
  %v
  %T
  %%
decimal integer
integer in hexadecimal, octal, binary
floating-point number: 3.141593 3.141592653589793 3.141593e+00 boolean: true or false
rune (Unicode code point)
string
quoted string "abc" or rune 'c'
any value in a natural format
type of any value
literal percent sign (no operand)
```

```
%9f    width 9, default precision
%.2f   default width, precision 2
%g     maximum number of significant digits
```

## Time

- The Time returned by `time.Now` contains both a wall clock reading and a monotonic clock reading
- Later time-telling operations use the wall clock reading
- Later time-measuring operations, specifically comparisons and subtractions, use the monotonic clock reading

### Formatting/Parsing

- Parsing based on example `Mon Jan 2 15:04:05 MST 2006`
- e.g. `t = time.Parse("3:04:05PM", s)`
- Format: `t.Format("15:04:05")`

## Godoc

- Every exported element must be documented (structure, an interface, a function, etc)
- The convention is to add a single-sentence comment ending with '.', starting with the name of the exported element
- Packages should start the comment with a `// Package <package-name>` comment

### Build Constraints

A build constraint, also known as a build tag, is a condition under which a file should be included in the package. Build constraints are given by a line comment that begins `//go:build`, e.g. for `mage` files:

```golang
//go:build mage

package main
```


## Static Linking

Go wants to statically link when it can.
Use `CGO_ENABLED=0` to disable C-library versions of `net` and `os/user`. https://pkg.go.dev/cmd/cgo. This means all of the packages are included in the binary, fewer hassles with cross-compiling and linking to dynamic libraries for different platforms.

```
CGO_ENABLED=0 go build main.go
```
## Tips

Read lines from stdin:
```
   reader := bufio.NewReaderSize(os.Stdin, 16 * 1024 * 1024)
```

Parse int:
```
   nTemp, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
   checkError(err)
   n := int32(nTemp)
```

Split line:
```
    arrTemp := strings.Split(strings.TrimSpace(readLine(reader)), " ")
```

Use Mutex:

```go
    var mu sync.Mutex

    func needsLocking() {
        mu.Lock()
        defer mu.Unlock()
```

Use RWMutex:

```go
    var mu sync.RWMutex

    func needsLocking() {
        // Allow multiple readers to share lock
        for {
            mux.RLock()
            for k, v := range m {
                fmt.Println(k, "-", v)
            }
            mux.RUnlock()
        }
```

Regexp Match:

```go

match, _ := regexp.MatchString("p([a-z]+)ch", "peach")
    fmt.Println(match)
```

