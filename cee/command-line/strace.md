# Strace

## Notes

* `strace` is a Linux-only program that lists all the system call that a program is doing
* Base usage: `strace <program-to-trace>` (e.g., `strace ls`)
* Each line of `strace` 's output will contain
  * the process ID
  * the name of the system call
  * the system call arguments
  * the return value
* Useful flags:
  * `strace -e <system call> <program>` will only display the calls to the system call passed as argument (e.g., `strace -e open` will only list the calls to the `open` system call)
  * `strace -f <program>` will follow the subprocesses started by the original program (`-f` stands for follow)
  * `strace -o <output-file>` will redirect the output to the specified `<output-file>`
  * `strace -p <pid>` will list the system calls for a live process
  * `strace -s <num-chars>` will show the first `<num-chars>` characters of each string
  * `strace -z` will only show system calls that returned without an error code
  * `strace -Z` will only show system calls that returned with an error code

## Resources

### Articles

* [Strace: The Lost Chapter](http://price.mit.edu/blog/2010/08/strace-the-lost-chapter/) - Greg Price
* [Strace - The Sysadmin's Microscope](https://web.archive.org/web/20200213190655/https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2) - Greg Price

### Websites

* [Julia Evans](https://jvns.ca/categories/strace/)

