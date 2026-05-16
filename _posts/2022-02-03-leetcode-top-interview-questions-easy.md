---
title: "Solving top interview questions - easy"
categories:
  - coding
toc: true
---

https://leetcode.com/explore/interview/card/top-interview-questions-easy/

# Array

## Remove Duplicates from sorted Array

Key points

- Array sorted in non-decreasing order
- Remove duplicates in-place such that each unique elements appears only once
- Modify inplace with O(1) memory

### Attempts

Loop through array from start to end to remove duplicate
Runtime O(n)

Solution 1

```
i = 0
while i < len(nums) - 1:
    if(nums[i] == nums[i + 1]):
        nums.pop(i)
        print(nums)
        i -=1
    i += 1
return len(nums)
```

Solution is not efficient as each duplicates is popped off individually

Solution 2:

Instead of removing each element, elements are replaced in the first k index.

```
lp = 0
for i in range(len(nums)):
    if(nums[i] != nums[lp]):
        lp += 1
        nums[lp] = nums[i]
return lp
```

## Best Time to Buy and Sell Stock II

Key points

- price[i] is given stock on i<sup>th<sup> day
- At most hold 1 share

### Attempts

To find the maximum profit, goal is to identify local minimum and local maximum. Buy at local minimum and sell at local maximum

- Buy when the price on the next day is higher
- Sell when price on the next day is lower

```
profit = 0
for i in range(len(prices) - 1):
    if prices[i + 1] > prices[i]:
        profit += (prices[i + 1] - prices[i])
return profit
```

## Rotate Array

Key points

- Rotate array to the right by k steps
- inline replacement

Learning:
nums = nums[-k:] + nums[:k] - creates a reference to a new nums
nums[:] = nums[-k:] + nums[:k] - replaces value in nums

## Contains Duplicate

Key points:
if any value appears at least twice, return true

### Attempts

| Method                                         | Time complexity  | Space Complexity |
| ---------------------------------------------- | ---------------- | ---------------- |
| Loop through array with each number            | O(n<sup>2</sup>) | O(1)             |
| Sort array and find duplicates in sorted array | O(n logn)        | O(1)             |
| Hashmap                                        | O(n)             | O(n)             |

```
nums.sort()
for i in range(len(nums) - 1):
    if nums[i] == nums[i + 1]:
        return true
return false
```

## Single Number

- Every element appears twice in an array, except for 1

### Attempts

| Method                                | Time complexity  | Space Complexity |
| ------------------------------------- | ---------------- | ---------------- |
| Loop through array with each number   | O(n<sup>2</sup>) | O(1)             |
| Sort array and search in sorted array | O(n logn)        | O(n)             |

```
nums.sort()
for i in range(0,len(nums)-2,2):
    if(nums[i] != nums[i + 1])
        return nums[i]
```

## Intersection of Two Arrays II

- Return an array of intersection
- Result appear as many times as it shows in any order

### Attempts

| Method                                                    | Time complexity  | Space Complexity |
| --------------------------------------------------------- | ---------------- | ---------------- |
| Loop through both array and append to list                | O(n<sup>2</sup>) | O(1)             |
| Sort both array and comparing using pointer in both array | O(n logn)        | O(n)             |

```
nums1.sort()
nums2.sort()
intersect = []
p1 = 0
p2 = 0

while p1 < len(nums1) and p2 < len(nums2):
    if nums1[p1] == nums2[p2]:
        intersect.append(nums1[p1])
        p1 += 1
        p2 += 1
    elif nums1[p1] < nums2[p2]:
        p1 += 1
    elif nums1[p1] > nums2[p2]:
        p2 += 1
return intersect
```

## Two Sum

- Given an array of integers and target, return indices of 2 number such that it adds up
  Key points
- Need to remember/track indices

### Attempts

| Method                                                          | Time complexity  | Space Complexity |
| --------------------------------------------------------------- | ---------------- | ---------------- |
| Loop through array twice while comparing                        | O(n<sup>2</sup>) | O(1)             |
| Use hashmap to check for sum, key:value pair is integer:indices | O(n logn)        | O(1)             |

```
dic = {}
for i,num in enumerate(nums):
    dic[num] = i
    if(type(dic[target - num]) == int):
        return [dic[target - num],i]
```

## Valid Sudoku

- row,column, 3x3 grid contains 1-9 without repeatition
- Board can be valid but not solvable

### Attempts

- Loop through each row,column and 3x3 grid
-
