Scheduling is unpredictable.
## Channels

Goroutines communicate through channels Allows for gorountines to signal each other

`ch := make(chan int)`

Passing a channel to a function, passes a pointer to the channel.

the zero value for a channel is nil.

Each value written to a channel can be read only once.

If multiple goroutines are reading from the same channel, a value written to the channel will be read by only one of them

channels are reference types in Go

```go
//creating a channel
ch := make(chan int)
//writing to a channel
ch <- b
//reading from a channel
a := <- ch

//closing a channel
close(ch)
```

Go also has `buffered` channels.
These channels buffer a limited number of writes without blocking. 

> allows sending data into the buffer _until the buffer is full_
> >[!tip]
> >used when one want gorountines to execute without waiting on each other

If the buffer fills before there are any reads from the channel, a subsequent write to the channel pauses the writing goroutine until the channel is read. Just as writing to a channel with a full buffer blocks, _reading from a channel with an empty buffer also blocks_

By default, channels are `unbuffered`.
> Have no internal storage
> Used for synchronization
   Every write to an open, un-buffered channel causes the writing goroutine to pause until another goroutine reads from the same channel.

Reading from an open, unbuffered channel causes the reading goroutine to pause until another goroutine writes to the same channel.

This means you cannot write to or read from an unbuffered channel without at least two concurrently running goroutines.

A `buffered channel` is created by specifying the capacity of the buffer when creating the channel:

**`ch := make(chan int, 10)`**

Reading a closed channel will succeed -> reading will not cause the channel to PANIC

Buffered channels with values that have not been read will be returned in order

A buffered or unbuffered channel with no values returns the zero value of the of the channels type
	-> channels support the comma, ok idiom


![[channels.png]]
`select` statement allows for a goroutine to read from of write to one set of multiple channels

```go
select {
case v <- ch
	fmt.Println(v)
case v <- ch2
	fmt.Println(v)
case <- ch4
	fmt.Println("get value on ch4 but it was ignored")
}
//each case is a read or write to a channel
```

ensure that all goroutines exit gracefully.

`for select` ⇒ ensuring that the loop is exited

The select statement has a `default` case

<aside> 💡

Having a `default` case inside a `for-select` loop is almost always the wrong thing to do. It will be triggered every time through the loop when there’s nothing to read or write for any of the cases. This makes your `for` loop run constantly, which uses a great deal of CPU.

</aside>

> Concurrency is an implementation detail, and good API design should hide implementation details as much as possible. This allows you to change how your code works without changing how your code is invoked.
> 
> Practically, this means that you should never expose channels or mutexes in your API’s types, functions, and methods Exposing a channel, puts the responsibility of channel management on the users of your API.
> 
> The users then have to worry about concerns like whether a channel is buffered or closed or `nil`. They can also trigger deadlocks by accessing channels or mutexes in an unexpected order.
> 
> Do not export functions with channel as parameters or structs with channels as fields
 
Ensure goroutines always exit ⇒ the runtime cannot detect that a goroutine will not be re-used and thus memory allocated for the variables on the stack will remain allocated for variables (not garbage collected)

## => Using context to terminate a goroutine

Using buffered channels

Useful when you know how many goroutines will be launched

Want to limit the number of goroutines to be launched

Limiting the amount of work the system has queued up

> Buffered channels work great when: ⇒ gathering data back from a set of goroutines ⇒ limiting concurrent usage. They help manage the amount of work a system has queued up, preventing your services from falling behind and becoming overwhelmed



#### Data Race
Two Goroutines are trying to read and write/ two attempting to write to the same memory location

There are no issues when multiple gorountines what to read from the same memory address



### Mutex

Mutex must called the `lock`  and `unlock`
	_failing to call unlock creates a deadlock_
Mutex creates latency, as there is a wait awaiting the mutex to unlock create application back pressure




## Channel signalling 

if the cancellation pattern is used with a gorountine make use of a buffered channel
> If the un-buffered channel misses a receiver it causes a memory leak