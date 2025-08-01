## Slices

a dynamically-sized, flexible view into the elements of an array.
> data structure with two elements: a length and a pointer to an element of an array.

Zero value of a slice = `nil`

_Three word data structure_
| pointer | length | capacity |

```go

type sliceHeader struct {
    Length        int
    Capacity      int
    ZerothElement *byte
}

//a function that takes a slice as an argument,
// receives an slice header 
// `Capacity` field records how much space the underlying
// array actually has; it is the maximum value of the `Length`
```

if a slice is gotten from an existing Array, its capacity becomes the capacity of the _backing array/slice_ - (minus) _starting index of the slice_

=> Making used of a three element slice definition
=> Slicing does not copy the slice’s data. It creates a new slice value that points to the original array
=> A slice cannot be grown beyond its capacity. Attempting to do so will cause a runtime panic

> [!info]
> a slice doesn’t make a copy of the underlying array. The full array will be kept in memory until it is no longer referenced. Occasionally this can cause the program to hold all the data in memory when only a small piece of it is needed.
> 
> if the capacity of the backing array does not allow for adding elements
> a new array is added and all modifications to existing data maybe lost
> [slice with references](https://github.com/wambua-iv/getting_my_go_up_code/blob/main/slices/modify_alert.go)

`make()`  => allocates a new array and creates a slice header to describe it

Created to the `stack`
#### append()

the underlying array may change if the capacity of the slice is exceeded

__Capacity Check__: 
	append() checks if the current slice has enough capacity to accommodate the new elements.
__No Capacity Change__: 
	If there is enough capacity, the new elements are added to the existing backing array, and the slice's length is updated.
__Capacity Change__: 
	If there is not enough capacity, append() allocates a new, larger backing array (typically double the current capacity), copies the existing elements to the new array, and then appends the new elements. The original slice will still point to the old backing array.




decoding and un-marshalling require pointer schematics


## Arrays

Arrays are a special data structure in Go that allow us to allocate contiguous blocks of fixed size memory.



# Slice weirded 🤪 behavior

Modifications to the contents of a slice is reflected in the original
⇒ Using `append` to change a slice’s length doesn’t get reflected in the original array/slice.
Slice is implemented as a struct with 3 fields:

#1. ⇒ `int` field for length
#2. ⇒ `int` field for capacity
#3. ⇒ pointer to a block of memory


Changes to values in a slice, changes the memory that the pointer points to.
Appending to the copy slice:
	⇒ length changes in the copy slice
	⇒ values stored in the shared by the copy and original
	⇒ length in the original remains unchanged.

The go runtime prevents the original slice from seeing the values since there are beyond the length of the original slice.

Slices passed to a function can have its contents modified, but can’t be resized