# Dont Just Check Errors Handle Them Gracefully - Dave Cheney - Gophercon 2016

### Errors are just values

- Any value that implements the error interface is itself an error.
- Programming with errors.
- Error handling categories:

### Sentinel errors

```go
if err == ErrSomething { ... }
```
e.g.

- `io.EOF`
- `syscall.ENOENT`
- `go/build.NoGoError`
- `path/filepath.SkipDir`

- Least flexible error handling, comparing with the already defined value and causes problem when giving more context, because if you return a different error it would break that check.

```go
buf := make([]byte, 100)
n, err := r.Read(buf)
buf = buf[:n]
if err == io.EOF {
    log.Fatal("read failed:", err)
}
```

- Never inspect the output of `error.Error()`
- Sentinel errors create a dependency between two packages.
- Avoid using sentinel error values.

### Error Types

```go
if err, ok := err.(SomeType); ok { ... }
```

- Error Type is a type you create which implements the error interface.
- Callers can use type assertion, to extract extra context.
- But error types need to be made public so that people can use type assetion.
- Better than error values but also avoid using error types.

### Opaque errors

- Most flexible, Least coupling between your code and caller.
- You don't know what happened inside, all you know if it worked or it didn't.

    ```go
    import "github.com/quxx/bar"

    func fn() error {
        x, err := bar.Foo()
        if err != nil {
            return err
        }
        // use x
    }
    ```

- Assert errors for behaviour, not type.
- **Don't just check errors, handle them gracefully.**
- TGPL book recommends annotating errors but its again you are converting error to string adding few things to it and converting back to new error, breaks the quality.
- How to add context to errors: github.com/pkg/errors

    -
    ```go
    err := errors.New("dhadam")
    fmt.Printf("%v\n", err)
    ```
    output: `dhadam`

    ```go
    fmt.Printf("%+v\n", err)
    ```
    output: `dhadam` and whole stack trace at that point.

    -
    ```go
    err := errors.Wrap(
            syscall.EBADF, "couldn't write to stream")
    fmt.Printf("%v\n", err)
    ```
    output: `couldn't write to stream: bad file descriptor`

- Only do one thing while handling an error, don't print it and also return to caller. Do either of it.

     ```go
    return errors.Wrap(err, "write failed")
     ```


### Conclusion

- Errors are part of your package's public API.
- Treat errors as opaque; assert for behaviour, not type.
- Minimise the use of sentinel error values in your program.
- Convert errors to opaque errors with errors. Wrap them.
- Use `errors.Cause` to recover the underlying error.


### Links:

- Video: https://www.youtube.com/watch?v=lsBF58Q-DnY
- Slides: http://dave.cheney.net/paste/gocon-spring-2016.pdf
- Author: https://twitter.com/davecheney
- Related links: http://dave.cheney.net/tag/error-handling
