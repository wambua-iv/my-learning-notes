>[!tip]
interfaces are satisfied implicitly
>Interfaces should attempt to describe behavior
>Interface should be used when there is more than one aspect of implementation of the interface
>>[!note]
>>Do not begin writing code by creating interfaces, interfaces should be discovered when there is shared behavior
>>Attempt to not export `interfaces`

*The bigger the interface, the weaker the abstraction.*.[adding abstractions make code harder to read]
 
common use cases for interfaces
	- Common behavior
	- Decoupling
		- implementation itself can be replaced with another without even having to change our code
	- Restricting behavior


performance overhead can occur when calling a method through an interface. It requires a lookup in a hash table’s data structure to find the concrete type an interface points to

An interface should live on the consumer side in most cases.
	it’s up to the client to decide whether it needs some form of abstraction and then determine the best abstraction level for its needs
	=> client should be forced to depend on methods it doesn’t use

[Returning interfaces]
	=> Be conservative in what you do, be liberal in what you accept from others.
		- Returning structs instead of interfaces
		- Accepting interfaces if possible


You cannot create a **new instance** of a variable that’s specified by an interface, nor can you specify that two parameters of the same interface type are also of the same concrete type.
→ interfaces do not have any concrete implementation or state

```go
type Stack[T any] struct {
    vals []T
}
//Type parameter information is placed within brackets and has two parts
		//=> 1: type parameter name
		//=> 2: type constraint, which uses an interface to specify which types are valid
		
	//generic function
	func divAndReminder[T integer](num, deno) (T,T, error) {
		if deno == 0 {
				return 0,0, errors.New("cannot divide by zero")
				}
			return num/deno, num % deno, nil 
		}
```


generics not based on basic types [types that can be nil]can not return [nil] *because that’s not a valid value for all value types*

The easiest way to get a zero value for a generic is to simply declare a variable with var and return it,

⇒ the empty interface says nothing <`interface{}`>/`any`
	= You can only store and retrieve values of the any type

### Type Constraints

```go
type interger interface { 
	~int | ~int8 | ~int64 
	}
```

interface with type elements can only be used as type constraints
The `tilde` is used to specify that a type parameter can be any type that has the same underlying type as the specified type.
without the `tilde` the interface restricts all the types whose underlying type is the the given type.