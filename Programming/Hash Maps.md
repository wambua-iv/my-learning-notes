
An associative array ⇒  an abstract data type that is used to store a collection of key and value pairs in such a way that each key appears only once in the collection (maps)

(Hash tables, Self-Balancing Binary Search Trees, Trie, Direct Addressing, )

Properties of Maps

- **Uniqueness**: Keys are unique, meaning no two pairs can have the same key.
- **Mutable**: The contents (keys and values) of associative arrays can be modified.
- **Dynamic Sizing**: The size of an associative array can change dynamically as pairs are added or removed.

abstract data type ⇒ a mathematical model used to describe a data type in terms of:

- operations supported by the data type,
- behavior of these operations
- possible values that can be passed to or received from them.

Supported operations:

 `get` ⇒  find the value (if any) that is bound to a given key. The argument to this operation is the key, and the value is returned from the operation. If no value is found, some lookup functions raise an exception,

 `set` ⇒ add a new (key, value) pair to the collection, mapping the key to its new value. Any existing mapping is overwritten

 `delete`  ⇒ 

### Additional Operations

1. **ContainsKey**: Checks if a specific key exists in the associative array.
2. **ContainsValue**: Checks if a specific value exists in the associative array.
3. **Size or Count**: Returns the number of (key, value) pairs in the associative array.
4. **IsEmpty**: Checks if the associative array is empty.
5. **Clear**: Removes all (key, value) pairs from the associative array.
6. **Keys**: Returns an iterable collection of all keys in the associative array.
7. **Values**: Returns an iterable collection of all values in the associative array.
8. **Entries**: Returns an iterable collection of all (key, value) pairs in the associative array.