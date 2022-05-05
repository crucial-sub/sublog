# Jump Game2

Given an array of non-negative integers `nums`, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

You can assume that you can always reach the last index.

**Example 1:**

```
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.

```

**Example 2:**

```
Input: nums = [2,3,0,1,4]
Output: 2

```

**Constraints:**

- `1 <= nums.length <= 104`
- `0 <= nums[i] <= 1000`

```jsx
let jump = function (nums) {
  const leng = nums.length;
  let jumpCount = 0;
  let idx = 0;
  let nextPoint = idx + nums[idx];
  let jumpPoint;
  if (leng === 1) return 0;
  if (leng === 2) return 1;

  while (idx <= leng) {
    const checkRange = nums[idx];
    let rangeMax = idx + nums[idx];

    for (let i = 0; i <= checkRange; i++) {
      jumpPoint = idx + nums[idx];
      if (jumpPoint >= leng - 1) {
        if (i === 0) return jumpCount + 1;
        else return jumpCount + 2;
      }
      if (jumpPoint > rangeMax) {
        rangeMax = jumpPoint;
        nextPoint = idx;
      }
      idx++;
    }
    idx = nextPoint;
    nextPoint = idx + nums[idx];
    jumpCount++;
  }
  return jumpCount;
};

console.log(jump([2, 3, 1, 1, 4])); // 2
// console.log(jump([1, 1, 2, 2, 0, 1, 1])); // 5
// console.log(jump([1, 2, 3, 0, 1, 4])); // 3
// console.log(jump([3, 2, 1, 2, 1, 5])); // 2
// console.log(jump([5, 9, 3, 2, 1, 0, 2, 3, 3, 1, 0, 0])); // 3
// console.log(jump([7, 0, 9, 6, 9, 6, 1, 7, 9, 0, 1, 2, 9, 0, 3])); // 2
// console.log(jump([1, 3, 2])); // 2
```
