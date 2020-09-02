---
layout: post
title: Union Find Algorithm
date: 2020-09-01 15:41:38 -0400
author: Sarah
---
Earlier this summer, I tried to balance working through an algorithms course with my bootcamp workload. Ultimately, that didn't work out, but before I dropped the algorithms course I watched the videos on Union Find. This algorithm finds or creates a path between two objects. When considering the problem, we can look at the connections in a few different ways:

  - reflexive: p is connected to p 
  - symmetric: if p is connected to q then q is connected to p
  - transitive: if p is connected to q and q is connected to r, then p is connected to r

These connected objects can be structured as components, or sets of mutually connected objects. This algorith has two parts, the find query and the union query. The find query checks if two objects are in the same component, whereas the union query joins two objects that are not already in the same component. 

There are different ways to approach this problem and I will cover twow with the most efficient way being the last way.

### Quick Find

Below, I've created a class QuickFind which is instantiated with an array (named array) of integers of size num. The class contains class methods 'connected' and 'union.' The connected method checks if indexes p and q are in the same component by checking if they point to the same value in array. The union method joins two integers by changing all the values that match array[p] to the value of array[q].

The downside to this solution is it is too slow--it has two loops that go through the whole array when searching for unions and creating unions is quadratic which does not scale. With large amounts of data this solution takes years of computer time (30+).

{% highlight ruby %}
class QuickFind {

  <!-- build an array of num length -->
  constructor(num) {
    this.array = [];
    for (let i = 0; i < num; i++){
      this.array.push(i)
    }
  }

  <!-- checks whether p and q are in the same component -->
  connected = (p, q) => {
    return this.array[p] === this.array[q];
  }

  <!-- changes all entries with array[p] to array[q] -->
  union = (p, q) => {
    let parray = this.array[p];
    let qarray = this.array[q];
    for (let i = 0; i < this.array.length; i++){
      if (this.array[i] === parray) {
        this.array[i] = qarray;
      }
    }
  }
}

let array = new QuickFind(10)

{% endhighlight %}

After putting together this class I tested out some code in CodePen and created some visuals. We can represent an array of 10 integers like this:

![initial setup](/cautious-coder/assets/Initial-Setup.png)

If we create a union between 2 and 3, the value of 2 is changed to 3.

![union find visuals--1](/cautious-coder/assets/quickunion1.png)

When a union is created between 3 and 7 all values that match the value of array[3] are changed to have the value of array[7].

![union find visuals--2](/cautious-coder/assets/UnionFindIMg.png)

### Quick Union

The QuickUnion class is also instantiated with an array 'array' of size num and has class methods 'connected' and 'union.' The key difference with this solution is the findRoot method. This method finds and returns the root at a given index. The root is where the value at i is the same as the index. The connected method uses findRoot to check if p and q are in the same component and the union method uses findRoot to join p and q by giving the root of p the root of q.

{% highlight ruby %}
class QuickUnion {

  <!-- build an array of num length -->
  constructor(num) {
    this.array = [];
    for (let i = 0; i < num; i++){
      this.array.push(i)
    }
  }

  <!-- root is where array[i] is same as index -->
  findRoot = (i) => {
    while (i !== this.array[i]) {
      i = this.array[i];
    }
    return i;
  }

  <!-- checks whether p and q are in the same component 
  by checking to see if they have the same root -->

  connected = (p, q) => {
    return this.findRoot(p) === this.findRoot(q);
  }

  <!-- connects p and q by making the root of p 
  have the root of q as its parent  -->

  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);
    this.array[i] = j;
  }
}

let array = new QuickUnion(10)
{% endhighlight %}

If we create a union between 2 and 6 and then another union between 6 and 9, we get an array with one 3-part component. 

![quick union one way](/cautious-coder/assets/QuickUnion.png)

If we try to find the root starting at 2, we are pointed to 6, then to 9 which is the root. This component can also be visualized this way:

![quick union second way](/cautious-coder/assets/quickunion2.png)

This isn't the most efficient solution, either, but it can be improved in a couple different ways.

### Quick Union Improvement 1: Weighted

One way that the previous solution can be improved is by taking into account the size of the trees. Below I've added an if statement to the union method that checks which tree is larger before joining them. The smaller tree is always added to the bigger tree. This solution improves the running time overall, the section below is the most efficient.

{% highlight ruby %}
  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);

    if ( i === j) {
      return 
    }
    
    if (i.length < j.length) {
      array[i] = j; 
      j.length += i.length;
    } else {
      array[j] = i;
      i.length += j.length
    }
  }
{% endhighlight %}

### Quick Union Improvement 2: Path Compression

This improvement modifies the findRoot method in QuickUnion to compress the path length. This could be done in two passes by first finding the root of p and then setting the value of each examined element to that root, which flattens the path. Or it could be done in one pass by setting every other examined element to point directly to its grandparent. This second way halves the path.

The code below is the one pass option.

{% highlight ruby %}
  findRoot = (i) => {
    while ( i != this.array[i]) {
      this.array[i] = this.array[this.array[i]];
      i = array[i]
    }
    return i;
  }
{% endhighlight %}

This last improvement is the most efficient, taking what was a quadratic, unscalable solution (the UnionFind class) to a linear solution. 

Working through the lessons and videos about this alogrithm was difficult. It's been a while since I worked with higher-level math and wrapping my brain around the efficiency of these solutions took a long time. It helped to go through the solutions on CodePen and create visuals to better understand each improvement.

Source:
[Princeton University, Algorithms Part I, taught by Robert Sedgewick and Kevin Wayne](https://www.coursera.org/learn/algorithms-part1)