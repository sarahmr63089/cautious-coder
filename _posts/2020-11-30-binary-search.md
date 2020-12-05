---
layout: post
title: Binary Search
date: 2020-11-30 15:41:38 -0400
author: Sarah
---

Sometimes you have a sorted array and you want to know if a certain number is present in the array. Searching through an array is a lengthy process. Without knowing where an element is located you must check each element to see if it's the one you're looking for -- a search taking linear time O(n) where n is the number of elements. It's pretty easy to see that this gets out of hand when managing large arrays of data. 

The bright side is, if you're dealing with a sorted array, there is a better way to search! Binary search is a searching algorithm that drastically reduces the amount of time it takes to search an array (from O(n) to O(log(n))). This is accomplished by continually halving the portion of the array to be searched until the element is found. This blog post will break down the iterative and recursive approach to the binary search method. 

Both approaches have the same basic steps. For each pass through the loop or function find the middle point and compare the value at the to the target. If the target is higher than the middle point discard the lower half of the collection and run the loop again with the upper half. If the target is lower than the middle, discard the upper half and repeat the loop. This can be repeated until the middle and target are equal or until the collection has been exhausted without finding the target. 

Let's code!

The function will either return the index of the position of target in the array or -1, indicating that target is not in the array.

Method 1:

{% highlight javascript %}

function binarySearch(arr, target) {
  // find the the upper and lower indexes of the array
  let lowPoint = 0;
  let highPoint = arr.length - 1;

  // the loop continues until every element has been checked by moving the upper and lower boundaries
  while (low <= high) {
    
    // find the middle of array (rounding down with Math.floor())
    midPoint = Math.floor((lowPoint + highPoint) / 2)

    // when you find the target -- return the index
    if (arr[midPoint] === target) {
      return midPoint;
    }

    // if the target is in the lower half (smaller than the middle) move the high point down to the middle ( -1 as you've already checked the middle)
    if (arr[midPoint] > target) {
      highPoint = midPoint - 1;
    } 
    
    // if the target is in the upper half, move the low point up to the middle + 1
    else {
      lowPoint = midPoint + 1;
    }
  }

  // if the target isn't in the array, return -1
  return -1;
}

{% endhighlight %}

The recursive method is very similar, but the recursive function takes the high and low point as parameters in addition to the array and the target.

Method 2: 

{% highlight javascript %}

function binarySearch(arr, target) {
  let lowPoint = 0;
  let highPoint = arr.length - 1;

  return findIndex(arr, target, highPoint, lowPoint)
}

function findIndex(arr, target, high, low) {
  if (low > high) {
    return -1;
  }

  let mid = Math.floor((low + high) / 2);

  if (arr[mid] === target) {
    return mid;
  }

  if (arr[mid] > target) {
    return findIndex(arr, target, mid - 1, low)
  } else {
    return findIndex(arr, target, high, mid + 1)
  }
}

{% endhighlight %}

As a recap, this is a much more efficient way to search through a sorted array. Instead of looking at each element, half of the collection is tossed each time you search and this reduces the time complexity from O(n) to O(log(n)).