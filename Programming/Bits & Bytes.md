
Using [bit shifting] is a common and efficient way to multiply (or divide) by powers of [2] in programming, especially in low-level or performance-critical code.
	- Left shift (`<<`) multiplies a number by 2 for each shift.
    - Right shift (`>>`) divides a number by 2 for each shift (integer division).
Works for [integers]

| Shift Type           | Fills with          | Preserves Sign | Commonly Used For                  |
| -------------------- | ------------------- | -------------- | ---------------------------------- |
| **Logical Shift**    | Fills with `0`      | ❌ No           | Unsigned integers                  |
| **Arithmetic Shift** | Fills with sign bit | ✅ Yes          | Signed integers (two's complement) |

| Shift | Signed Type (`int`)          | Unsigned Type (`uint`) |
| ----- | ---------------------------- | ---------------------- |
| `>>`  | Arithmetic (sign-preserving) | Logical (fills 0s)     |
| `<<`  | Logical (can change sign)    | Logical                |

- **Left shifting a negative signed value** can lead to undefined behavior in C. 
- In Go, left shifting beyond the type size causes a compile-time error (Go is safer).
#### [Two's compliment]
=> used to represent signed numbers +ve & -ve
n bits = `-2^(n-1) => 2^(n-1)-1` 

#### [unsigned numbers]
=> represent non-negative integers
n bits = `0 => 2^n - 1`

integer overflow 
	=> occurs when a value exceeds the limits of the integer type
	=> overflows do not trigger program panics
	


two's compliments and unsigned operations in C results in unsigned numbers
 ```c
 unsigned i
for (i = n, i>= 0){
	f(a[i])
}

// loop would run forever
// unsigned integers will always be equal 0 will never go below that
```

Golang => unsigned and signed arithmetic operations are not supported


Always design a thing by considering it in its next larger context


### [type casting at the parameter level]
