# Dynamic Programming

## Notes

* It's an extension of the Divide and Conquer paradigm
* It works by recursively breaking down a problem into two or more sub-problems, until these become simple enough to be solved directly. The combination of the solutions for the sub-problems is a solution for the original problem.
* There are two key attributes that divide and conquer must have in order for dynamic programming to be applicable:
  * [Optimal substructure](https://en.wikipedia.org/wiki/Optimal_substructure) - optimal solution can be constructed from optimal solutions of its sub-problems
  * [Overlapping sub-problems](https://en.wikipedia.org/wiki/Overlapping_subproblems) - problem can be broken down into sub-problems which are reused several times, or a recursive algorithm for the problem solves the same sub-problem over and over rather than always generating new sub-problems
* There are two main techniques of caching and reusing previously computed results:
  *   [Memoization](https://www.wikiwand.com/en/Memoization) (top-down cache filling): usually done recursively

      ```
      memFib(n) {
          if (mem[n] is undefined)
              if (n < 2) result = n
              else result = memFib(n-2) + memFib(n-1)
              mem[n] = result
          return mem[n]
      }
      ```
  *   Tabulation (bottom-up cache filling): usually done iteratively

      ```
      tabFib(n) {
          mem[0] = 0
          mem[1] = 1
          for i = 2...n
              mem[i] = mem[i-2] + mem[i-1]
          return mem[n]
      }
      ```

![](<../../.gitbook/assets/immagine (4).png>)

### How do I recognise a Dynamic Programming problem?

1. Recognize a dynamic programming problem. This is often the most difficult step and it has to do with recognizing that there exists a recursive relation. Can your problem result be expressed as a function of results of the smaller problems that look the same?
2. Determine the number of changing parameters. Express your problem in terms of the function parameters and see how many of those parameters are changing. Typically you will have one or two, but technically this could be any number. Typical examples for one parameter problems are fibonacci or a coin change problem, for two parameter is edit distance.
3. Clearly express the recursive relation. Once you figure out that the recursive relation exists and you specify the problems in terms of parameters, this should come as a natural step. How do problems relate to each other. That is, assuming that you have computed the subproblems, how would you compute the main problem.
4. What are your base cases? When you break down sub-problems into smaller sub-problems and you break down those even further, what is the point where you cannot break it anymore? Do all these subproblems end up depending on the same small subproblem or several of them?
5. Decide if you want to implement it iteratively or recursively and be comfortable with both. Know the trade-offs between one or the other, such as recursive stack overflow, sparsity of computation, think if this is a repeated work or something that you always do online, etc.
6. Add memoization. If you’re implementing it recursively, add memoization. If you’re doing it iteratively, this will come for free. Adding memoization should be super mechanical. Just see if you have memoized the state at the beginning of your function and add it to memoization before each return statement. Memoization value should almost always be the result of your function.
7. Time complexity = (# of possible states \* work done per each state). This should also be relatively mechanical. Count the number of states that you can have. This will be linear if you have a single parameter, quadratic if you have two and so on. Think about the work done per each state - that is the work that you have to do assuming that you’ve computed all of the subproblems.

### Patterns

1. Minimum (Maximum) Path to React a Target
   * Given a target find minimum (maximum) cost / path / sum to reach the target.
   * Approach: Choose minimum (maximum) path among all possible paths before the current state, then add value for the current state.
   * Generate optimal solutions for all values in the target and return the value for the target (Top-down, Bottom-up).
   * Problem List: [DP (Max - Min)](https://leetcode.com/problem-list/55ac4kuc/)
2. Distinct Ways
   * Given a target find a number of distinct ways to reach the target.
   * Approach: Sum all possible ways to reach the current state.
   * Generate sum for all values in the target and return the value for the target (Top-down, Bottom-up).
   * Problem List: [DP (distinct ways)](https://leetcode.com/problem-list/55ajm50i/)
3. Merging Intervals
   * Given a set of numbers find an optimal solution for a problem considering the current number and the best you can get from the left and right sides.
   * Approach: Find all optimal solutions for every interval and return the best possible answer.
   * Get the best from the left and right sides and add a solution for the current position.
   * Problem List: [DP (merging intervals)](https://leetcode.com/problem-list/55aj8s16/)
4. DP on Strings
   * General problem statement for this pattern can vary but most of the time you are given two strings where lengths of those strings are not big
   * Approach: Most of the problems on this pattern requires a solution that can be accepted in O(n^2) complexity.
   * If you are given one string s the approach may little vary
   * Problem List: [DP on strings](https://leetcode.com/problem-list/55afh7m7/)
5. Decision Making
   * The general problem statement for this pattern is forgiven situation decide whether to use or not to use the current state. So, the problem requires you to make a decision at a current state.
   * Approach: If you decide to choose the current value use the previous result where the value was ignored; vice-versa, if you decide to ignore the current value use previous result where value was used.
   * Problem List: [DP (decision making)](https://leetcode.com/problem-list/55af7bu7/)

### Problems solved using Dynamic Programming

* [Balanced Partition](https://www.wikiwand.com/en/Partition_problem) - given a set of n integers between 0 and K, partition these integers into two sets so that `|sum(S1) - sum(s2)|` is minimal
* [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/description/) - given a staircase with n steps to the top, and you can climb x or y steps, in how many disinct ways can you reach the top of the staircase?
* [Coin Change](https://www.wikiwand.com/en/Change-making_problem) - given an infinite supply of coins with different values and a target price, in how many distinct ways can we make the change?
* [Fibonacci Sequence](https://www.wikiwand.com/en/Fibonacci_number) - what is the n-th number in the Fibonacci sequence?
* [House Robber](https://leetcode.com/problems/house-robber/)
* [Knapsack](https://www.wikiwand.com/en/Knapsack_problem) - given a set of items, each with a weight and a value, what is the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible?
* [Longest Common Subsequence](https://www.wikiwand.com/en/Longest_common_subsequence_problem) - given two strings, what is the longest subsequence (=non necessarily contiguous) for which letters from the two strings appear in the same order?
* [Maximum Subarray Sum](https://www.wikiwand.com/en/Maximum_subarray_problem) - given an array of integers, which contiguous subarray has the largest sum?
* Minimum Edit Distance (or [Levenshtein Distance](https://www.wikiwand.com/en/Levenshtein_distance)) - what is the minimum number of single-character edits (insertions, deletions, or subsitutions) required to change one word into another?

## Resources

### Articles

* [DP IS EASY! 5 Steps to Think Through DP Questions](https://leetcode.com/problems/target-sum/discuss/455024/DP-IS-EASY!-5-Steps-to-Think-Through-DP-Questions.) - LeetCode
* [Dynamic Programming is not Black Magic](https://qsantos.fr/2024/01/04/dynamic-programming-is-not-black-magic/) - Quentin Santos
* [Dynamic Programming vs Divide-and-Conquer](https://trekhleb.dev/blog/2018/dynamic-programming-vs-divide-and-conquer/) - Oleksii Trekhleb
* [Dynamic programming vs memoization vs tabulation](https://programming.guide/dynamic-programming-vs-memoization-vs-tabulation.html) - programming.guide
* [Dynamic Programming – 7 Steps to Solve any DP Interview Problem](https://dev.to/nikolaotasevic/dynamic-programming--7-steps-to-solve-any-dp-interview-problem-3870) - Nikola Otasevic
* [Introduction to Dynamic Programming](http://20bits.com/article/introduction-to-dynamic-programming) - Jesse Farmer
* [What are the top 10 most popular dynamic programming problems among interviewers?](https://www.quora.com/What-are-the-top-10-most-popular-dynamic-programming-problems-among-interviewers) - Quora

### Books

* [Algorithms, Chapter 6: Dynamic Programming](https://people.eecs.berkeley.edu/~vazirani/algorithms/chap6.pdf)
* Introduction To Algorithms, Chapter 15: Dynamic Programming ([lecture](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-046j-introduction-to-algorithms-sma-5503-fall-2005/video-lectures/lecture-15-dynamic-programming-longest-common-subsequence/), [notes](https://catonmat.net/mit-introduction-to-algorithms-part-ten))

### Courses

* [Dynamic Programming](https://www.youtube.com/watch?v=OQ5jsbhAv_M\&list=PLcDimPvbmfT8qAxD6JH_kmXiQwTNcoK78) - MIT
* [Dynamic Programming - I](https://www.udemy.com/course/dynamic-programming-i/?ranMID=39197\&ranEAID=JVFxdTr9V80\&ranSiteID=JVFxdTr9V80-C3d0CuJRL.3Gl.67I5NPXw\&LSNPUBID=JVFxdTr9V80\&utm_source=aff-campaign\&utm_medium=udemyads) - Udemy
* [Grokking Dynamic Programming Patterns for Coding Interviews](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews) - Educative
* [Intro To Dynamic Programming - Coding Interview Preparation](https://www.udemy.com/course/dynamic-programming/) - Udemy
* [Master the art of Dynamic Programming](https://www.udemy.com/course/master-the-art-of-dynamic-programming/) - Udemy

### LeetCode Problems

* [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
* [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
* [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
* [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
* [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
* [Burst Balloons](https://leetcode.com/problems/burst-balloons/)
* [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
* [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)
* [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
* [Domino and Tromino Tiling](https://leetcode.com/problems/domino-and-tromino-tiling/)
* [Edit Distance](https://leetcode.com/problems/edit-distance/)
* [Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)
* [House Robber](https://leetcode.com/problems/house-robber/)
* [Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/)
* [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
* [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
* [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
* [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings)
* [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
* [Minimum Cost Tree From Leaf Values](https://leetcode.com/problems/minimum-cost-tree-from-leaf-values/)
* [Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)
* [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)
* [Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/)
* [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/description)
* [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
* [Remove Boxes](https://leetcode.com/problems/remove-boxes/)
* [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/)
* [Target Sum](https://leetcode.com/problems/target-sum/)
* [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)
* [Unique Paths](https://leetcode.com/problems/unique-paths/)
* [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

### Websites

* [A Tutorial on Dynamic Programming](https://mat.gsia.cmu.edu/classes/dynamic/dynamic.html) - Michael A. Trick
* [Dynamic Programming Practice Problems](https://people.cs.clemson.edu/~bcdean/dp_practice/) - Brian Dean

### YouTube playlists

* [Dynamic Programming](https://www.youtube.com/playlist?list=PLrmLmBdmIlpsHaNTPP_jHHDx_os9ItYXr) - Tushar Roy
* [Dynamic Programming](https://www.youtube.com/playlist?list=PLDV1Zeh2NRsAsbafOroUBnNV8fhZa7P4u) - William Fiset

### YouTube videos

* [Dynamic Programming](https://www.youtube.com/watch?v=6h6Fi6AQiRM) - NPTEL
* [Dynamic Programming and Uniform Cost Search](https://www.youtube.com/watch?v=aIsgJJYrlXk) - Stanford CS221
* [Dynamic Programming for people who hate it](https://www.youtube.com/watch?v=OW2-fcoFNps) - Coding Patterns
