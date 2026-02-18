# goroutines
> 	"Think of a goroutine as a set of instructions running in parallel to
	the rest of the code. All routines share the same thread as the main
	program, thus sparing a few data structures and the expensive
	context switching usually performed by the kernel when jumping
	from one thread to another. To give you a practical comparison, a
	goroutine allocates around 4KB of memory, whereas an OS thread
	roughly takes 1MB. You can easily run hundreds of thousands of
	goroutines on a regular computer without breaking a sweat."