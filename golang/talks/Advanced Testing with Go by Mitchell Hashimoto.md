# Advanced Testing with Go by Mitchell Hashimoto


### Table Driven Tests

- Write test cases in an array of struct.
- And a for loop iterates over the test cases
- Low overhead to add new test cases
- Makes testing exhaustive scenarios simple
- Makes reproducing reported issues simple
- Do this pattern a *lot*
- Follow pattern even for a single cases, if its possible to grow

```go
func TestAdd(t *testing.T) {
   cases := []struct{ A, B, Expected int }{
        { 1, 1, 2 },
        { 1, -1, 0 },
        { 1, 0, 1 },
        { 0, 0, 0 },
   }
   for _, tc := range cases {
       actual := tc.A + tc.B
       if actual != expected {
           t.Errorf(
           "%d + %d = %d, expected %d",
           tc.A, tc.B, actual, tc.Expected)
       }
   }
}
```

- Consider naming the cases
- So when test fails it gives you index of the failed test, having names can help you understand what test actually failed.

```go
func TestAdd(t *testing.T) {
    cases := map[string]struct{ A, B, Expected int }{
        "foo": { 1, 1, 2 },
        "bar": { 1, -1, 0 },
    }
    for k, tc := range cases {
        actual := tc.A + tc.B
        if actual != expected {
            t.Errorf(
                "%s: %d + %d = %d, expected %d",
                k, tc.A, tc.B, actual, tc.Expected)
        }
    }
}
```

### Test Fixtures

- Data required for running tests
- Data like fake configs, etc. can be put in the fixtures directory.
- "go test" sets `pwd` as package directory
- Use relative path `test-fixtures` directory as a place to store test data.
- Very useful for loading config, model data, binary data, etc.

```go
func TestAdd(t *testing.T) {
    data := filepath.Join("test-fixtures", "add_data.json")
    // ... Do something with data
}
```

### Golden Files (also Test Flags)

```go
var update = flag.Bool("update", false, "update golden files")

func TestAdd(t *testing.T) {
    // ... table (probably!)
    for _, tc := range cases {
        actual := doSomething(tc)
        golden := filepath.Join("test-fixtures", tc.Name+".golden")
        if *update {
            ioutil.WriteFile(golden, actual, 0644)
        }
        expected, _ := ioutil.ReadFile(golden)
        if !bytes.Equal(actual, expected) {
            // FAIL!
        }
    }
}
```
- Test complex output without manually hardcoding it
- Human eyeball the generated golden data. If it is correct, commit it.
- Very scalable way to test complex structures (write a String() method)

### Global State

- Avoid it as much as possible.
- Instead of global state, try to make whatever is global a configuration option using global state as the *default*, allowing tests to modify it.
- If necessary, make global state a var so it can be modified.

```go
// Not good on its own
const port = 1000

// Better
var port = 1000

// Best
const defaultPort = 1000
type ServerOpts {
   Port int // default it to defaultPort somewhere
}
```

### Test Helpers

- Creating a temporary file write a function so that test becomes readable than having all the boiler plate.
```go
func testTempFile(t *testing.T) string {
    tf, err := ioutil.TempFile("", "test")
    if err != nil {
        t.Fatalf("err: %s", err)
    }   
    tf.Close()
    return tf.Name()
}
```
- The above boiler plate should not be in a test functions
- Never return errors. Pass in `*tesSng.T` and fail.
- By not returning errors, usage is much prettier since error checking is gone.
- Used to make tests clear on what they’re testing vs what is boilerplate.

```go
func testTempFile(t *testing.T) (string, func()) {
    tf, err := ioutil.TempFile("", "test")
    if err != nil {
        t.Fatalf("err: %s", err)
    }
    tf.Close()
    return tf.Name(), func() { os.Remove(tf.Name()) }
}
func TestThing(t *testing.T) {
    tf, tfclose := testTempFile(t)
    defer tfclose()
}
```
And an actual example

```go
func testChdir(t *testing.T, dir string) func() {
    old, err := os.Getwd()
    if err != nil {
        t.Fatalf("err: %s", err)
    }
    if err := os.Chdir(dir); err != nil {
        t.Fatalf("err: %s", err)
    }
    return func() { os.Chdir(old) }
}
func TestThing(t *testing.T) {
    defer testChdir(t, "/other")()
    // ...
}
```
- Returning a `func()` for cleanup is an elegant way to hide that
- The `func()` is a closure that can have access to `*testing.T` to also fail
- Example: `testChdir` proper setup/cleanup would be at least 10 lines
without the helper.

### Package/Functions

