# Compilers

## Notes

* The compiler is a program that takes your human-readable source code, analyses it, and produces a computer-readable binary code
* The steps of processing involved in compilng are:
  * **Preprocessing**: reads the input file and substitutes specific macros, symbols and directives with their definitions;
  * **Lexical analysis (or tokenization)**: split the input up character by character, and group them together to form words, identifiers, symbols, and more - these groups are called _tokens_;
  * **Parsing**: take the tokens, attempt to see if they're in certain patterns (usually defined by a _grammar_), then associate those patterns with expressions like calling functions, recalling variables, or math operations; the output of the parser is the _abstract syntax tree_ (AST);
  * **Optimisation**: take the AST and try to evaluate constant expressions, remove unused variables or unreachable code, unroll loops if possible, etc.;
  * **Generating code**: take the AST and emit the equivalent in the assembly language in an _object file_ (or other high-level programming languages in the case of a transpiler)
  * **Linking**: take all the object files and make an executable, a shared library, or a static library
* The **front end** is responsible for taking in the source code and turning it into an intermediate representation.
* The **middle end** is responsible for performing various optimizations on the intermediate representation.
  * these optimisations are _independent_ of the target platform, so they are going to speed up the code regardless of what the back end does
  * some examples of optimisations are **constant folding**, **reachability analysis** and **dead code elimination**
* The back end is responsible for taking the optimised intermediate representation and generating the machine code for the specific CPU architecture or generating bytecode.

## Resources

### Articles

* [Let's Build a Compiler](https://compilers.iecc.com/crenshaw/) - Jack Crenshaw
* [Making a Brainf\*ck to C Compiler in Rust](https://medium.com/@thelukaswils/making-a-brainf-ck-to-c-compiler-in-rust-10f0c01a282d) - Luke I. Wilson
* [Understanding Compilers - For Humans](https://medium.com/@thelukaswils/understanding-compilers-for-humans-ba970e045877) ([v2](https://towardsdatascience.com/understanding-compilers-for-humans-version-2-157f0edb02dd)) - Luke I. Wilson
* Writing a C Compiler - Nora Sandler
  * [Part 1](https://norasandler.com/2017/11/29/Write-a-Compiler.html) - Integers
  * [Part 2](https://norasandler.com/2017/12/05/Write-a-Compiler-2.html) - Unary Operators
  * [Part 3](https://norasandler.com/2017/12/15/Write-a-Compiler-3.html) - Binary Operators
  * [Part 4](https://norasandler.com/2017/12/28/Write-a-Compiler-4.html) - Even More Binary Operators
  * [Part 5](https://norasandler.com/2018/01/08/Write-a-Compiler-5.html) - Local Variables
  * [Part 6](https://norasandler.com/2018/02/25/Write-a-Compiler-6.html) - Conditionals
  * [Part 7](https://norasandler.com/2018/03/14/Write-a-Compiler-7.html) - Compound Statements
  * [Part 8](https://norasandler.com/2018/04/10/Write-a-Compiler-8.html) - Loops
  * [Part 9](https://norasandler.com/2018/06/27/Write-a-Compiler-9.html) - Functions
  * [Part 10](https://norasandler.com/2019/02/18/Write-a-Compiler-10.html) - Global Variables

### Books

* [Crafting A Compiler](https://smile.amazon.co.uk/Crafting-Compiler-Charles-N-Fischer/dp/0136067050/) - Charles Fischer, Ron Cytron, Richard LeBlanc
* [Crafting Interpreters](https://craftinginterpreters.com) - Robert Nystrom
* [Engineering: A Compiler](https://smile.amazon.co.uk/Engineering-Compiler-Keith-Cooper/dp/012088478X/) - Keith D. Cooper, Linda Torczon
* [The Architecture of Open Source Applications](https://aosabook.org/en/index.html)
  * [Chapter 11: LLVM](https://aosabook.org/en/llvm.html) - Chris Lattner

### Courses

* [Compilers](https://iucompilercourse.github.io/IU-P423-P523-E313-E513-Fall-2020/) - Indiana University, Fall 2020

### GitHub repositories

* [How to write a JIT compiler](https://github.com/spencertipping/jit-tutorial) - Spencer Tipping
* [simple.vm](https://github.com/skx/simple.vm) - Simple virtual machine which interprets bytecode

### Websites

* [Compiler Explorer](https://godbolt.org)
