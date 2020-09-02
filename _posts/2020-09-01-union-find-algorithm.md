---
layout: post
title: Union Find Algorithm
date: 2020-09-01 15:41:38 -0400
author: Sarah
---
Earlier this summer, I tried to balance working through an algorithms course with my bootcamp workload. SAY THE COURSE HERE Ultimately, that turned out to be too much to balance, but before I dropped the algorithms course I watched the videos on Union Find. This algorithm finds or creates a connection between two objects. When considering the problem, we can look at the connections in a few different ways:

  - reflexive: p is connected to p 
  - symmetric: if p is connected to q then q is connected to p
  - transitive: if p is connected to q and q is connected to r, then p is connected to r

This algorithm has two parts: the find query and the union query. The find query checks if two objects are in the same group, whereas the union query joins two objects that are not already in the same group. 

There are different ways to approach this problem and I will cover two with the most efficient way being the last way.

### Quick Find

Below, I've created a class QuickFind which is instantiated with an ids (named 'ids') of integers of size num. The class contains class methods 'connected' and 'union.' The connected method checks if indexes p and q are in the same group by checking if they point to the same value in ids. The union method joins two integers by changing all the values that match ids[p] to the value of ids[q].

{% highlight javascript %}
class QuickFind {

  // build an array of num length
  constructor(num) {
    this.ids = [];
    for (let i = 0; i < num; i++){
      this.ids.push(i)
    }
  }

  // checks whether p and q are in the same component
  connected = (p, q) => {
    return this.ids[p] === this.ids[q];
  }

  // changes all entries with ids[p] to ids[q]
  union = (p, q) => {
    let pids = this.ids[p];
    let qids = this.ids[q];
    
    for (let i = 0; i < this.ids.length; i++){
      if (this.ids[i] === pids) {
        this.ids[i] = qids;
      }
    }
  }
}

let ids = new QuickFind(10)

ADD SOME METHOD EXAMPLES

{% endhighlight %}

After putting together this class I tested out some code in CodePen and created some visuals. We can represent an array of 10 objects where each object is initialized with its own group id, here their index number.

![initial setup](/cautious-coder/assets/Initial-Setup.png)

If we create a union between 2 and 3, the group id of 2 is changed to 3.

![union find visuals--1](/cautious-coder/assets/quickunion1.png)

When a union is created between 3 and 7 all objects that match the group id of ids[3] are changed to have the group id of ids[7].

![union find visuals--2](/cautious-coder/assets/UnionFindIMg.png)

The downside to this solution is that it's too slow--it has two loops that go through the whole array when searching for unions, and creating unions runs in quadratic time, which does not scale. With large amounts of data this solution could take years of computer time.

### Quick Union

A moderate improvement on Quick Find is Quick Union. The QuickUnion class is also instantiated with an array 'ids' of size num and has class methods 'connected' and 'union.' The key difference with this solution is how we represent the groups of joined objects. They are represented hierarchically, similar to a tree. 

EXPLAIN HOW EACH NODE REFERENCES ITS PARENT

If we create a union between 2 and 6 and then another union between 6 and 9, we get an array with one 3-part component. 

![quick union one way](/cautious-coder/assets/QuickUnion.png)

If we try to find the root starting at 2, we are pointed to 6, then to 9 which is the root. This component can also be visualized this way:

![quick union second way](/cautious-coder/assets/quickunion2.png)

The findRoot method finds and returns the root of a tree at a given index. The root is where the value at i is the same as the index. The connected method uses findRoot to check if p and q are in the same component and the union method uses findRoot to join p and q by giving the root of p the root of q.

{% highlight javascript %}
class QuickUnion {

  // build an array of num length
  constructor(num) {
    this.ids = [];
    for (let i = 0; i < num; i++){
      this.ids.push(i)
    }
  }

  // root is where ids[i] is same as index
  findRoot = (i) => {
    while (i !== this.ids[i]) {
      i = this.ids[i];
    }
    return i;
  }

  // checks whether p and q are in the same component 
  by checking to see if they have the same root

  connected = (p, q) => {
    return this.findRoot(p) === this.findRoot(q);
  }

  // connects p and q by making the root of p 
  have the root of q as its parent

  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);
    this.ids[i] = j;
  }
}

let ids = new QuickUnion(10)

ADD CODE EXAMPLES
{% endhighlight %}

This isn't the most efficient solution, either, as the trees can get too tall, but it can be improved in a couple different ways.

### Quick Union Improvement 1: Weighted

One way that the previous solution can be improved is by taking into account the size of the trees. With this improvement each instance of weighted quick union is initialized with a "size" array that tracks the size of each group. Then in the union method there is an if statement that checks which tree is larger before joining them. The smaller tree is always added to the bigger tree. 

{% highlight javascript %}

  // initialize with an array that shows the size of each group at it's index
  constructor(num) {
    this.ids = [];
    this.size = [];

    for (let i = 0; i < num; i++){
      this.ids.push(i)
      this.size.push(0)
    }
  }

  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);

    if ( i === j) {
      return 
    }
    
    if (this.size[i] < this.size[j]) {
      this.ids[i] = j; 
      this.size[j] += this.size[i];
    } else {
      this.ids[j] = i;
      this.size[i] += this.size[j]
    }
  }
{% endhighlight %}

### Quick Union Improvement 2: Path Compression

This improvement modifies the findRoot method in QuickUnion to compress the path length. This could be done in two passes by first finding the root of p and then setting the value of each examined element to that root, which flattens the path. Or it could be done in one pass by setting every other examined element to point directly to its grandparent. This second way halves the path.

The code below is the one pass option.

{% highlight javascript %}
  findRoot = (i) => {
    while ( i != this.ids[i]) {
      this.ids[i] = this.ids[this.ids[i]];
      i = this.ids[i]
    }
    return i;
  }
{% endhighlight %}

The combination of these two improvements take what was a quadratic, unscalable solution (the UnionFind class) to a linear solution. 

FULL CODE HERE

{% highlight javascript %}

class WeightedQuickUnionWithPathCompression {

  constructor(num) {
      this.ids = [];
      this.size = [];

      for (let i = 0; i < num; i++){
        this.ids.push(i)
        this.size.push(0)
      }
    }

  findRoot = (i) => {
    while ( i != this.ids[i]) {
      this.ids[i] = this.ids[this.ids[i]];
      i = this.ids[i]
    }
    return i;
  }

  connected = (p, q) => {
    return this.findRoot(p) === this.findRoot(q);
  }


  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);

    if ( i === j) {
      return 
    }
    
    if (this.size[i] < this.size[j]) {
      this.ids[i] = j; 
      this.size[j] += this.size[i];
    } else {
      this.ids[j] = i;
      this.size[i] += this.size[j]
    }
  }
}

{% endhighlight %}


Working through the lessons and videos about this algorithm was difficult. It's been a while since I worked with higher-level math and wrapping my brain around the efficiency of these solutions took a long time. It helped to go through the solutions on CodePen and create visuals to better understand each improvement.

Source:
[Princeton University, Algorithms Part I, taught by Robert Sedgewick and Kevin Wayne](https://www.coursera.org/learn/algorithms-part1)