# Concurrency

## Notes

* A **semaphore** is like an integer, with three differences:
  * it can be initialised to any integer value, but the only allowed operations are _increment_ and _decrement_ (you **cannot** read the current value of the semaphore)
  * when a thread decrements the semaphore, if the result is negative, the thread blocks itself until another thread increments the semaphore
  * when a thread increments the semaphore, if there are other threads blocked, one of them gets unblocked
* The simplest use for a semaphore is **signaling**, which means one thread sends a message to another thread to indicate that something has happened.
* A second common use for a semaphore is to enforce mutual exclusion (**mutex**) around some portion of the code called **critical section**. When a single thread is allowed to execute (so the semaphore is initialised with the value 1) we have a mutex, when `n` threads are allowed to execute we have a **multiplex**.
* A **rendezvous** is a synchronisation problem for which two threads have to reach a certain point in the execution, and neither is allowed to proceed further until both have arrived. The generalisation for `n` threads instead of just two is called a **barrier**.
* A **deadlock** is a situation in which one thread is holding a resource and waiting for another to be available, while at the same another thread is holding the resource the first thread wants and is waiting on the resource held by the first thread. This situation must be avoided, as it will halt indefinitely the execution of a program.
* Another situation to avoid as much as possible is called **starvation**: one or more threads might be waiting forever to enter in a critical section while other threads are able to carry on with theirs. In order to avoid this situation, we enforce a requirement of **bounded waiting**, which means the time a threads waits on a resource has to be provably finite.

## Resources

### Articles

* [How does a mutex work? What does it cost?](https://mortoray.com/2019/02/20/how-does-a-mutex-work-what-does-it-cost/) - Edaqa Mortoray

### Books

* [Concurrency in Go: Tools and Techniques for Developers](https://www.amazon.co.uk/dp/1491941197) - Katherine Cox-Buday
* [Java Concurrency in Practice](https://www.amazon.co.uk/dp/0321349601) - Brian Goetz
* [The Little Book Of Semaphores](https://greenteapress.com/semaphores/LittleBookOfSemaphores.pdf) - Allen B. Downey

### Images

<figure><img src="../.gitbook/assets/Top 6 Multithreading Design Patterns You Must Know.gif" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/What is a Deadlock.gif" alt=""><figcaption></figcaption></figure>

### YouTube Videos

* [Santa gave me a concurrency problem for Christmas...](https://www.youtube.com/watch?v=zwUzulwiDpI) - LaurieWired
