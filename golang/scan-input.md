# Scan Input from STDIN

* Taking in input from command line

```go
var name string
fmt.Scanf("%s", &name)
```

**Note**: Above method is super simple, we cannot scan lines using above.

* Scan lines

```go
scanner := bufio.NewScanner(os.Stdin)
scanner.Scan()
line := scanner.Text()
fmt.Println(line)
```
