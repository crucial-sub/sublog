# Jump Game

You are given an integer array `nums`. You are initially positioned at the array's **first index**, and each element in the array represents your maximum jump length at that position.

Return `true` *if you can reach the last index, or* `false` *otherwise*.

**Example 1:**

```
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.

```

**Example 2:**

```
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.

```

**Constraints:**

- `1 <= nums.length <= 104`
- `0 <= nums[i] <= 105`

```jsx
let canJump = function (nums) {
  const leng = nums.length;
  if (leng === 1) return true;
  let idx = 0;
  let jumpMax = idx + nums[0];
  let jumpPoint;
  while (idx <= jumpMax) {
    jumpPoint = idx + nums[idx];
    if (jumpPoint >= leng - 1) return true;
    jumpMax = Math.max(jumpMax, jumpPoint);
    idx++;
  }
  return false;
};

// console.log(canJump([3, 2, 1, 0, 4]));
// console.log(canJump([2, 3, 1, 1, 4]));
console.log(canJump([0, 2, 3]));
// console.log(canJump([1, 1, 2, 2, 0, 1, 1]));
```
