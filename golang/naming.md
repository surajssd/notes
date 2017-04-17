# Naming

### Good Names

- consistent
- short
- accurate

### The rule of thumb

- The greater the distance between a name's declaration and its uses, the longer the name should be.
- Longer here means descriptive


### Name style

- Use mixed-cases not names with underscores
- Acronyms should be capitalized


### Local variables

- Keep them short, e.g. `i` for `index`, `r` for `reader`.

### Parameters

- Function parameters are like local variables.
- Keep them short when the **type** of the variable is *descriptive enough*.
- But if the type of the function parameter is a built in type then make the **names** *descriptive enough* because they don't signify anything in the documentation.

### Return values

- Return values should only be used for the documentation purposes.
- Try to avoid the naked returns.
- Even if you have return types named don't use naked returns.

### Receivers

- Receivers again are similar to the local variables so should be named smaller, mainly single letter names.

### Exported package level names

- Anything that is exported is always gonna be used with the package name.
- So don't add package name to the exported values for e.g. don't use `bytes.ByteBuffer` instead use `bytes.Buffer`.

### Errors

- Error **types** should be of the form `FooError`
- Error **values** should be of the form `ErrFoo`

### The standard library

- It's a great place to find good go code.
- Look to it for inspiration.

### References:

- What's in a name? [video](https://youtu.be/sFUSP8Au_PE).
- [Variables by ArdanLabs](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/variables/README.md#links)