- Break down functionality into packages/functions judiciously
- NOTE: Don’t overdo it. Do it where it makes sense.
- Doing this correctly will aid testing while also improving organization. Over-doing it will complicate testing and readability.
- Qualitative, but practice will make perfect.
- Unless the function is extremely complex, we try to test only the exported functions, the exported API.
- Treat unexported functions/structs as implementation details: they are a means to an end. As long as we test the end and it behaves within spec, the means don’t matter.
- Some people take this too far and choose to only integration/
acceptance test, the ultimate "test the end, ignore the means."


### Networking

- Testing networking? Make a real network connection.
- Don’t mock `net.Conn`. 

```go
// Error checking omitted for brevity
func TestConn(t *testing.T) (client, server net.Conn) {
    ln, err := net.Listen("tcp", "127.0.0.1:0")

    var server net.Conn
    go func() {
        defer ln.Close()
        server, err = ln.Accept()
    }()
    client, err := net.Dial("tcp", ln.Addr().String())
    return client, server
}
```
- That was a one-connection example. Easy to make an N-connection.
- Easy to test any protocol.
- Easy to return the listener as well.
- Easy to test IPv6 if needed.
- Why ever mock `net.Conn`? (Rhetorical, for readers)


### Configurability

- Unconfigurable behavior is often a point of difficulty for tests.
      - Example: ports, timeouts, paths
- Over-parameterize structs to allow tests to fine-tune their behavior
- It is okay to make these configurations unexported so only tests can
set them.

```go
// Do this, even if cache path and port are always the same
// in practice. For testing, it lets us be more careful.
type ServerOpts struct {
    CachePath string
    Port int
}
```

### Subprocessing

- Subprocessing is typical a point of difficult-to-test behavior.
- Two options:
 	1. Actually do the subprocess
 	2. Mock the output or behavior

##### Subprocessing: Real

- Actually execuSng the subprocess is nice
- Guard the test for the existence of the binary
- Make sure side effects don’t affect any other test

```go
var testHasGit bool
func init() {
    if _, err := exec.LookPath("git"); err == nil {
        testHasGit = true
    }
}
func TestGitGetter(t *testing.T) {
    if !testHasGit {
        t.Log("git not found, skipping")
        t.Skip()
    }
    // ...
}
```

##### Subprocessing: Mock

- You still actually execute, but you’re executing a mock!
- Make the `*exec.Cmd` configurable, pass in a custom one
- Found this in the `stdlib`, it is how they test `os/exec`!

```go
func helperProcess(s ...string) *exec.Cmd {
    cs := []string{"-test.run=TestHelperProcess", "--"}
    cs = append(cs, s...)
    env := []string{
        "GO_WANT_HELPER_PROCESS=1",
    }
    cmd := exec.Command(os.Args[0], cs...)
    cmd.Env = append(env, os.Environ()...)
    return cmd
}
```
What it executes
```go
func TestHelperProcess(*testing.T) {
    if os.Getenv("GO_WANT_HELPER_PROCESS") != "1" {
        return
    }
    defer os.Exit(0)
    args := os.Args
    for len(args) > 0 {
        if args[0] == "--" {
            args = args[1:]
            break
        }
        args = args[1:]
    }

    // ...
    cmd, args := args[0], args[1:]
    switch cmd {
        case “foo”:
         // ...
```

### Interfaces

- Interfaces are mocking points.
- Behavior can be defined regardless of implementation and exposed via custom framework or testing.go (covered elsewhere)
- Similar to package/functions: do this judiciously, but overdoing it will complicate readability. 

### Timing-dependent Tests

```go
func TestThing(t *testing.T) {
    // ... 
    select {
    case <-thingHappened:
    case <-time.After(timeout):
        t.Fatal("timeout")
    }
}
```
- No "fake time"
- It's just a multiplier available that can be set to increase timeouts
- Not perfect, but not as intrusive as fake time. Still, fake time could be better.

```go
func TestThing(t *testing.T) {
    // ... 
    timeout := 3 * time.Minute * timeMultiplier
    select {
    case <-thingHappened:
    case <-time.After(timeout):
        t.Fatal("timeout")
    }
}
```

### Parallelization

```go
func TestThing(t *testing.T) {
    t.Parallel()
}
```
- Don’t do it. Run multiple processes.
- Makes test failures uncertain: is it due to pure logic but, or race?
- *OR*: Run tests both with `-parallel=1` and `-parallel=N`
- Prefer to just not use parallelization. Use multiple
processes and unit tests specifically written to test for races.


### Links:

- Slides: https://speakerdeck.com/mitchellh/advanced-testing-with-go
- Video: https://www.youtube.com/watch?v=yszygk1cpEc
- Event: Recorded in Berlin on March 15, 2016.