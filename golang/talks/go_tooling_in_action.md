# Go tooling in Action by Francesc Campoy


### `go list`

docs:
```bash
$ go list -f '{{ .Name }}: {{ .Doc }}'
imagebuilder: Package builder uses code from github.com/docker/docker/builder/* to implement a Docker builder that does not create individual layers, but instead creates a single layer.
```

```bash
$ go list -f '{{ .Doc }}' fmt
Package fmt implements formatted I/O with functions analogous to C's printf and scanf.
```


see all imports:
```bash
$ go list -f '{{ .Imports }}'
[bytes fmt github.com/openshift/imagebuilder/vendor/github.com/docker/docker/builder/command github.com/openshift/imagebuilder/vendor/github.com/docker/docker/builder/parser github.com/openshift/imagebuilder/vendor/github.com/fsouza/go-dockerclient github.com/openshift/imagebuilder/signal github.com/openshift/imagebuilder/strslice io/ioutil log os path/filepath regexp runtime strings text/scanner unicode]
```

see all imports of imports:
```bash
$ go list -f '{{ join .Imports "\n" }}' fmt
errors
io
math
os
reflect
strconv
sync
unicode/utf8
```

### `go doc`

easy ways to see docs than using `go list`
```bash
$ go doc
package imagebuilder // import "github.com/openshift/imagebuilder"

Package builder uses code from github.com/docker/docker/builder/* to
implement a Docker builder that does not create individual layers, but
instead creates a single layer.

TODO: full windows support

const NoBaseImageSpecifier = "scratch" ...
var LogExecutor = logExecutor{} ...
[SNIP]
```

but you can get the documentation of a specific package and function in that package as well
```bash
$ go doc fmt
package fmt // import "fmt"

Package fmt implements formatted I/O with functions analogous to C's printf
and scanf. The format 'verbs' are derived from C's but are simpler.


Printing

The verbs:

General:

    %v  the value in a default format
```

for getting info of a specific function in package
```bash
$ go doc fmt Printf
func Printf(format string, a ...interface{}) (n int, err error)
    Printf formats according to a format specifier and writes to standard
    output. It returns the number of bytes written and any write error
    encountered.
```

you can also see docs in browser as well, not only standard library docs but also docs in all the packages on your machine.
```bash
$ godoc -http :19009
```
now visit `http://localhost:19009`

### `errcheck`

checking all the missing error handlers, that could cause silent errors

```bash
$ errcheck
/home/hummer/go/src/github.com/openshift/imagebuilder/signal/signal.go:14:37: undeclared name: syscall
/home/hummer/go/src/github.com/openshift/imagebuilder/signal/signal.go:30:33: undeclared name: syscall
/home/hummer/go/src/github.com/openshift/imagebuilder/signal/signal_linux.go:13:28: undeclared name: syscall
/home/hummer/go/src/github.com/openshift/imagebuilder/strslice/strslice.go:20:12: undeclared name: json
/home/hummer/go/src/github.com/openshift/imagebuilder/strslice/strslice.go:22:13: undeclared name: json
/home/hummer/go/src/github.com/openshift/imagebuilder/signal/signal_linux.go:14:14: undeclared name: syscall
[SNIP]
```

### `go vet`

```bash
$ go vet github.com/skippbox/kompose/pkg/loader/compose
pkg/loader/compose/compose.go:123: no formatting directive in Fatalf call
exit status 1
```

What is `go vet`?
```bash
$ go doc cmd/vet
Vet examines Go source code and reports suspicious constructs, such as
Printf calls whose arguments do not align with the format string. Vet uses
heuristics that do not guarantee all reports are genuine problems, but it
can find errors not caught by the compilers.
[SNIP]
```

### Testing

- Files that end with `_test.go` are ignored by compiler.
- Will be taken into account when we run tests.

### Links

- Video: https://www.youtube.com/watch?v=uBjoTxosSys
- Video Author: https://twitter.com/francesc
