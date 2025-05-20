
Empty maps return the zero value of the maps value

`comma ok value` => checking for key presence in a map

ranging over maps is __unpredictable__ 

Reading and making use of maps is always gonna work on copies 
 ##{pointer schematics will not work on maps}## 
 the value being passed around is just a pointer

to make changes on a value in a map, create a copy of the map value, make changes to the value and assign the changed value to the map.

_a map is not safe in a multi-threaded environment_

In built data type that allows for association of one value to another.

⇒ Zero value for a map is `nil`

⇒ maps are not *comparable* using `==` or `≠`

```go
var nonNilMap map[int]string

//short mode
totalWinsAgainst := map[string]int{}

/* 
if the number of key value pairs is known at the start
make use of make
 */
 ages := make(map[string]int, 10)
```

Maps don’t allow for duplicate values to be stored

= Keys used for maps can be comparable types. (`slices` and `maps` cannot be used as keys)

_Comma, ok_ idiom

= assigns results of a map to two variables,

→the first gets the value associated with the key

→the second value returned is a boolean

Maps are like slices in several ways:
* Maps automatically grow as you add key-value pairs to them.
* If you know how many key-value pairs you plan to insert into a map, you can use make to create a map with a specific initial size.
* Passing a map to the `len` function tells you the number of key-value pairs in a map.
* The zero value for a map is nil.

Maps are not comparable. You can check if they are equal to nil, but you cannot check if two maps have identical keys and values using == or differ using !=.

Maps created with make still have a length of 0, and they can grow past the initially specified size.

Maps are useful when you need to organize values using something other than an increasing integer value, such as a name.

When you try to read the value assigned to a map key that was never set, the map returns the zero value for the map’s value type. In this case, the value type is an int, so you get back a 0.