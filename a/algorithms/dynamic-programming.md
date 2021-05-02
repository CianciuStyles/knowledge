# Dynamic Programming

## Notes

* It's an extension of the Divide and Conquer paradigm
* It works by recursively breaking down a problem into two or more sub-problems, until these become simple enough to be solved directly. The combination of the solutions for the sub-problems is a solution for the original problem.
* There are two key attributes that divide and conquer must have in order for dynamic programming to be applicable:
  * [Optimal substructure](https://en.wikipedia.org/wiki/Optimal_substructure) - optimal solution can be constructed from optimal solutions of its sub-problems
  * [Overlapping sub-problems](https://en.wikipedia.org/wiki/Overlapping_subproblems) - problem can be broken down into sub-problems which are reused several times, or a recursive algorithm for the problem solves the same sub-problem over and over rather than always generating new sub-problems
* There are two main techniques of caching and reusing previously computed results:
  * [Memoization](https://www.wikiwand.com/en/Memoization) \(top-down cache filling\): usually done recursively

    ```text
    memFib(n) {
        if (mem[n] is undefined)
            if (n < 2) result = n
            else result = memFib(n-2) + memFib(n-1)
            mem[n] = result
        return mem[n]
    }
    ```

  * Tabulation \(bottom-up cache filling\): usually done iteratively

    ```text
    tabFib(n) {
        mem[0] = 0
        mem[1] = 1
        for i = 2...n
            mem[i] = mem[i-2] + mem[i-1]
        return mem[n]
    }
    ```

![](../../.gitbook/assets/immagine%20%281%29.png)

### Problems solved using Dynamic Programming

* [Fibonacci Sequence](https://www.wikiwand.com/en/Fibonacci_number) - what is the n'th number in the Fibonacci sequence?
* [Knapsack](https://www.wikiwand.com/en/Knapsack_problem) - given a set of items, each with a weight and a value, what is the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible?
* Minimum Edit Distance \(or [Levenshtein Distance](https://www.wikiwand.com/en/Levenshtein_distance)\) - what is the minimum number of single-character edits \(insertions, deletions, or subsitutions\) required to change one word into another?

## Resources

### Articles

* [Dynamic Programming vs Divide-and-Conquer](https://trekhleb.dev/blog/2018/dynamic-programming-vs-divide-and-conquer/) - Oleksii Trekhleb
* [Dynamic programming vs memoization vs tabulation](https://programming.guide/dynamic-programming-vs-memoization-vs-tabulation.html) - programming.guide

