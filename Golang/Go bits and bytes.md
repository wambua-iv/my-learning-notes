
Go does not have a concept of absence of value
representing a null value may necessitate creating a nil pointer

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


references types and in-built values use __value schematics__ to move data around in a program
:wavy_dash: Avoid creating pointers to slices, channels or interface

_empty slices_

Make use of `var` to create zero value data structures

```go
var es struct{} 
es := struct {}{}
// empty literal notation
```

:purple_square: `append` call => create *__value semantic mutation API__*  
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
Go interfaces let you reuse behavior with lots of different types. If you tightly couple behavior to one concrete data type you lose that ability.


Attempt to take in concrete data and return the concrete data => ease of testing  

Only concrete data is moved around and `across program boundaries`

__Runtime checks__

>“Program to an interface, not an implementation.”


I/O bound workload for concurrency

Arrays of different length cant be assigned to each other