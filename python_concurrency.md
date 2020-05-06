# Concurrency in Python

General rule of thumb: `multiprocessing` for CPU-bound tasks, `asyncio` for 
I/O-bound tasks when available, `gevent`/`threading` for I/O-bound tasks when
necessary. 

## Threading
- Runs on single processor
- Uses OS-level threads
- Good for parallelizing I/O-bound tasks
- Have to worry about deadlocks/race conditions/thread-safe shared data
  structures

## Gevent
- Runs on single processor
- Uses process-level (green) threads (aka coroutines)
- Monkey patches common libraries to have non-blocking I/O
- Good for parallelizing I/O-bound tasks
- No CPU context switching like with `threading`
- Have to worry about deadlocks/race conditions/thread-safe shared data
  structures

## Asyncio
- Runs on single processor
- Uses event-loop
- Good for parallelizing I/O-bound tasks
- No CPU context switching like with `threading`
- No race conditions like with `threading` and `gevent`
- Not all third party libraries support `asyncio` yet
- Long running synchronus tasks might hog the event loop, preventing
  other async tasks from being started/executed

## Multiprocessing
- Runs on multiple processors
- Good for parallelizing CPU bound tasks
- More overhead to start separate processes than threads

## Celery
- Can run on multiple processors ([worker concurrency setting](https://docs.celeryproject.org/en/stable/userguide/workers.html#concurrency)),
  or on multiple green threads ([eventlet support](https://docs.celeryproject.org/en/stable/userguide/concurrency/eventlet.html)),
  or mix both types of workers
- Can run on multiple hosts

## References

- https://realpython.com/python-concurrency/
- https://medium.com/@nhumrich/asynchronous-python-45df84b82434
