>[!Note]
> 💡 Pointer ⇒ Variable that hold the location in memory where a value is stored.Zero value for a Pointer is _**nil**_  `Derferencing a nil pointer causes a program to fail`
> 🔥Pointers regardless of the type has **always the same amount of memory size**
> `&` ⇒ **Address Operator** precedes a value type and returns the address where the value is stored.
> `*` ⇒ **Indirection Operator** precedes a variable of type pointer and returns the value pointed to
> For methods with pointer receivers, do not access the values using value schematics, the method will not be included in the `method set`

 ```go
x := 5
pToX := &x
z := 5 + *pToX
var x *int

type Printer interface {
	print()
}
type user struct {
	name string
}

u:= user {"Bill"}

entities := []Printer {
	//Store copy of the user value
	u
	//store a copy of the address of the user value
	&u
}
```

For structs, use an `&` before a struct literal to create a pointer instance
a struct with a field of a pointer to a primitive type, you can’t assign a literal directly to the field
When you pass a constant to a function, the constant is copied to a parameter, [which is a variable]. Since it’s a variable, it has an address in memory. The function then returns the variable’s memory address

Since Go is a call-by-value language, the values passed to functions are copies. For nonpointer types like primitives, structs, and arrays, this means that the called function cannot modify the original. Since the called function has a copy of the original data, the original data’s immutability is guaranteed.

Pointers are used for data mutation [function gets a copy of the pointer. This still points to the original data, which means that the original data can be modified by the called function]
 
⇒ Time to pass a pointer is constant across all memory sizes
 
if you want the value assigned to a pointer parameter to still be there when you exit the function, you must de-reference the pointer and set the value

```go
func setToTen(n *int) {
    *n = 10 // dereference the pointer to assign the value
}
```

> [!tip]
> If `p` is a pointer to a struct and you want to access its fields, you need to use `p.FieldName` or dereference the pointer `(*p).FieldName`.
> >You can’t use an `&` before a primitive literal (numbers,booleans and strings) or a constant because they don’t have ***memory addresses***; they exist only at compile time.
> >Pointer to a primitive type ⇒ *declare a variable then point to it*
> >

```go
type path []byte

func (p *path) ToUpper() {
    for i, b := range *p {
        if 'a' <= b && b <= 'z' {
            (*p)[i] = b + 'A' - 'a'
        }
    }
}

func main() {
    pathName := path("/usr/bin/tso")
    pathName.ToUpper()
    fmt.Printf("%s\n", pathName)
}
```

🎯 [Nil pointers] passed to a function will not allow for making the pointer [Non-nil]. You can only [re-assign] if a value is assigned to it

```go
func main() {
var f *int
failedUpdate(f) //passing a nil pointer
}

func failedUpdate (g *int) {
//value of g is nil
x := 10
g = &x //does not change the value that f points to
}
```

🟩 Have a function instantiate and return the struct rather than populating a struct by passing a pointer to into a function.

```go
func makeFoo() (Foo, error) {
f : = Foo{
Field1: “val”]
Field2: 20
}
return f, nil
}

//Don't
func makeFoo(f *Foo) error {
f.Field1 = “val”
f.Field2 = 20
return nil
}
```

- ⇒ Consideration for [pointers] should be made when functions are expecting [interface].
- ⇒ Value types should be favored when returning values from functions
- ⇒ when modifying the pointed to value make use of a pointer receiver.

>[!tip]
>Passing data to a function takes longer as the data gets larger.
>Returning *pointers for smaller data sets takes longer than returning the actual data*. There is a flip *if the data gets larger and it is faster to return a pointer* to a large data set.

🟥 [Pointers are not returned from functions] ⇒ Returning a pointer from a function, the memory that the pointer points will be no-longer available.
🟥 Data that the pointer points to should be a local variable with a pre-determined size at run time.