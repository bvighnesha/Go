# Summary

> Goroutines

* All Go programs have at least one goroutine: the one that calls the `main` function when the program starts.
* Go programs end when the `main` goroutine stops, even if other goroutines have not completed their work yet.
* The `time.Sleep` function pauses the current goroutine for a set amount of time.
* Go makes no guarantees about when it will switch between goroutines, or how long it will keep running one goroutine for. This allows the goroutines to run more efficiently, but it means you can’t count on operations happening in a particular order.
* Function return values can’t be used in a `go` statement, because the return value wouldn’t be ready when the calling function attempted to use it.
* If you need a value from a goroutine, you’ll need to pass it a channel to send the value back on.

> Channels

* Channels are created by calling the built-in `make` function.
* Each channel only carries values of one particular type; you specify that type when creating the channel. `myChannel := make(chan MyType)`
* You send values to channels using the `<-` operator: `myChannel <- "a value"`
* The `<-` operator is also used to receive values from a channel: `value := <-myChannel`

