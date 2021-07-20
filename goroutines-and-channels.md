# Goroutines and Channels

Concurrent vs Parallel  
  
Concurrent: Individual parts of the program run independently at different times.  
Parallel: Individual parts of the program run independently at the same time.  
  
New goroutines are created by the `go` statement. Syntactically, a `go` statement is an ordinary function or method call prefixed by the keyword `go`. A `go` statement causes the function to be called in a newly created goroutine. The `go` statement itself completes immediately.  
  
_channels_ are the connections between goroutines.  
  
A channel is a communication mechanism that lets one goroutine send values to another goroutine. Each channel is a conduit for values of a particular type, called the channel’s _element type_.   
  
To create a channel, we use the built-in `make` function.  
  
a channel is a _reference_ to the data structure created by `make`. When we copy a channel or pass one as an argument to a function, we are copying a reference, so caller and callee refer to the same data structure. As with other reference types, the zero value of a channel is `nil`.  
  
Two channels of the same type may be compared using `==`. The comparison is true if both are references to the same channel data structure. A channel may also be compared to `nil`.  
  
A channel has two principal operations, _send_ and _receive_, collectively known as _communications_.   
A send statement transmits a value from one goroutine, through the channel, to another goroutine executing a corresponding receive expression.   
Both operations are written using the `<-` operator.   
In a send statement, the `<-` separates the channel and value operands.   
In a receive expression, `<-` precedes the channel operand.   
IMPORTANT: A receive expression whose result is not used is a valid statement.

```text
ch <- x  // a send statement

x = <-ch // a receive expression in an assignment statement
<-ch     // a receive statement; result is discarded
```

Channels support a third operation, _close_, which sets a flag indicating that no more values will ever be sent on this channel; subsequent attempts to send will panic.  
  
Receive operations on a closed channel yield the values that have been sent until no more values are left; any receive operations thereafter complete immediately and yield the zero value of the channel’s element type.

To close a channel, we call the built-in `close` function:

```text
close(ch)
```

A channel created with a simple call to `make` is called an _unbuffered_ channel, but `make` accepts an optional second argument, an integer called the channel’s _capacity_. If the capacity is non-zero, `make` creates a _buffered_ channel.

```text
ch = make(chan int)    // unbuffered channel
ch = make(chan int, 0) // unbuffered channel
ch = make(chan int, 3) // buffered channel with capacity 3
```

UNBUFFERED CHANNELS:  
  
A send operation on an unbuffered channel blocks the sending goroutine until another goroutine executes a corresponding receive on the same channel, at which point the value is transmitted and both goroutines may continue.   
Conversely, if the receive operation was attempted first, the receiving goroutine is blocked until another goroutine performs a send on the same channel.  
  
Communication over an unbuffered channel causes the sending and receiving goroutines to _synchronize_. Because of this, unbuffered channels are sometimes called _synchronous_ channels.   
When a value is sent on an unbuffered channel, the receipt of the value _happens before_ the reawakening of the sending goroutine.  
  
Channels can be used to connect goroutines together so that the output of one is the input to another. This is called a _pipeline_.  
  
UNIDIRECTIONAL CHANNEL TYPES:  
  
Conversions from bidirectional to unidirectional channel types are permitted in any assignment. There is no going back, however: once you have a value of a unidirectional type such as `chan<- int`, there is no way to obtain from it a value of type `chan int` that refers to the same channel data structure.  
  
BUFFERED CHANNELS:  
  
A buffered channel has a queue of elements. The queue’s maximum size is determined when it is created, by the capacity argument to `make`.   
The statement below creates a buffered channel capable of holding three `string` values.

```text
ch = make(chan string, 3)
```

![An empty buffered channel.](https://learning.oreilly.com/library/view/the-go-programming/9780134190570/graphics/emptychannel.png)

A send operation on a buffered channel inserts an element at the back of the queue, and a receive operation removes an element from the front.   
If the channel is full, the send operation blocks its goroutine until space is made available by another goroutine’s receive.   
Conversely, if the channel is empty, a receive operation blocks until a value is sent by another goroutine.

We can send up to three values on this channel without the goroutine blocking:

```text
ch = make(chan string, 3)
```

```text
ch <- "A"
ch <- "B"
ch <- "C"
```

At this point, the channel is full, and a fourth send statement would block.  
.

![A full buffered channel.](https://learning.oreilly.com/library/view/the-go-programming/9780134190570/graphics/fullchannel.png)

If we receive one value,

```text
fmt.Println(<-ch) // "A"
```

the channel is neither full nor empty so either a send operation or a receive operation could proceed without blocking. In this way, the channel’s buffer decouples the sending and receiving goroutines.

![A partially full buffered channel.](https://learning.oreilly.com/library/view/the-go-programming/9780134190570/graphics/nonemptychannel.png)

In the unlikely event that a program needs to know the channel’s buffer capacity, it can be obtained by calling the built-in `cap` function:

```text
fmt.Println(cap(ch)) // "3"
```

When applied to a channel, the built-in `len` function returns the number of elements currently buffered. Since in a concurrent program this information is likely to be stale as soon as it is retrieved, its value is limited, but it could conceivably be useful during fault diagnosis or performance optimization.

```text
fmt.Println(len(ch)) // "2"
```

After two more receive operations the channel is empty again, and a fourth would block:

```text
fmt.Println(<-ch) // "B"
fmt.Println(<-ch) // "C"
```

Go will handle multiplexing goroutines onto OS threads for you. The algorithm it uses to do this is known as a _work stealing_ strategy.



