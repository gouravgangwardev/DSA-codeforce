1. Median of Two Sorted Arrays

  issue: first i think just merge both array and sort it, it work but its O((m+n) log(m+n)) time complexity and question wants O(log(min(m,n))) only. also i was confuse in median formula when total lenght is even or odd, i mess up alot of time in this.

how i solve it:

do binary search on smaller array insted of merging both.
partition both array so left half have same no of element as right half.
handel edge case when partition index is 0 or equal to lenght of array (used INT_MIN / INT_MAX for this boundry values).
if lenght even → average of two middle element. if odd → max of left side is answer.

complexity: O(log(min(m, n))) time, O(1) space.

2. Container With Most Water

issue: first attempt i did brute force with two nested loop (O(n²)) — getting TLE on big inputs, obviously.

how i solve it:

used two pointer, one at starting, one at last.
area = min(height[l], height[r]) * (r - l).
always move the pointer which have smaller height inward, becoz moving the taller one can never increase area, only decrease.

complexity: O(n) time, O(1) space.

3. First Missing Positive

  issue: i was keep using hash set / sorting first but the ques asking for O(n) time and O(1) extra space so that not gonna work here.

how i solve it:

use index placement (cyclic sort) trick: for every value v in range [1, n], place it on index v-1 by swaping.
after placeing all, scan the array — first index i where nums[i] != i+1 that is the ans i+1.
if everything already in place then ans is n+1.

complexity: O(n) time, O(1) extra space.

4. Combination Sum II

  issue: getting duplicate combination in output becoz input array have duplicate number in it.

how i solve it:

sort the array first, very important step.
in backtracking loop, skip the number if its same as previous one at same recursion depth (if (i > start && candidates[i] == candidates[i-1]) continue;).
since every number can be use only once, i moved to i+1 in recursive call (not i).

complexity: O(2ⁿ) worst case for generating subset, but prune alot bcz of sorting + early break when candidates[i] > target.

5. Combination Sum

  issue: got confuse this one with #4 — i used i+1 in recursive call by mistake and got wrong ans, becoz here number can repeat unlike previous one.

how i solve it:

same backtracking templete like Combination Sum II, but recursive call pass i (not i+1) since same element allowed to reuse.
still sorted the array so i can break early once candidates[i] > remaining target.

complexity: O(2ᵗ) roughly, where t = target/min(candidates), prune by sorting.

6. Sudoku Solver

  issue: validity checking (row/col/3x3 box) was slow becoz i was recompute it from scratch for every single cell on every backtrack try, very inefficient.

how i solve it:

write one single isValid(board, row, col, digit) helper checking row, column and 3x3 box togather insted of 3 separate pass.
backtracking: find empty cell → try digit 1-9 → place digit if valid → recurse → undo (backtrack) if not leading to solution.
return true up the recursion as soon as board full filled, so it dont keep searching after already found solution.

complexity: exponential worst case, but constrain heavily in practical by sudoku's own rule.

7. Valid Sudoku

  issue: i was going to just reuse the whole solver from #6, which is way more work then needed — this ques only need validation, not actually solving it.

how i solve it:

used 3 array of hash set (or boolean array) — one for row, one for column, one for each 3x3 box.
single pass thru the 9x9 board: for every filled cell, compute boxIndex = (row/3)*3 + col/3 and check if digit already exist in that row/col/box set. if yes → return false. otherwise insert it in set.

complexity: O(1) — fix 81 cell — but written as O(n²) if generalise for n x n board.

8. Find First and Last Position of Element in Sorted Array

  issue: i did normal binary search to find one occurance, then linear scan left/right to find boundry — it technically work but not O(log n) like ques want.

how i solve it:

write two binary search: one biased to find leftmost index (keep going left even after found target), one biased to find rightmost index (keep going right after found target).
if value not found at all, return [-1, -1].

complexity: O(log n) time, O(1) space.

9. Search in Rotated Sorted Array

  issue: normal binary search dont work directly becoz array not fully sorted anymore, i kept getting confuse about which half to search in.

how i solve it:

at every step, check which half (left..mid or mid..right) is actualy sorted.
if sorted half contain the target (compare with nums[left]/nums[right]), search there, otherwise search other half.

complexity: O(log n) time, O(1) space.

10. Next Permutation

  issue: first i tryed generating all permutation and find current one position — way to slow (O(n!)) and pointless for this ques.

how i solve it:

scan from right to find first index i where nums[i] < nums[i+1] (this is the "pivot").
if found, scan from right again to find first index j > i where nums[j] > nums[i], then swap nums[i] and nums[j].
reverse the suffix after index i to get smallest possible arrangement for that part.
if no pivot found, array is in descending order (means its last permutation) → just reverse whole array.

complexity: O(n) time, O(1) space.

11. 3Sum Closest

  issue: first wrote brute force triple nested loop (O(n³)) which was to slow, then i forgate to actualy track the closest sum properly (kept overwriting instead of comparing with abs diffrence).

how i solve it:

sort the array.
fix one element, then use two pointer on rest to find closest sum to target in O(n) per fix element.
keep a closestSum variable and update only when abs(currentSum - target) < abs(closestSum - target).
move left pointer right if sum to small, move right pointer left if sum to big.

complexity: O(n²) time, O(1) extra space (not counting sort).

12. 3Sum

  issue: getting alot of duplicate triplet in the output, very annoying.

how i solve it:

sort first.
fix the first number, skip it if its same as previous iteration number (if (i > 0 && nums[i] == nums[i-1]) continue;).
use two pointer for remaining two number, and after found valid triplet, skip duplicate value on both left and right pointer before continue.

complexity: O(n²) time, O(1) extra space (not counting sort/output).

13. Largest Rectangle in Histogram

  issue: brute force checking every pair of bar (O(n²)) was timing out on big histogram input.

how i solve it:

used a monotonic increasing stack of index.
when current bar shorter then bar at top of stack, pop the stack and calculate rectangle area using the popped height, width decide by current index and new stack top.
push a sentinel (height 0) at the end so any remaining bar in stack also get process properly.

complexity: O(n) time, O(n) space.

14. Remove Duplicates from Sorted Array II

  issue: my first version was either allowing unlimited duplicate or removing to much (only allowing 1) — didnt correctly implement the "atmost twice" rule properly.

how i solve it:

used a slow pointer (k) tracking write position.
for every number, compare it with nums[k-2] — if diffrent, its safe to keep (this naturaly allow upto 2 duplicate in row).
overwrite nums[k] with current value and increment k only when check pass.

complexity: O(n) time, O(1) extra space.

15. Maximal Rectangle

  issue: tryed solving it from scratch with nested loop checking every possible rectangle — extremly slow (O(n²·m²)) and very messy code.

how i solve it:

realise this actualy reduce to running "Largest Rectangle in Histogram" (#13) on every row.
build a heights[] array that update as i go down each row: if cell is '1', heights[col] += 1, if cell is '0', reset heights[col] = 0.
run histogram algo on heights[] for every row and track max area seen so far.

complexity: O(rows × cols) time, O(cols) space.

16. Sort Colors

  issue: first solution just called generic sort function — works but ques want single pass O(n) solution (dutch national flag algo), not O(n log n).

how i solve it:

used 3 pointer: low, mid, high.
if nums[mid] == 0 → swap with low, increment both low and mid.
if nums[mid] == 1 → just increment mid.
if nums[mid] == 2 → swap with high, decrement high (dont increment mid here, becoz swaped-in value also need to be check again).
loop until mid > high.

complexity: O(n) time, O(1) space, single pass only.
