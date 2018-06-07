---
layout: post
title: 【CSP】 Backtracking Search
categories: notes
---

## Backtracking Search

![img](http://7xq5e8.com1.z0.glb.clouddn.com/20180516/backtracking.png)

## Question

N people With ID:  0,1,2,3,4,5,6...., N is even，
We need to orchestrate the N individuals so that each person encounters different opponents in each match. The final solution is:

A **N/2 * (N - 1)** matrix

## Example 1

2 persons with ID: 0,1 , The solution is ：

[[0,1]]

the number of solution: x=1


> [0,1] and [1,0] is same solutiuon

## Example 2

4 persons with ID: 0,1,2,3, The solutions are:

```
 [[0,1], [2,3]]  # 1
 [[0,2], [1,3]]  # 2
 [[0,3], [1,2]]  # 3
 ```

the number of solutions: x=3

## Example 3

6 persons with ID: 0,1,2,3,4,5, The solutions are:

```
[[0, 1], [2, 3], [4, 5]] #1
[[0, 2], [1, 4], [3, 5]] #2
[[0, 3], [1, 5], [2, 4]] #3
[[0, 4], [1, 3], [2, 5]] #4
[[0, 5], [1, 2], [3, 4]] #5
 ```

the number of solutions: x=5

## How to do it automaticly?

Ideas：

First, list the different people that everyone can match and do not repeat

4 persons:

```
[[0,1], [0,2], [0,3]]
[[1,2], [1,3]]
[[2,3]]
```

6 persons:

```
[[0, 1], [0, 2], [0, 3], [0, 4], [0, 5]]
[[1, 2], [1, 3], [1, 4], [1, 5]]
[[2, 3], [2, 4], [2, 5]]
[[3, 4], [3, 5]]
[[4, 5]]
```

- init Depth search array: **Keys[i]**
- Initialize depth search location marker: **keyidx**
- Each element in Keys represents the searched position. If the solution is not satisfied, the keyidx will be traced back.
- keyidx points to the jth column in the persons array, and it would find answer in this column, if Keys[keyidx] = -2, means it has not been search, if Keys[keyidx] = -1, means there is no answer in this column, and keyidx would add 1 to find next colum
- once when we find out all column, but it still has no full solution, we should backtracking to last answer and use another answer

example:

```
found [[0,1],[2,3],[4,5]]

next: [[0,2], [1,3]], we found that we need [4,5],
but [4,5] was already used, so we should backtracking to :
[[0,2],[1,4]]
then we could found [3,5]

Then the solution is [[0,2],[1,4], [3,5]]
```

## The Program results example

```
###############################################
| Author: Shang |
| Time: 2018-5-30 |
| Github: https://github.com/Shinepans |
Desc:
 It is a stranger matching algorithm, It produce double person, and each record is different that every one met another one, and it would not repeat
###############################################
Team inited, It is [0, 1, 2, 3, 4, 5, 6, 7]
All case was found
[[0, 1], [0, 2], [0, 3], [0, 4], [0, 5], [0, 6], [0, 7]]
[[1, 2], [1, 3], [1, 4], [1, 5], [1, 6], [1, 7]]
[[2, 3], [2, 4], [2, 5], [2, 6], [2, 7]]
[[3, 4], [3, 5], [3, 6], [3, 7]]
[[4, 5], [4, 6], [4, 7]]
[[5, 6], [5, 7]]
[[6, 7]]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 1], [0, 2], [0, 3], [0, 4], [0, 5], [0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 1]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 2], [1, 3], [1, 4], [1, 5], [1, 6], [1, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 3], [2, 4], [2, 5], [2, 6], [2, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 1], [2, 3]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5], [3, 6], [3, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  4
    Keys[keyidx] =  -2
    Que = [[4, 5], [4, 6], [4, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 1], [2, 3], [4, 5]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  5
    Keys[keyidx] =  -2
    Que = [[5, 6], [5, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  6
    Keys[keyidx] =  -2
    Que = [[6, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 1], [2, 3], [4, 5], [6, 7]]
**Found All?**
     True
[-2, -2, -2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 2], [0, 3], [0, 4], [0, 5], [0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 2]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 2], [1, 3], [1, 4], [1, 5], [1, 6], [1, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 2], [1, 3]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 4], [2, 5], [2, 6], [2, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5], [3, 6], [3, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  4
    Keys[keyidx] =  -2
    Que = [[4, 6], [4, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 2], [1, 3], [4, 6]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  5
    Keys[keyidx] =  -2
    Que = [[5, 6], [5, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 2], [1, 3], [4, 6], [5, 7]]
**Found All?**
     True
[-2, -2, -2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 3], [0, 4], [0, 5], [0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 3]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 2], [1, 4], [1, 5], [1, 6], [1, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 3], [1, 2]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 4], [2, 5], [2, 6], [2, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5], [3, 6], [3, 7]]
    rangStart= 0
**RES** Not Found
**START** Find
    keyidx =  4
    Keys[keyidx] =  -2
    Que = [[4, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 3], [1, 2], [4, 7]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  5
    Keys[keyidx] =  -2
    Que = [[5, 6]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 3], [1, 2], [4, 7], [5, 6]]
**Found All?**
     True
[-2, -2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 4], [0, 5], [0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 4]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 4], [1, 5], [1, 6], [1, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 4], [1, 5]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 4], [2, 5], [2, 6], [2, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 4], [1, 5], [2, 6]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5], [3, 6], [3, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 4], [1, 5], [2, 6], [3, 7]]
**Found All?**
     True
[-2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 5], [0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 5]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 4], [1, 6], [1, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 5], [1, 4]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 4], [2, 5], [2, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 5], [1, 4], [2, 7]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5], [3, 6]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 5], [1, 4], [2, 7], [3, 6]]
**Found All?**
     True
[-2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 6], [0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 6]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 6], [1, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 6], [1, 7]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 4], [2, 5]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 6], [1, 7], [2, 4]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4], [3, 5]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 6], [1, 7], [2, 4], [3, 5]]
**Found All?**
     True
[-2, -2, -2, -2]
**START** Find
    keyidx =  0
    Keys[keyidx] =  0
    Que = [[0, 7]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 7]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  1
    Keys[keyidx] =  -2
    Que = [[1, 6]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 7], [1, 6]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  2
    Keys[keyidx] =  -2
    Que = [[2, 5]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 7], [1, 6], [2, 5]]
**Found All?**
     False
**↓** Deep Search
**START** Find
    keyidx =  3
    Keys[keyidx] =  -2
    Que = [[3, 4]]
    rangStart= 0
**RES** Found One
    TempRes: [[0, 7], [1, 6], [2, 5], [3, 4]]
**Found All?**
     True
All is found, case :  7
[[0, 1], [2, 3], [4, 5], [6, 7]]
[[0, 2], [1, 3], [4, 6], [5, 7]]
[[0, 3], [1, 2], [4, 7], [5, 6]]
[[0, 4], [1, 5], [2, 6], [3, 7]]
[[0, 5], [1, 4], [2, 7], [3, 6]]
[[0, 6], [1, 7], [2, 4], [3, 5]]
[[0, 7], [1, 6], [2, 5], [3, 4]]
```
