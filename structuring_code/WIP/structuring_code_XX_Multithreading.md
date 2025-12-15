# Multithreaded concurrent/parallel execution
We won't be doing any multithreading in this tutorial, but talking about is important, as the code structure that is presented takes this consideration into account.

In the following I will sometimes use the term transaction to mean the running of an exec.

## Structure of Multithreaded Execs
In a multithreaded setting we have a constraint on the structure of execs.
```
(exec (<THREAD_ID> <PRIORITY>) <SOURCES> <SINKS>)
```
`(<THREAD_ID> <PRIORITY>)` is required to be ground (not contain variables).

The runtime then uses predication enforce a thread prefix.
(prefixes are mentioned in [Data in Mork](https://github.com/trueagi-io/MORK/wiki/Data-in-MORK))

The thread prefix for a `<THREAD_ID>` of `thread-1` looks like this:  
`(exec (thread-1 $<PRIORITY>) $<SOURCES> $<SINKS>)`

The runtime sees it like this (where `[_]` is an arity tag):
`[4] exec [2] thread-1`

Once a thread starts running nothing can be written to that prefix other than that thread.

Priority is then a "thread local" property rather than a global one.

### Permission Prefixes

Prefixes are used as a permission to read an write a part of the global state. Threads then race to acquire permissions for their transactions.
Sources behave as readers, and Sinks as writers. Threads can share reads, but not writes. 
The runtime derives "constant" prefixes dynamically to keep contention as low as possible.

As an example:
```
(exec (thread-1 0) (, (path-file (((/ home) / code) / mork) $x) 
                      (extension .mm2 $x)
                   )
                   (, (.mm2 $x)
                   )
)
```
Thread prefix
- `[4] exec [2] thread-1` 
Priority
- `0` 
Read permission prefixes
- `[3] path-file [3] [3] [2] / home / code / mork`
- `[3] extension .mm2`
Write permission prefix
- `[2] .mm2`

Note how the permission prefixes stop before the variable.
If we had a reverse index of `path-file` called `file-path` and used that instead to get the filename:
```
(file-path $x (((/ home) / code) / mork)) 
```
the derived read permission prefix would be:
- `[3] file-path`
The fact that it is shorter is very important; more of the global state will be in-accessible for writes (everything below that prefix) until the transaction is complete.

Within a transaction, reads and writes can have overlapping permissions.  

It is therefore possible to reason about the behavior as follows:
- Look up next exec in current thread (biased by Priority)
- Derive the the set of read and write permissions prefixes.
- _Attempt_ to acquire all needed permissions for the transaction.
  - If successful, transact
    - make reads
    - release read permissions when reads are complete
    - run with write permissions
    - release write permissions on completion of transaction
    - Reset Priority to top
  - On failure defer current transaction

## Writing Multithreaded Oblivious code
One could write all single threaded priorities as pairs in the `(<THREAD_ID> <PRIORITY>)` form and the single threaded behavior would be equivalent.


### Sequential footguns

Using multithreaded concurrent/parallel execution (like in the server branch) the priority does not guarantee determinism, Priority still determines which execs will _attempt_ to run first.
If an exec cannot run (cannot acquire read/write permissions yet), it is not exhausted, instead it is deferred, and the next highest priority is attempted.  
This is why it is a _priority_ and not a _sequence_.  

To write MM2 in a way that will work in both single-threaded and multi-threaded context correctly, one cannot simply rely on solely on priorities for sequencing.

Working with exec chaining alone won't be enough either.  
Using multithreaded concurrent/parallel execution we would have to consider the following case.

We would like to have these 2 threads communicate do do work together.
```
; guarantees execution of initial execs
t

; thread 1
(exec (thread-1 0) (, t)
                   (, 
                      (t 1)
                      (exec (thread-1 0) (, (t 2)   ) 
                                         (, (t 2 1) )
                      )
                   )
)

; thread 2
(exec (thread-2 0) (, t) 
                   (, 
                      (t 2)
                      (exec (thread-2 0) (, (t 1)   )   
                                         (, (t 1 2) )
                      )
                   )
)
```
Ideally they would both run, `thread-1` and `thread-2` would write `(t 1)` and `(t 2)` at the same time.
Then the spawned execs would both expect to see each other's message,  
and then they would both write `(t 2 1)` and `(t 1 2)` respectively.

The desired output
```
(t 1)
(t 2)
(t 1 2)
(t 2 1)
t
```
But let's try to run this single threaded.

run 
```sh
./mork run --steps 2 Multithreading_Exec_Race_Seq.mm2
``` 
we get this output
```
(t 1)
(exec (thread-2 0) (, t) (, (t 2) (exec (thread-2 0) (, (t 1)) (, (t 1 2)))))
t
```

We find that `thread-1` failed to find `(t 2)` with it's spawned exec. It then exhausted itself.

In this simulation, `thread-2` hasn't made progress yet.
Once it finally does, our output will be "incomplete", missing `(t 2 1)`.
```
(t 1)
(t 2)
(t 1 2)
t
```

### Shared State, Private state
It should be clear that the the issue with permissions has to do with sharing. Any form of shared information can cause a sequence to fail.

Either because a transaction
- was delayed by a permission failure
- ran prematurely exhausting itself

A convention would be needed to predicate what part of the global space is private to a thread. It could be as simple as having a predication of this form:
- `(thread-local <THREAD_ID> <LOCAL_DATA>)`
given that a `<THREAD_ID>` is ground the derived prefix would be
- `[3] thread-local <THREAD_ID>`

So long as threads only read/write to their own id, this is effective.

This _almost_ guarantees sequencing by itself if a thread only accesses local data.

_Almost_ as one might be tempted to debug programs by reading from a running thread's locals. That read can interfere with the sequence, by permission failure, when the thread tries to write at that prefix. 

This would guarantee however that a thread can store things like program counters, state labels.

Once a program counter or a state label has been changed, execs can be engineered to go from succeeding to match, to failing to match local state. This includes recursive execs...

### Ordered Cycles

# WIP