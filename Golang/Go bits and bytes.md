 Emphasis on code clarity and readability against unnecessary complexity
 [happy path column]
Go does not have a concept of absence of value ⇒ representing a null value may necessitate creating a nil pointer
The fastest way to read from memory is to read it sequentially. A slice of structs in Go has all the data laid out sequentially in memory. This makes it fast to load and fast to process.
## Pre-declared Identifiers 

In Go, **predeclared identifiers** are special names that are built into the language. They serve specific purposes and are not reserved words, allowing the set to expand without invalidating existing programs. Here are some notable predeclared identifiers:

1. **`true`** and **`false`**: Untyped boolean values.
2. **`iota`**: Represents the untyped integer ordinal number of the current const specification in a const declaration.
3. **`nil`**: The zero value for pointers, channels, functions, interfaces, maps, or slices.
4. **Types**: **`bool`**, **`byte`**, **`complex64`**, **`complex128`**, **`error`**, **`float32`**, **`float64`**, **`int`**, **`int8`**, **`int16`**, **`int32`**, **`int64`**, **`rune`**, **`string`**, **`uint`**, **`uint8`**, **`uint16`**, **`uint32`**, **`uint64`**, and **`uintptr`**.
5. **Functions**: <span class="purple"> **`append`**</span>, **`cap`**, **`copy`**, **`delete`**, **`imag`**, **`len`**, **`make`**, **`new`**, **`panic`**, **`print`**, **`println`**, **`real`**, and **`recover`**.

⇒ _You must be very careful to **never redefine any identifiers in the universe block**_


> [!NOTE] Shadowing
>  `:=` reuses only variables that are declared in the current block. When using `:=`, make sure that you don’t have any variables from an outer scope on the lefthand side unless you intend to shadow them.
> 	 -> be cautious with shadowed variable, out values may not have the value assigned
> 	 => Assess variable shadowing against temporary variable creation


```go 
// variable shadowing
var client *http.Client
//simulate a tracing condition
if tracing {
	client, err := createCleintWithTracing()
	// coded
} else {
	client, err := clientWithoutTracing()
}

```
 => Results in an error `client is declared but not used`
	  -> the variables in the {} blocks are local to their scope
`var` keyword initializes a variable to its zero value

```go
// pointer schematic
for i := range friends {
	friends[1] = "James"
}
//this version of the range function works on the original data structure

for i, num := range friends {
	friends[1] = "Tommy"
}
/// in this version the for range function works on a copy of the data
```

The [range] expression is evaluated on the first iteration, any updates to the expression value does not modify the number of iterations

evaluation of the range operator is done at the beginning of the loop
	-> During the iteration the value of the loop is not re-evaluated.
	-> To affect the original value, either use pointers or the [pointer schematic] version of the range operator
Do not use `range` to iterate over user defined container types

#### Implicit conversion 

> Implicit conversion occurs when the a __named kind__ is forced on to an anonymous type

```go 
type android struct {
	version int
	YOM string
}
type ios struct {
	version int
	YOM string
}

var moto android
var six ios

moto = ios 
// this will cause a type error
// cannot use six (variable of struct type ios) as android value in assignment

seven := struct {
	version int
	YOM string
}{
	version: 7
	YOM: "2018/10" 
}
seven = moto // there are no errors encountered
```

>[!note]
>calculation of different integer types require explicit conversion
>🟥 Go throws a `mismatched types` error if types are mixed

references types and in-built values use __value schematics__ to move data around in a program
▶️ Avoid creating pointers to slices, channels or interface

_empty slices_

Make use of `var` to create zero value data structures

```go
var es struct{} 
es := struct {}{}
// empty literal notation
```

🟣`append` call => create *__value semantic mutation API__*  
	* append gets its own copy of the slice value
	* It mutates its own copy of the slice
	* returns a new copy out 

Causes for a memory leak in Go
	Gorounties blocked 
		Maps used as a cache, (maps based on an event)

Append works off the length of a slice, appending after the last element in the Array


Does data manipulation lead to a different data structure or modifies the same data structure

pointer schematics are used to manipulate data that is not changing the address type/loaction

> When defining a type create the factory functions and methods together

> [!tip]
> Do not make a copy of a value that is pointed to.
> Do not switch from _pointer schematics_ to _value schematics_ 



> [!note]-  Built in types 
> make use of __value schematics__ to move values around in the program
> Use __value schematics__ for `fields in a struct` 
> `reference types` are to be passed around using __value schematics__
> Reading and writing to `reference types` make use of __pointer schematics__ in functions of decode and unmarshal 
> Struct types => make a choice on the schematics and stick to the chosen schematic type


> [!attention]
>  if the compiler does not know the value of a value at compile time it can not be on the `stack` => frames are fixed at compile time

Golang double in-direction

interfaces are value less 

Behavior depending on concrete data being operated on

interfaces are valueless type  

Embedded types inner method promotion => doesn't happen automatically 

>[!info]
>embedding does not create a relationship

>[!tip]
>Visibility in Go is at the package level. So you can hide details just as easily with separate data types and functions as you can with struct methods. Deciding which to use has more to do with the ergonomics for the caller.
>
Go interfaces let you reuse behavior with lots of different types. If you tightly couple behavior to one concrete data type you lose that ability.[discover structs, not creating them from the beginning]
>	“Program to an interface, not an implementation.”


Attempt to take in concrete data and return the concrete data => ease of testing [avoid returning interfaces]

Only concrete data is moved around and `across program boundaries`

__Runtime checks__




I/O bound workload for concurrency

Arrays of different length cant be assigned to each other

#### init functions
= Cannot be invoked directly
```go
func init (){}

func main(){
	init()  // invalid reference
}
```
Limited error management in an init function => 🗒️ does not [return an error]
When a package is initialized all the constants  and variable declarations in the package are evaluated. 
	- init functions come after this step
	- Multiple init functions can be created in a package
		🔷 - Execution order of the init function inside the package is based on the source files alphabetical order

🟩 use cases
	creating side effects


Perhaps a little bit of duplicated code might occasionally be better if it improves other aspects such as code expressiveness.

🔷 Smallest amount of memory that can be addressed is a *[byte]*


[Breaking Golang change]
	When using Go 1.22 or later, if the go directive is set to 1.22 or higher, a for loop creates a new index and value variable on each iteration. This behavior is applied per module.

>[!note]
>**When unmarshaling from JSON into a struct field with no `json` tag, the name match is case-insensitive. When marshaling a struct field with no `json` tag back to JSON, the JSON field will always have an uppercase first letter, because the field is exported.**
>If a field should be ignored when marshaling or unmarshaling, use a dash (`-`) for the name. If the field should be left out of the output when it is empty, add `,omitempty` after the name. For example, in the `Order` struct, if you didn’t want to include `CustomerID` in the output if it was set to an empty string, the struct tag would be `json:"customer_id,omitempty"`.

```go 
type config struct {
	pgxConn string
}

func (c *config) configure() {
	verbing := new(config)
	*verbing = *c
	fmt.Println(*verbing)
}

func main() {
	fmt.Println("Hello, 世界")
	verbed := config{
		pgxConn: "thinking",
	}
	verbed.configure()

}
```