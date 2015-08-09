+++
categories = ["Go", "Programming", "Game"]
date = "2015-08-09T18:19:14+02:00"
description = "An example of how go's buffered channels may be used for implementing a game loop."
draft = false
keywords = []
title = "Go buffered channels for game loops"

+++

So previously I was reading a great blog post about game loops over at 
[Koonsolo.com](http://www.koonsolo.com/news/dewitters-gameloop/). Although 
the post is from 2009, it still provided some great examples of different
techniques and the good and bad about each implementation. 

**Note** it is recommended that you read that post before reading any further.

In the last of the implementations, the updating of the game state was done 25 
times per second. No more, no less. This means that the rendering function had
to either interpolate between the current and the last state, or just make a
qualified guess about what to render based on the last state (this is what is
actually done in the example). If the rendering function is slower than 25 FPS,
then the speed of the game will still be normal, since updating is done at 
a steady 25 updates per second. This solution seems ideal for almost any game,
and that is why I choose it for this example implementation in Go.

Since go has built in support for various concurrency features such as channels,
I thought it would make sense to do the updating of the game in its own
goroutine, and then send back an updated state through a channel.

First we make a new type called a `gameState`. Because this is just an example,
We just assume the gameState is a `float64` containing the position of a player.

~~~ go
type gameState float64
~~~

Now for the update function, the only input parameter should be a send-only 
channel of the gameState type.

~~~ go
func updateGame(c chan<- gameState) {
	//update logic goes here
}
~~~

To make the function run at a steady 25 ticks per second, we will use go's 
`time` package and the Tick function from that package. time.Tick() takes 
a duration as a parameter, and returns a channel on which the current time will
be sent to on every tick. For our example, the duration should be `time.Second / 25`

In go, it is possible to range over a channel so the code inside the for loop
will run once at every tick, and that is excatly what we want. 

~~~ go
func updateGame(c chan<- gameState) {
	updateTicker := time.Tick(time.Second / TPS)
	var state gameState
	state = 0.0
	const playerSpeed = 20.0
	for range updateTicker {
		state = state + (playerSpeed / TPS)
		c <- state
	}	
}
~~~

This small example updates the game state by adding some constant to it 
at every tick. It then sends the updated state to the channel. (In real life, 
this function would also poll for input events, handle AI and so on, but to
keep it simple it just updates the position of the player).

Now for the main loop, we could implement it like this:

~~~ go
func main() {
	state := make(chan gameState, 10)
	go updateGame(state)
	var lastState, currentState gameState
	
	// make sure current and last state is initialized
	currentState = <-state
	lastState = currentState
	
	lastUpdate := time.Now()
	for gameIsRunning {
		select {
		
		// a new state is ready. Use it
		case s := <-state:
			lastState = currentState
			currentState = s
			lastUpdate = time.Now()
		
		// no new states are ready. Render
		default:
			// This will give us a value from 0 to 1, 
			// based on when the next update will 
			// arrive. We use this for interpolation
			i := time.Since(lastUpdate).Seconds() * TPS
			render(lastState, currentState, i)
		}
	}
}
~~~

The reason we make the channel buffered is simple: If the rendering function 
takes more than 1/25 of a second (we have less than 25 FPS), then a normal
channel would block because a state is already in it. By making space for 10
states, the rendering could happen as slow as 2.5 FPS (25/10) and the game would
still update normally. In the select statement, the states will be pulled of the
channel if there is anything available. If more than one state is available on
the channel, they will all be pulled down so the latest is always used for 
rendering.

Conclusion
----------

On fast hardware, this game loop should run just fine with no problems at all,
just as in deWiTTERS example. On slow hardware, two problems can arise:

* The update funciton takes more than 1/25 seconds to complete, and the entire
  game will slow down. This problem was also present on deWiTTERS example.
* The rendering function is too slow, and the channeled buffer will become full
  and thereby cause a block. This will lead to a _very_ bad playing experience,
  since the game will update slower, and the framerate will be really low. To 
  fix the problem of the game updating to slow, the size of the buffered channel
  could just be made bigger.

I think this is a great example of what go's concurrency features are capable of,
and I also think that the code for this (very simplified) game loop, is easy to
understand considered it is concurrent.


An example of this code in use is available [here](http://play.golang.org/p/QJ7RlhgR6o) in the go playground.
