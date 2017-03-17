# Testing in golang

## Table driven testing

### Sample example

Save below file with `something_test.go`.
```go
package testingo

import (
    "strings"
    "testing"
)

// TestIndex of the strings
func TestIndex(t *testing.T) {
    var tests = []struct {
        s   string
        sep string
        out int
    }{
        {"", "", 0},
        {"", "a", -1},
        {"fo", "foo", -1},
        {"oofoofoo", "f", 2},
        {"oofoofoo", "x", -1},
    }

    for _, test := range tests {
        actual := strings.Index(test.s, test.sep)
        if actual != test.out {
            t.Errorf("Index(%q, %q) = %v; want %v", test.s, test.sep, actual, test.out)
        }
    }
}
```

and test using

```bash
go test
```

You can also enable parallel testing by adding this to your test file:
```go
t.Parallel()
```

Skip test using `t.Skip("some message as to why skip this test")`.
Error reporting using, `t.Errorf`, `t.Fatalf`, `t.Logf`.


## Quality tests in go

### Shared setup and teardown:

Return a teardown func so setup and teardown code is kept together

```go
func setup(t *testing.T) (*db, func()) {
    db, err := dial(testDatabase)
    if err != nil {
        t.Errorf("dial: %s", err)
        return nil, func(){}
    }
    return db, func() {
        if err := db.Close(); err != nil {
            t.Errorf("db close: %s", err)
        }
    }
}
```

### Using different package name for the test code


- This gives you a external perspective of the package
- You don't fiddle with the internal functions
- *"If you don't trust yourself to not use private methods in unit tests, then I cannot help you" -- Dave Cheney*

## Test coverage

*"Shoot for 90% code coverage, 100% of the happy path" --William Kennedy*

repost test coverage stats

```bash
$ go test -cover
```

the `go` tool can generate coverage profiles that may be interpreted by the cover tool.

```bash
$ go test -coverprofile=cover.out
$ go tool cover -func=cover.out
```

To see how much of your code was touched when you ran your test

```bash
$ go test -covermode=count -coverprofile=count.out
$ go tool cover -html=count.out
```

Coverage visualization

```bash
$ go tool cover -html=cover.out
```

## Detect race conditions in software

```bash
$ go test -race
```



Ref:

- Testing Techniques https://www.youtube.com/watch?v=ndmB0bj7eyw
