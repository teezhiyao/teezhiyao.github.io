---
title: "Leet code notes and solutions"
categories:
  - coding
tags:
  - coding
toc: true
---

# Question 704 - [Binary Search](https://leetcode.com/problems/binary-search/)

Solution 1: Loop through array from start to end

```
for i in range nums:
    if target == num[i]
        return i
    return -1
```

Runtime complexity: O(n)

Solution 2: Start searching from center of array

```
i = int(len(nums) / 2)
while i > 0 and i < len(nums):
    if target == nums[i]:
        return i
    elif target > nums[i] and i >= len(nums) / 2:
        i += 1
    elif target < nums[i] and i <= len(nums) / 2:
        i -= 1
    else:
        break
return -1
```

Given solution: Make use of a left, right pointer to determine the next pivot

target = 10
L = P + 1 or R = P - 1 is used to calculate the pointer that moved

| index | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   | Calculation              |
| ----- | --- | --- | --- | --- | --- | --- | --- | --- | ------------------------ |
| Array | -1  | 0   | 3   | 5   | 9   | 10  | 12  | 15  |                          |
|       | L   |     |     | P   |     |     |     | R   | P = 0 + (7 - 0) // 2 = 3 |
|       |     |     |     |     | L   | P   |     | R   | P = 4 + (7 - 4) // 2 = 5 |

target = 11
L = P - 1 or R = P + 1 is used to calculate the pointer that moved

| index | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   | Calculation              |
| ----- | --- | --- | --- | --- | --- | --- | --- | --- | ------------------------ |
| Array | -1  | 0   | 3   | 5   | 9   | 10  | 12  | 15  |                          |
|       | L   |     |     | P   |     |     |     | R   | P = 0 + (7 - 0) // 2 = 3 |
|       |     |     |     |     | L   | P   |     | R   | P = 4 + (7 - 4) // 2 = 5 |
|       |     |     |     |     |     |     | L,P | R   | P = 6 + (7 - 6) // 2 = 6 |
|       |     |     |     |     |     | R   | L,P |     | R < L , break loop       |

Code re-implementation

```
l = 0
r = len(nums) - 1
while l <= r:
    p = l + (r - l) // 2
    if target == nums[p]:
        return p
    elif target <= nums[p]:
        r = p - 1
    elif target >= nums[p]:
        l = p + 1
return -1
```

Learnings

- Use // for integer devision in python
- Think of solutions using pointer when direction is involved. Initial implementation involved moving in only 1 direction (And breaking the loop when target > num[i] or target < num[i] respectively)
