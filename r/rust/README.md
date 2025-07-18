---
description: https://www.rust-lang.org/
---

# Rust

## Notes

### Ownership

* There are three rules to ownership in Rust:
  1. Each value has an owner
  2. There is only one owner of a value - other variables may _borrow_ the value
  3. When the owner of a value goes out of scope, the value gets dropped immediately
* In Rust, we use the term copy when only stack variables are copied; if there is heap data as well we use the term clone
* When a value is dropped:
  1. the destructor, if available, is immediately run
  2. the heap portion is immediately freed
  3. the stack portion is immediately popped

### References & Borrowing

* A reference to a type is specified using the `&` (ampersand) notation
* Rust introduces a concept called _lifetimes_, which can be summed up as the rule that references must always be valid, which means the compiler will not allow you to create a reference that outlives the value it is referencing, and you can never point to null
* References are immutable by default, but can be changed to mutale using the `&mut` notation
* At any given time, you can have either _exactly one mutable_ reference or _any numer of immutable_ references to a value

## Resources

### Articles

* [24 days from node.js to Rust](https://candle.dev/blog/javascript-to-rust/javascript-to-rust-day-1-rustup/) - Jarrod Overson
* [A half-hour to learn Rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust) - Amos Wenger
* [From C# to Rust: A 42-Day Developer Challenge](https://woodruff.dev/from-c-to-rust-a-42-day-developer-challenge/) - Chris Woody Woodruff
* [Getting Started with Rust by Building a Tiny Markdown Compiler](https://jesselawson.org/rust/getting-started-with-rust-by-building-a-tiny-markdown-compiler/) - Jesse Lawson
* [Optimising my Rust solutions for Advent of Code](https://nindalf.com/posts/optimising-rust/) - Krishna Sundarram
* [Rust concepts I wish I learned earlier](https://rauljordan.com/rust-concepts-i-wish-i-learned-earlier/) - Raul Jordan ([HN thread](https://news.ycombinator.com/item?id=34427604), [Reddit thread](https://www.reddit.com/r/rust/comments/10eu2aw/rust_concepts_i_wish_i_learned_earlier/))
* [Rust for professionals](https://overexact.com/rust-for-professionals/) - overexact.com
* [State of the Crates 2025](https://ohadravid.github.io/posts/2024-12-state-of-the-crates/) - Ohad Ravid ([Reddit thread](https://www.reddit.com/r/rust/comments/1hafdai/state_of_the_crates_2025/))
* [Writing a file system from scratch in Rust](https://blog.carlosgaldino.com/writing-a-file-system-from-scratch-in-rust.html) - Carlos Galdino
* [Writing a JVM in Rust ](https://andreabergia.com/series/writing-a-jvm-in-rust/)- Andrea Bergia
  * [I have written a JVM in Rust](https://andreabergia.com/blog/2023/07/i-have-written-a-jvm-in-rust/)
  * [A JVM in Rust part 2 - The class files format](https://andreabergia.com/blog/2023/07/a-jvm-in-rust-part-2-the-class-files-format/)
  * [A JVM in Rust part 3 - Parsing class files](https://andreabergia.com/blog/2023/07/a-jvm-in-rust-part-3-parsing-class-files/)
  * [A JVM in Rust part 4 - The Java bytecode](https://andreabergia.com/blog/2023/08/a-jvm-in-rust-part-4-the-java-bytecode/)
  * [A JVM in Rust part 5 - Executing instructions](https://andreabergia.com/blog/2023/08/a-jvm-in-rust-part-5-executing-instructions/)
  * [A JVM in Rust part 6 - Methods and exceptions](https://andreabergia.com/blog/2023/09/a-jvm-in-rust-part-6-methods-and-exceptions/)
  * [A JVM in Rust part 7 - Objects and GC](https://andreabergia.com/blog/2023/10/a-jvm-in-rust-part-7-objects-and-gc/)
  * [A JVM in Rust part 8 - Retrospective](https://andreabergia.com/blog/2023/11/a-jvm-in-rust-part-8-retrospective/)
* [Writing an OS in Rust](https://os.phil-opp.com/) - Philipp Oppermann

### Books

* [100 Exercises To Learn Rust](https://rust-exercises.com/) - Luca Palmieri
* [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)
* [C++ to Rust Phrasebook](https://cel.cs.brown.edu/crp/title-page.html)
* [Easy Rust](https://fongyoong.github.io/easy_rust/)
* [Effective Rust](https://www.lurklurk.org/effective-rust/) - David Drysdale
* [Hands-on Rust](https://pragprog.com/titles/hwrust/hands-on-rust/) - Herbert Wolverson
* [Learn Rust With Entirely Too Many Linked Lists](https://rust-unofficial.github.io/too-many-lists/index.html)
* [Programming Rust](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/) - Jim Blandy, Jason Orendorff, and Leonora Tindall
* [Rust and WebAssembly](https://rustwasm.github.io/docs/book/)
* [Rust for Rustaceans](https://rust-for-rustaceans.com/) - Jon Gjengset
* [Rust in Action](https://www.rustinaction.com/) - Tim McNamara
* [The Complete Rust Security Handbook: From Safe Code to Unbreakable Systems](https://yevh.github.io/rust-security-handbook/)
* [The Embedded Rust Book](https://docs.rust-embedded.org/book/index.html)
* [The Rust Performance Book](https://nnethercote.github.io/perf-book/introduction.html)
* [The Rust Programming Language](https://doc.rust-lang.org/book/) - Steve Klabnik and Carol Nichols
  * [Rust Book Experiment](https://rust-book.cs.brown.edu/)
* [The Secrets of Rust: Tools](https://bitfieldconsulting.com/books/rust-tools) - John Arundel
* [Zero To Production In Rust](https://www.zero2prod.com/index.html?country=the%20UK\&discount_code=VAT20) - Luca Palmieri

### Crates

* [cargo-chef](https://crates.io/crates/cargo-chef) - A cargo sub-command to build project dependencies for optimal Docker layer caching
* [criterion](https://crates.io/crates/criterion) - Statistics-driven micro-benchmarking library
* [dioxus](https://crates.io/crates/dioxus) - Build fullstack web, desktop, and mobile apps with a single codebase
* [eyre](https://crates.io/crates/eyre) - Flexible concrete Error Reporting type built on std::error::Error with customizable Reports
* [hyperfine](https://crates.io/crates/hyperfine) - A command-line benchmarking tool
* [nom](https://crates.io/crates/nom) - A byte-oriented, zero-copy, parser combinators library
* [pest](https://crates.io/crates/pest) - The elegant parser
* [ratatui](https://crates.io/crates/ratatui/) - A library that's all about cooking up terminal user interfaces
* [rayon](https://crates.io/crates/rayon) - Simple work-stealing parallelism for Rust
* [regex](https://crates.io/crates/regex) - An implementation of regular expressions for Rust

### Courses

* [Comprehensive Rust](https://google.github.io/comprehensive-rust/) - Android team, Google
* [Learn Rust by Building Real Applications](https://www.udemy.com/course/rust-fundamentals/) - Lyubomir Gavadinov
* [Learning Rust by Working Through the Rustlings Exercises](https://egghead.io/courses/learning-rust-by-solving-the-rustlings-exercises-a722) - Chris Biscardi
* [The Rust Programming Language](https://frontendmasters.com/courses/rust/) - Richard Feldman
* [Write Yourself a CLI – A Fast File Finder in Rust](https://github.com/corrode/write-yourself-a-cli) - corrode.dev
* [Write Yourself A Shell In Rust](https://github.com/corrode/write-yourself-a-shell) - corrode.dev
* [Write Yourself a Web App in Rust](https://github.com/corrode/write-yourself-a-web-app) - corrode.dev

### GitHub repositories

* [Awesome Rust](https://github.com/rust-unofficial/awesome-rust)
* [Awesome Rust Books](https://github.com/sger/RustBooks)
* [Black Hat Rust](https://github.com/skerkour/black-hat-rust)
* [From JavaScript to Rust eBook](https://github.com/vinodotdev/node-to-rust)
* [Programming in Rust](https://github.com/Kobzol/rust-course-fei) - Rust course taught at FEI VŠB-TUO
* [py2rs - From Python into Rust](https://github.com/rochacbruno/py2rs)
* [Rust Learning](https://github.com/ctjhoa/rust-learning)
* [TheAlgorithms/Rust](https://github.com/TheAlgorithms/Rust)

### IDEs

* [Lapce](https://lapce.dev/)
* [RustRover ](https://www.jetbrains.com/rust/)- JetBrains
* [Visual Studio Code](https://code.visualstudio.com/) - Microsoft
  * [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer) ([GitHub](https://github.com/rust-lang/rust-analyzer))
  * [RustOwl](https://github.com/cordx56/rustowl)

### Libraries

* [CXX](https://cxx.rs/) - safe interop between Rust and C++

### Subreddits

* [r/learnrust](https://www.reddit.com/r/learnrust/)
* [r/rust](https://www.reddit.com/r/rust/)

### Websites

* [Are we game yet?](https://arewegameyet.rs/)
* [Are we GUI yet?](https://areweguiyet.com/)
* [Are we learning yet?](https://www.arewelearningyet.com/)
* [Are we web yet?](https://www.arewewebyet.org/)
* [Leptos](https://leptos.dev/) - Build interactive web applications in Rust
* [Rust Programming Language](https://www.rust-lang.org/)
  * [2024 State of Rust Survey Result](https://blog.rust-lang.org/2025/02/13/2024-State-Of-Rust-Survey-results.html)
  * [Rustlings](https://rustlings.rust-lang.org/)
* [Rustfinity](https://www.rustfinity.com/)

### YouTube channels

* [fasterthanlime](https://www.youtube.com/@fasterthanlime/videos)
* [Jon Gjengset](https://www.youtube.com/@jonhoo)

### YouTube playlists

* [From Python to Rust](https://www.youtube.com/playlist?list=PLEIv4NBmh-GsWGE9mY3sF9c5lgh5Z_jLr)
* Rust for Java Developers - Jeremy Chone
  * [Introduction](https://www.youtube.com/watch?v=iFdh4sPC5Tg)
  * [Java 17 side by side](https://www.youtube.com/watch?v=_xiPUXMZeyU)
  * [Understanding Ownership](https://www.youtube.com/watch?v=Vg1LGHuAPP8)
