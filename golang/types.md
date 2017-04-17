
## Types

- Types may be *named* or *unnamed*. Named types are specified by *type name*; unnamed types are
specified using a *type literal*, which composes a new type from existing types.
- Basic: numbers, boolean, strings
- Aggregate: arrays, structs
- Reference: pointers, slices, maps, functions and channels
- Interfaces

- Rune is a single character in single quotes, similar to char in C
- We can use `%c` to print char, or use %q with the quotes.

## Arrays

- The type `[n]T` is an array of `n` values of type `T`.

    ```go
    var a [10]int
    
    OR
    
    primes := [6]int{2, 3, 5, 7, 11, 13}
    ```

- Array Literal

    ```go
    [3]bool{true, true, false}
    ```

- If array literal is defined as with ellipsis instead of size, array length is determined by number of elements

    ```go
    [...]int{1, 2, 3, 4, 5, 6}
    ```

- Normally array takes value of "zero" form of that type, but we can customize values like following:

    ```go
    a := [...]int{3: -1, -2}  // Array of size five with last two elements as -1 and -2
    fmt.Printf("%#v\n", a)    // [5]int{0, 0, 0, -1, -2}
    ```

- The size of array is part of the type. i.e. array declared as `[2]int` it is of type `[2]int`. Following is totally fine.
    ```go
    a := [3]int{1, 2, 3}
    a = [3]int{4, 5, 6}
    ```
    
- This is not correct.

    ```go
    b := [2]int{4, 5}
    b = [3]int{4, 7, 8}   // type mismatch
    ```

- We can define array similar to Maps, but the array indexes have to be int and constants.

    ```go
    const (
        BLR = 2
        PNQ = 4
        AWB = 6
    )

    airports := [...]string{PNQ: "Pune", BLR: "Bangalore", AWB: "Aurangabad"}
    fmt.Println(airports[AWB])      // Aurangabad
    fmt.Printf("%#v\n", airports)   // [7]string{"", "", "Bangalore", "", "Pune", "", "Aurangabad"}

    ```

- Array Comparison, arrays of same size can be compared to check if each and every element matches.

    ```go
    a := [3]int{1, 2, 3}
    b := [...]int{1, 2, 3}
    c := [3]int{1, 2}
    d := [2]int{1, 2}
    fmt.Println(a == b) // true
    fmt.Println(b == c) // false
    fmt.Println(c == d) // invalid operation: c == d (mismatched types [3]int and [2]int)
    ```

- Arrays by default are passed as a copy not reference.

## Slices

- Arrays - fixed size.
- Slices - dynamically sized.
- The type `[]T` is a slice with elements of type `T`.

    ```go
    var s []int = primes[1:4]
    ```

- Creating slice of specific size at start.

    ```go
    subjects := make([]string, 0, len(hours))
    ```

- Slice are of the form half-open intervals, that include the first element and exclude the last one.
- Slices are like references to arrays
- A slice does not store any data, it just describes a section of an underlying array. Changing the elements of a slice modifies the corresponding elements of its underlying array. Other slices that share the same underlying array will see those changes.

- Slice Literal, A slice literal is like an array literal without the length.

    ```go
    r := []bool{true, true, false}
    
    s := []struct {
        i int
        b bool
    }{
        {2, true},
        {23, false},
        {21, true}
    }
    ```

- Slice Literal looks like array literal but it but size not given, this implictly creates an array but yeilds a slice that points to it.

- Slice has three components viz. Pointer, Length, Capacity.
- Slice length and capacity

    ```go
    r := []int{1, 3, 4, 5, 6, 7, 8, 9, 10}
    fmt.Println(len(r[2:4]), cap(r[2:4]))
    ```
    output
    ```bash
    2 7
    ```
    
- The length of a slice is the number of elements it contains, it cannot exceed capacity.
- The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

- Slicing beyond capacity causes panic and slicing beyond length will extend the slice.
- Slice is a pointer to an element in underlying array, so passing it is by reference by default.
- Slices cannot be compared similar to arrays, except for the slices of bytes can be compared with `bytes.Equal`. Since slice is a reference it can only be used for either *reference identity* or *nil*

- Creating Slice with make
    ```go
    b := make([]int, 0, 5) // len(b)=0, cap(b)=5
    ```

- Slices of Slices

    ```go
    // Create a tic-tac-toe board.
	board := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

    ```

- Appending to slices

    ```go
    var s []int
    s = append(s, 2, 3, 4)
    ```
    output
    ```bash
    2 3 4
    ```

- If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

- Append slice to slice, ellipsis how to 

    ```go
    x = append(x, x...)
    ```

## Maps

