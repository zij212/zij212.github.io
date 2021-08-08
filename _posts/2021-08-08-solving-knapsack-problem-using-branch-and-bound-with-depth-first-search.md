---
layout: post
title: "Solving 0/1 Knapsack Problem Using Branch and Bound with Depth First Search"
date: 2021-08-08
---


## What is 0/1 knapsack problem?

Given a set of items (along with their value and weight), and a knapsack of limited capacity, solve for the set of items that maximize the total value under the capacity constraint. 0/1 here means that you can either take or not take an item; you cannot take a fraction of an item.

## What is Branch and Bound?

It is an approach that forms candidate solutions as a tree, and find the optimal solution by exploring the tree efficiently. During the exploration, it comes up with better and better estimations of the lower and upper bounds of the solution, and reduce the search space.

## What is Depth First Search?

It is an algorithm for traversing tree structure. It searches through a branch to its end and then traceback.  You can think of it as Last in First out (LIFO), and we will explain this concept later.

## Problem Formation

In the context of 0/1 knapsack problem, each level of the tree represents an item in the list. At the root node, we are making the decision of taking the first item of list or not. The left child of the root node represents the decision of taking the first item, whereas the right child represents the decision of not taking the first item.

![bnb_tree1.png]({{site.url}}/img/bnb_tree1.png)

For example the green node in the graph means: not taking the 1st item, taking the 2nd item, taking the 3rd item, not taking the 4th item

At any node, we evaluate if it is a feasible solution, and decide the next node to evaluate.

1.  First, we calculate the remaining room at the node. If the value is negative, then it is not a feasible solution. We should stop exploring down the branch.
1. Then we calculate the value on hand and compare it with the best solution. If the current node yields better result, we update the best solution.
1. Next we estimate the future rewards of exploring down the branch by iteratively taking the next valuable item (or fraction of an item) until the bag is full. Note that this is a greedy estimation. If the value plus the greedy future reward estimation is smaller than the current best solution, there is no reason exploring down the node.
1. Remember Last in First out (LIFO)? If we decide to stop exploring down a node, we should go up to its ancestor that puts the last item in the bag, and explore the option of not taking this item: basically moving to the right child node of the youngest ancestor node whose right child node has not been visited yet. Otherwise, we move on to the left child node of the current node.
1. Stop if traverse back to root node and both children of the root node have already been explored. Return the best solution on hand.

## Example

Let's go through a toy problem.

**Problem Statement**

The capacity limit of the bag is 7. The list of  items are shown below. Find the set of the items that maximize the value in bag under the capacity constraint.

| Item  | Value  | Weight  |
|---|---|---|
| 0  | 1  | 1  |
| 1  | 4  | 3  |
| 2  | 5  | 4  |
| 3  | 7  | 5  |

**Solution**

First, sort the list of item  by (unit_weight_value, weight) in descending order.

|Index in sorted list| Item (original index)  | Value  | Weight  | Unit Weight Value|
|---|---|---|---|---|
| 0 | 3  | 7  | 5  | 1.4 |
| 1 | 1  | 4  | 3  | 1.3333333333333333|
| 2 | 2  | 5  | 4  | 1.25 |
| 3 | 0  | 1  | 1  | 1.0 |

The log below shows how does Branch and Bound traverse the tree.
```
DEBUG:knapsack_solver:(0 None), room left: 7, value:0, estimate: 9.666666666666666 
DEBUG:knapsack_solver:(1 1), room left: 2, value:7, estimate: 9.666666666666666 
DEBUG:knapsack_solver:(2 1), room left: -1, value:-inf, estimate: -inf 
DEBUG:knapsack_solver:(2 0), room left: 2, value:7, estimate: 9.5 
DEBUG:knapsack_solver:(3 1), room left: -2, value:-inf, estimate: -inf 
DEBUG:knapsack_solver:(3 0), room left: 2, value:7, estimate: 8 
DEBUG:knapsack_solver:(4 1), room left: 1, value:8, estimate: 8 
DEBUG:knapsack_solver:(4 0), room left: 2, value:7, estimate: 7 
DEBUG:knapsack_solver:(1 0), room left: 7, value:0, estimate: 9.0 
DEBUG:knapsack_solver:(2 1), room left: 4, value:4, estimate: 9.0 
DEBUG:knapsack_solver:(3 1), room left: 0, value:9, estimate: 9 
DEBUG:knapsack_solver:(4 1), room left: -1, value:-inf, estimate: -inf 
DEBUG:knapsack_solver:(4 0), room left: 0, value:9, estimate: 9 
DEBUG:knapsack_solver:(3 0), room left: 4, value:4, estimate: 5 
DEBUG:knapsack_solver:(2 0), room left: 7, value:0, estimate: 6 
```

Let's walk through the first 4 line of the log.

At the root node `(0 None)`, we calculate the remaining room (7, same as the capacity of the bag), value (0, as we have not take anything yet), and the greedy estimate of exploring down ($7+4/3*(7-5)=9.666666666666666$, also the upper bound of the solution). 

From the root node, we go to its left child, which represents taking the 1st item in the sorted list. Let's call this node `(1, 1)`. The first value in the  tuple is the depth of the tree, you can also think of it as the 1st item in the sorted list. The second value in the tuple is a binary; 1 means taking the item, 0 means not taking the item. At `(1, 1)`, the remaining room is $7-5=2$, the value is 7, and since we are taking the 1st item, the estimate is the same as its parent. This is the also the best solution so far (node visited with the highest value).

Since `(1, 1)` is a feasible solution, and not the last item, we will move on to its left child `(2, 1)`, which stands for taking the 2nd item in the sorted list. The weight of the 2nd item in the sorted list is 3, and is smaller than the remaining capacity, which is 2. Therefore, it is not a feasible solution, so we should traverse back. We move to the unexplored right child of `(2, 1)`'s parent `(1, 1)`, node `(2, 0)`.

Since `(2, 0)` stands for not taking the 2nd item in the sorted list, the remaining capacity and value is the same as its parent `(1, 1)`. The estimate at `(2, 0)` should be the value at `(1, 1)`, plus the value of greedily taking in the 3rd and 4th item (or fractions of them) in the sorted list, giving us $(7+5/4)*2=9.5$. 9.5 is the upper bound of exploring down the branch, and it is smaller than the current best value 7, so we should keep going down to its left child `(3, 1)`.

Now that you understand how to read the log, you should be able to follow the remaining nodes visited. You can also use the graph below as a guide. The green edges are paths traversed,  and the green node is the optimal solution: taking the 2rd and 3rd item from the sorted list; their total value is 9, and total weight is 7.

![bnb_tree2.png]({{site.url}}/img/bnb_tree2.png)

## Implementation

You can see my implementation in python [here](#https://github.com/zij212/discrete_optimization/blob/master/knapsack/knapsack_solver_bnb.py)

The input needs to be in the following format.

```
num_of_items capacity_of_bag
item1_value item1_weight
item2_value item2_weight
item3_value item3_weight
...
```



