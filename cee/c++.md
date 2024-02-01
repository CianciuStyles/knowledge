# C++

## Notes

* In order to translate a C++ program (as a plain text file) into an executable program, two main operations need to happen:
  * _compilation_: C++ source files are converted into an intermediate format called object files;
  * _linking_: find where each symbol is and link them together - reconstruct a single executable application from many individual object files.
* Pre-processor directives include:
  * `#include`: when the pre-processor finds this directive, it will copy and paste the contents of the file being included;
  * `#define`: when the pre-processor finds this directive, it will replace the macro with its definition (a macro can also have arguments);
  * `#if` ... `#end`: the processor will or will not include the portion of code between these two directives depending on whether the condition of the directive is true (1) or false (0)
* Header files are useful to share definitions among different files, so that it is possible to use in each compilation unit (=C++ file) variables, classes, and functions defined somewhere else.
* To avoid including multiple times the same header files (and hence definitions) in a compilation unit, there are two main techniques:
  * the `#pragma once` directive (non-standard but almost universally supported)
  * an include guard - usually implemented via a `#ifndef` ... `#endif` block:

```cpp
// my_class.h
#ifndef MY_CLASS_H // include guard
#define MY_CLASS_H

// Contents of MyClass
// ...

#endif /* MY_CLASS_H */
```

* Includes with the double quotes (e.g., `#include "Log.h"`) are used for header files in the same directory as the source file, while includes with angular brackets (e.g., `#include <iostream>`) are used for standard library headers.
* A _pointer_ is an integer variable containing a memory address. It is possible to retrieve the address of a variable with the ampersand operator (e.g., `int* ptr = &myvar`) and it's possible to get back the value contained at the address stored in the pointer with the asterisk operator (e.g., `int mynewvar = *ptr`).
* A _reference_ is syntax sugar on top of pointers to make it a bit easier to work with: it is not a variable with storage requirements, but simply a reference to an existing variable. It is denoted with an ampersand next to the type (e.g., `int& reference = a` where `a` is a variable of type `int`). It is useful especially in function declarations to pass arguments _by reference_ (i.e., with a pointer) rather than _by value_ (i.e., creating a copy of the value to be used inside the function).
* Classes allow to define types containing both data and functions (called methods) that act upon them. The only difference between a class and a struct is that, by default, all members of a class are _private_, while all members of a struct are _public_.

```cpp
class Player {
public:
    int x, y;
    int speed;
    
    void Move(int xa, int ya) {
        x += xa * speed;
        y += ya * speed;
    }
};

int main() {
    Player player;
    player.Move(1, -1);
}
```

* The static variable has two different meanings, depending on the context where it's used:
  * inside classes, a static variable/method will be shared among all instances of the class where the static variable is defined (NB a static method can only access static variables in its body);
  * outside classes, static means the linkage of the symbol declared as static will be limited to the translation unit (=C++ file) where it was defined.
* The _constructor_ is a special method of a class that gets automatically called when a new object is created, and is used to properly initialise all member variables of an object. The _destructor_, instead, is the special method that will be called when an object is about to be destroyed (either by exiting its scope or when the operator `delete` is called).

## Resources

### Books

* [Modern C++ Tutorial: C++ 11/14/17/20 On the Fly](https://changkun.de/modern-cpp/en-us/00-preface/) - Changkun Ou ([GitHub](https://github.com/changkun/modern-cpp-tutorial))

### Frameworks

* [U++](https://www.ultimatepp.org/index.html) - Cross-Platform App Development Framework

### GitHub repositories

* [fffaraz/awesome-cpp](https://github.com/fffaraz/awesome-cpp)
* [filipdutescu/modern-cpp-template](https://github.com/filipdutescu/modern-cpp-template)
* [LearnCPP](https://github.com/Lakhankumawat/LearnCPP)
* [Modern C++ Programming Course (C++11/14/17/20/23)](https://github.com/federico-busato/Modern-CPP-Programming)
* [mortennobel/cpp-cheatsheet](https://github.com/mortennobel/cpp-cheatsheet)
* [muqsitnawaz/modern-cpp-cheatsheet](https://github.com/muqsitnawaz/modern-cpp-cheatsheet)
* [rigtorp/awesome-modern-cpp](https://github.com/rigtorp/awesome-modern-cpp)
* [The C++ Build Process Explained](https://github.com/green7ea/cpp-compilation)
* [The comprehensive catalog of C++ books](https://github.com/yuchdev/CppBooks)

### Reddit Threads

* [Open source projects with examples of good modern C++](https://www.reddit.com/r/cpp/comments/489f9l/open\_source\_projects\_with\_examples\_of\_good\_modern/)

### Videos

* [C++ Dynamic Linking vs Static Linking](https://www.youtube.com/watch?v=Jzh4ZULXsvo) - Jamie King

### Websites

* [Awesome Modern C++](https://awesomecpp.com/)
* [C++ By Example](https://cppbyexample.com/)
* [Modern C++ Tutorial](https://changkun.de/modern-cpp/en-us/00-preface/)

### YouTube playlists

* [Advanced C++](https://www.youtube.com/watch?v=7arYbAhu0aw\&list=PLE28375D4AC946CC3) - Bo Qian
* [C++](https://www.youtube.com/playlist?list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb) - The Cherno
* [C++ STD Gems](https://www.youtube.com/watch?v=BxO6G4RgmAM\&list=PLqCJpWy5FohdH8bbqclZrV8CG14UEAZ2c) - ChiliTomatoNoodle
* [C++ Unit Test](https://www.youtube.com/watch?v=TS2CTf11k1U\&list=PL5jc9xFGsL8GyES7nh-1yqljjdTvIFSsh) - Bo Qian
* [Modern C++](https://www.youtube.com/watch?v=U6mgsPqV32A\&list=PL5jc9xFGsL8FWtnZBeTqZBbniyw0uHyaH) - Bo Qian