- Similar to `dict` in python
- Map maps keys to values

    ```go
    y := make(map[int]string)
    y[1] = "one"
    ```

- A map is a refernce to data structure created by make. When we pass it to other function it receives copy of the reference, so even if we pass it to other functions and if it makes changes, those appear in the data structure.

- Unlike python all the keys of map are of same type and all the values are of same type.
- Key should be some value which is comparable with `==` operator, key can be floating point numbers but its a bad idea to use them.
- Value can be anything.

- Map Literals
    ```go
    x := map[int]string{
		1: "one",
		2: "two",
		3: "three",
	}
    ```

- **Note**: the comma after last field(`3: "three"`) is necessary, otherwise it would give error.

- Making changes to map
    
    Delete an element:
    ```go
    delete(m, key)   // remove element m[key]
    ```
    
    Test that a key is present with a two-value assignment:
    ```go
    elem, ok = m[key]
    ```
    If `key` is in `m`, `ok` is `true`. If not, `ok` is `false`. If `key` is not in the map, then `elem` is the `zero` value for the map's element type.

- Unlike python, all operations(lookup, len, delete, range) on map are safe even if the element is not in the map.
- So lookup on the key that is not present returns zero value of that value type.
- You cannot get address of individual map elements i.e. `ptr := &m[key]` will give compiler error.
- Zero Value of the map type is `nil`
- Maps cannot be compared to each other except for the `nil`.

- To check if the element exists in map we can do a lookup that looks like this:
    ```go
    a[1] = "one"
    num, ok := a[2]     // num="" and ok=false
    ```

- Comparing maps

    ```go
    func compareMaps(a, b map[int]string) bool {
        if len(a) != len(b) {
            return false
        }   
        for key, valA := range a { 
            if valB, ok := b[key]; !ok || valA != valB {
                return false
            }
        }   
        return true
    }
    ```

- For using things that don't support equivalent comparison like slice, to use them in maps, you can use a helper function that does the conversion for you, like slice to string and then use that string as key in map.

- This can be done using some function like

    ```go
    func helper(list []string) { return fmt.Sprintf("%q", list) }
    ```

## Structs

- Structs
    ```go
    type Vertex struct {
        X int
        Y int
    }
    v := Vertex{4, 5}
    fmt.Println(v.X, v.Y)
    ```
    
- Even in struct if the variables are supposed to be accessed via `.` notation they should start their names with capital letter, otherwise it won't be accessible in some other package.

- Struct Literals

    ```go
    v1 := Vertex{X: 1}    // Type 1
    v2 := Vertex{4, 5}    // Type 2
    ```

- The second type should be used if struct in the same package or if the struct represents obvious things, but using it for something complicated, makes it hard to remember, always use 1st type of struct literal.

- Pointer to structure

    ```go
    x := &v
    x.X = 6
    x.Y = 7
    ```
    
    We can use the dot opeartor to access the struct fields.

- A struct is comparable only if the values of that struct are comparable.
- So if a struct is comparable it can be used as a key to a Map.

- Struct fields can also be anonymous, so if struct A has struct X as a field it can be defined without name, so fields in struct X gets populated in struct A.

    ```go
    type X struct {
        p int
        q int
    }
    ```

- So normally someone would define A as
    
    ```go
    type A struct {
        foo X
        yay int
    }
    var a A
    // access it as
    a.foo.p = 10
    a.foo.q = 20
    a.yay = 30
    ```

- With this notation you can access it as
    ```go
    type A struct {
        X
        Yay int
    }
    var a A
    // access it as
    a.p = 10
    a.q = 20
    a.yay = 30
    ```

- For both same output

    ```go
    fmt.Printf("%#v\n", a)    // main.A{X:main.X{p:10, q:20}, yay:30}
    ```

- Struct to JSON a.k.a Marshalling.

    ```go
    import "encoding/json"
    data, err := json.Marshal(structObject)
    ```
    
- We can control the behavior of the marshalling of the structure using *field tags*. So when doing marshalling instead of using the struct variables it uses, variable names from key value pair `json:"foo"`. For below example Myth will be replaced by `illusion` and Virtual will be replaced by `vfx`, also `omitempty` will omit this field if the value is zero type.

    ```go
    type foo struct {
        Real string
        Myth string `json:"illusion"`
        Virtual string `json:"vfx,omitempty"`
    }
    ```

- JSON to struct a.k.a UnMarshalling

    ```go
    var foo []struct{ Name string}
    err := json.Unmarshal(data, &foo)
    ```
    
- This will unmarshal only the name part.

- During unmarshalling the matching process of JSON object name to Struct field name is case-insensitive, you will have to use the field tag only when the JSON object has underscore and your object does not have it.
