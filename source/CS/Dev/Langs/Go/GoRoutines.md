#reference

**Goroutines** = hilos ligeros gestionados por el runtime de Go, no por el kernel. Comparten el hilo del programa, evitando estructuras y el costoso *context switching*. Una goroutine ocupa ~4 KB frente a ~1 MB de un hilo del SO → se pueden lanzar **cientos de miles** sin despeinarse.

> "All routines share the same thread as the main program... a goroutine allocates around 4KB of memory, whereas an OS thread roughly takes 1MB."

# Lanzar goroutines

```go
go tarea()                    // ejecuta tarea() en paralelo
go func() { fmt.Println("anónima") }()
```

> El `main` no espera a las goroutines: si termina, mueren. Hay que sincronizar.

# Channels (comunicación)

Go favorece "comunicar para compartir memoria", no "compartir memoria para comunicar".

```go
ch := make(chan int)        // canal sin búfer (síncrono)
ch := make(chan int, 10)    // con búfer (capacidad 10)

ch <- 42                    // enviar (bloquea si lleno)
v := <-ch                   // recibir (bloquea si vacío)
close(ch)                   // cerrar

for v := range ch { ... }   // recibir hasta que se cierre
```

# Sincronización

```go
// WaitGroup: esperar a N goroutines
var wg sync.WaitGroup
for i := 0; i < 5; i++ {
    wg.Add(1)
    go func(n int) { defer wg.Done(); trabajo(n) }(i)
}
wg.Wait()

// Mutex: proteger estado compartido
var mu sync.Mutex
mu.Lock(); contador++; mu.Unlock()
```

# select (multiplexar canales)

```go
select {
case v := <-ch1: fmt.Println(v)
case ch2 <- x:   fmt.Println("enviado")
case <-time.After(time.Second): fmt.Println("timeout")
default:         fmt.Println("nada listo")
}
```

# Patrones

| Patrón | Idea |
|--------|------|
| **Worker pool** | N goroutines consumen de un canal de tareas. |
| **Fan-out/fan-in** | Repartir trabajo y reunir resultados. |
| **Pipeline** | Encadenar etapas vía canales. |
| **Context** | `context.Context` para cancelación/timeout propagados. |

> Detectar carreras de datos: `go run -race main.go`.

# Recursos
### [[Go 101]]
### [Go by Example — Goroutines](https://gobyexample.com/goroutines) · [Concurrency patterns](https://go.dev/blog/pipelines)
