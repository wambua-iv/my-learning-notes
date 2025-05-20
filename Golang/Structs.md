Structs defines an __API to constrain and to allow only certain keys__.

> no hierarchy in Golang ⇒ translates to there is no inheritance

~~When defining a type based on another user defined type, methods available to the base type are not accessible to the type defined.~~

User defined types based on in built types can be assigned literals and constants that are compatible with the underlying type.

A struct is a __sequence of named elements__, called __fields__, each of which has a _name_ and a _type_. Field names may be specified explicitly (IdentifierList) or implicitly (EmbeddedField). Within a struct, non-blank field names must be __unique__.

```go 
type person struct {
	name string 
	age int 
	pet string }


var fred person

bob := person{}
// **Both initialize all fields in the struct to their zero values**
```

>You can perform a _type conversion_ from one struct type to another if the fields of both structs have the same names, order, and types. 
>		*for this to work the struct has to be initialized* [[[[Go bits and bytes#Implicit conversion]]


#### Struct composition
A struct type `T` __may not contain a field__ of type `T`, or of a __type containing `T` as a component__, directly or indirectly, if those containing types are only array or struct types.

```go 
// invalid struct types
type (
	T1 struct{ T1 }       // T1 contains a field of T1
	T2 struct{ f [10]T2 } // T2 contains T2 as component of an array
	T3 struct{ T4 }       // T3 contains T3 as component of an array in struct T4
	T4 struct{ f [10]T3 } // T4 contains T4 as component of struct T3 in an array
)

// valid struct types
type (
	T5 struct{ f *T5 }       // T5 contains T5 as component of a pointer
	T6 struct{ f func() T6 } // T6 contains T6 as component of a function type
	T7 struct{ f [10][]T7 }  // T7 contains T7 as component of a slice in an array
```

> Type conversion between types sharing the same underlying type keep the same storage but associate different methods.
> 
> 	>**types that share the same underlying type** can be **converted between each other**, and this conversion:
> 	>- **Does not copy or allocate new memory**, it's a compile-time type change.
> 	>- **Does not carry over methods**, because methods are associated with the _named type_, not the underlying type


>[!tip]
>💡 Create code clarity by providing a name for a concept and describing the kind of data of data that is expected.
⇒ When you have the same underlying data but different sets of operations to perform make use of two types.

## Methods

Functions with receivers

Pointer Receivers

⇒ Method modifies the receiver, the effects of the method to the value received are required to be visible outside the method definition.

Value Receivers

⇒ Effects of the method on the value received are not needed outside the method definition

A `method call` in go is converted into a `function call` 

Method names cannot be _overloaded_. Using the same method names for different methods on the same type is not allowed as it complicates code clarity and readability.


### Method Sets
struct type `S` and a [named type](https://go.dev/ref/spec#Types) `T`, promoted methods are included in the method set of the struct as follows:

- If `S` contains an embedded field `T`, the [method sets](https://go.dev/ref/spec#Method_sets) of `S` and `*S` both include promoted methods with receiver `T`. The method set of `S` also includes promoted methods with receiver `T`.
- If `S` contains an embedded field `*T`, the method sets of `S` and `*S` both include promoted methods with receiver `T` or `*T`.

#### Promoted Methods
Concepts  