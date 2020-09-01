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

Below, I've created a class QuickFind which is instantiated with an array (named id) of integers of size num. The class contains class methods 'connected' and 'union.' The connected method checks if indexes p and q are in the same component by checking if they point to the same value in id. The union method joins two integers by changing all the values that match id[p] to the value of id[q].

The downside to this solution is it is too slow--it has two loops that go through the whole array when searching for unions and creating unions is quadratic which does not scale. With large amounts of data this solution takes years of computer time (30+).

```
class QuickFind {

  <!-- build an array of num length -->
  constructor(num) {
    this.id = [];
    for (let i = 0; i < num; i++){
      this.id.push(i)
    }
  }

  <!-- checks whether p and q are in the same component -->
  connected = (p, q) => {
    return this.id[p] === this.id[q];
  }

  <!-- changes all entries with id[p] to id[q] -->
  union = (p, q) => {
    let pid = this.id[p];
    let qid = this.id[q];
    for (let i = 0; i < this.id.length; i++){
      if (this.id[i] === pid) {
        this.id[i] = qid;
      }
    }
  }
}

let array = new QuickFind(10)

```

ADD IN SCREENSHOTS AND A DIAGRAM

### Quick Union

The QuickUnion class is also instantiated with an array 'id' of size num and has class methods 'connected' and 'union.' The key difference with this solution is the findRoot method. This method finds and returns the root at a given index. The root is where the value at i is the same as the index. The connected method uses findRoot to check if p and q are in the same component and the union method uses findRoot to join p and q by giving the root of p the root of q.

```
class QuickUnion {

  <!-- build an array of num length -->
  constructor(num) {
    this.id = [];
    for (let i = 0; i < num; i++){
      this.id.push(i)
    }
  }

  <!-- root is where id[i] is same as index -->
  findRoot = (i) => {
    while (i !== this.id[i]) {
      i = this.id[i];
    }
    return i;
  }

  <!-- checks whether p and q are in the same component by checking to see if they have the same root -->
  connected = (p, q) => {
    return this.findRoot(p) === this.findRoot(q);
  }

  <!-- connects p and q by making the root of p have the root of q as it's parent  -->
  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);
    this.id[i] = j;
  }
}

let array = new QuickUnion(10)
```

ADD SOME DIAGRAMS AND SCREENSHOTS

This isn't the most efficient solution, either, but it can be improved in a couple different ways.

### Quick Union Improvement 1: Weighted

One way that the previous solution can be improved is by taking into account the size of the trees. Below I've added an if statement to the union method that checks which tree is larger before joining them. The smaller tree is always added to the bigger tree. This solution improves the running time overall, the section below is the most efficient.

```
  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);

    if ( i === j) {
      return 
    }
    
    if (i.length < j.length) {
      id[i] = j; 
      j.length += i.length;
    } else {
      id[j] = i;
      i.length += j.length
    }
  }
```

### Quick Union Improvement 2: Path Compression

This improvement modifies the findRoot method to compress the path length. This could be done in two passes by first finding the root of p and then setting the value of each examined element to that root, which flattens the path. 

ADD PICTURE

Or it could be done in one pass by setting every other examined element to point directly to its grandparent. This second way halves the path.

ADD PICTURE

The code below is the one pass option.

```
  findRoot = (i) => {
    while ( i != this.id[i]) {
      this.id[i] = this.id[this.id[i]];
      i = id[i]
    }
    return i;
  }
```

This last improvement is the most efficient, taking what was a quadratic, unscalable solution (the UnionFind class) to a linear solution. 

Working through the lessons and videos about this alogrithm was difficult. It's been a while since I worked with higher-level math and wrapping my brain around the efficiency of these solutions took a long time. It helped to go through the solutions on CodePen and create visuals to better understand each improvement.

Source:
[Princeton University, Algorithms Part I, taught by Robert Sedgewick and Kevin Wayne](https://www.coursera.org/learn/algorithms-part1)