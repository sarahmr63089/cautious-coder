---
layout: post
title: Solving the House Robber Problem with Dynamic Programming
date: 2020-12-11 15:41:38 -0400
author: Sarah
featured-image: 
featured-image-alt: 
---

Recently, I've been diving into dynamic programming with the help of this [video series](https://www.youtube.com/playlist?list=PLVrpF4r7WIhTT1hJqZmjP10nxsmrbRvlf) by Andrey Grehov. DP is a notoriously tricky problem-solving approach, but Grehov's method has helped clear things up considerably. To further cement the process, I've been trying to solve other DP problems that he doesn't cover in his course. One of these is this [House Robber](https://leetcode.com/problems/house-robber/) problem found on Leetcode. 

Before I dive into the House Robber problem, let me explain some of the concepts I've been learning. Dynamic programming is an optimization method, often used to solve combinatorial problems (the how many? questions) or optimization problems (minimum number of steps, maximum profits, etc.). With DP you find the optimal solution to a problem by constructing the optimal solutions to subproblems. 

There are a couple different approaches to finding the optimal approach, top down or bottom up. The top down approach often involves a combination of memoization and recursion. Memoization is an optimization technique that stores, or caches, the results of calculations for later use. With a bottom up approach, you take small components of the problem and assemble them into the solution. It's also called tabulation and always uses iteration.

As a framework for solving this problem, I am going to refer to Grehov's 5 steps for solving DP problems:

1. Define the objective function
2. Identify base cases
3. Identify the recurrence relation or the transition function
4. Identify the order of computation; Is this a top down or bottom up approach?
5. Where is the answer located? 

The problem: You are a robber planning to rob a street of houses. Each house has a potential profit, but adjacent houses are connected by a security system. What is the maxmum profit you can make without robbing adjacent houses?

Step One: Objective Function

The objective function is the what you want to minimize or maxize for the solution and is often found in the description of the problem. Here the objective function is F(i) = the maximum profit when robbing ith houses.

Step Two: Identify Base Cases

This step tripped me up when I first tried this problem. I started with the base case of solving one house and then two houses, but it would have been better to start with zero. You could choose to rob 0 houses and what would be the max profit?

So our base cases are:
  - rob 0 houses: max profit = 0
  - rob 1 house: max profit = value of first house

Step Three: Identify the Recurrence Relation or the Transition Function

This is the function that we can apply to all our subproblems to find our solution. We can find it by identifying patterns from the first few subproblems:
  - rob 0 houses: max profit = 0
  - rob 1 house: max profit = value of first house
  - rob 2nd house: max profit = either value of this house or value of previous house
  - rob 3rd house: max profit = Max(previous house OR this house + value of first house)
  - rob 4th house: max profit = Max(previous house OR this house + value of second house)
  etc.

By the time we get to the fourth and fifth houses our comparisons represent sums of house profits from non-adjacent houses and we can look at our transition function as:

max profit = max(f(n - 1), n + f(n - 2))

Step Four: Identify the Order of Computation

My solution below is a bottom up approach.

Step Five: Where is the answer located?

The answer will be located in the last place of the maxProfit array.

Let's a look at my solution where the houses are represented by array "nums":

{% highlight javascript %}

var rob = function(nums) {
  // if there are no houses
  if (nums.length === 0) {
    return 0
  } 
  
  // our profit array which will track the max profit as we traverse the houses
  let maxProfit = []
  
  // base cases
  maxProfit[0] = 0
  maxProfit[1] = nums[0]
  
  // because maxProfit starts at zero but we start with the house at nums[1], the loop here starts at 1, but we push to the next point in maxProfit
  for (let i = 1; i < nums.length; i++) {
    maxProfit[i + 1] = Math.max(maxProfit[i], nums[i] + maxProfit[i - 1])    
  }

  return maxProfit[nums.length]
};

{% endhighlight %}