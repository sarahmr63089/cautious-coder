---
layout: post
title: Union Find Algorithm Part I
date: 2020-09-01 15:41:38 -0400
author: Sarah
---
Earlier this summer, I tried to balance an algorithms course and my bootcamp workload. I chose Princeton's Algorithms Part I with Robert Sedgewick and Kevin Wayne, which can be found on Coursera. Ultimately, that turned out to be too much to balance, but before I dropped the algorithms course I watched the videos on Union Find. This blog post (and Part II) works through the code presented in those lectures (originally written in Java, but I've written in out in Javascript for my own comfort). 

The Union Find algorithm (also called Disjoint Set) finds or creates a connection between two objects. When considering the problem, we can look at the connections in a few different ways:

  - reflexive: p is connected to p 
  - symmetric: if p is connected to q then q is connected to p
  - transitive: if p is connected to q and q is connected to r, then p is connected to r

This algorithm has two parts: the find query and the union query. The find query checks if two objects are in the same group, whereas the union query joins two objects that are not already in the same group. 

There are different ways to approach this problem and I will cover two with the most efficient way being the last way.

### Quick Find

Below, I've created a class 'QuickFind' which is initialized with an array (named 'ids') of objects of size num. Each object in ids begins as its own group, with their initial group id being themself (in the examples below this is their index). The class contains class methods 'connected' and 'union.' The connected method checks if the objects at indexes p and q are in the same group by checking if they point to the same group id in ids. The union method joins two objects by changing all the group ids that match ids[p] to the group id of ids[q].

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

let newIds = new QuickFind(10)

newIds.ids
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

newIds.connected(2, 5)
// false

newIds.union(2, 5)

newIds.ids
// [0, 1, 5, 3, 4, 5, 6, 7, 8, 9]

newIds.union(7, 2)

newIds.ids
// [0, 1, 5, 3, 4, 5, 6, 5, 8, 9]

newIds.union(2, 8)

newIds.ids
// [0, 1, 8, 3, 4, 8, 6, 8, 8, 9]

newIds.connected(2, 8)
// true

ADD SOME METHOD EXAMPLES

{% endhighlight %}

After putting together this class I tested out some code in CodePen. Below the class are some of the tests I ran in the CodePen console. First, I asked the console for the original array and checked two random objects (2 and 5) for a connection. Then I created a union between these two objects. After the union I re-printed the array and 2's group id was changed to 5 to signal their connection. I think created a few more unions and ended by joining 2 and 8. The final array shows that the group ids for indexes 2, 5, 7 and 8 all match meaning they are all in the same group.

To further cement this concept, I decided to create some visuals with a site called 'Creately.' Below, I've represented an array of 10 objects in which each object is initialized with its own group id, here their index number.

![initial setup](/cautious-coder/assets/Initial-Setup.png)

If we create a union between 2 and 3, the group id of 2 is changed to 3.

![union find visuals--1](/cautious-coder/assets/quickunion1.png)

When a union is created between 3 and 7 all objects that match the group id of ids[3] are changed to have the group id of ids[7].

![union find visuals--2](/cautious-coder/assets/UnionFindIMg.png)

Unfortunately, this is not the most efficient way to approach this problem. The downside is that it's too slow--it has two for loops that go through the whole array, and creating unions runs in quadratic time, which does not scale. With large amounts of data this solution could take years of computer time.

### Quick Union

A moderate improvement on Quick Find is Quick Union. The QuickUnion class is also initialized with an array 'ids' of size num and has class methods 'connected' and 'union.' The key difference with this solution is how we represent the groups of joined objects. They are represented hierarchically, similar to a tree. Instead of all the objects having the same group id, each object points to its parent until the root object is reached (the root points to itself). For example, if we create a union between 2 and 6 and then another union between 6 and 9, we get an array with one 3-part component. When we go to ids[2] we are directed to go to its parent at ids[6]. When we go to ids[6], we are directed to its parent at ids[9]. ids[9] points to itself and we know we've found the root of this tree.

![quick union one way](/cautious-coder/assets/QuickUnion.png)

This component can also be visualized this way where the parent is the number on the left and the object itself is on the right.

![quick union second way](/cautious-coder/assets/quickunion2.png)

In the QuickUnion class most of the code from QuickFind is duplicated, with the biggest change being findRoot method. This method finds and returns the root of a tree at a given index. The root is where ids[i] is the same as the index i. The connected method uses findRoot to check if p and q are in the same component and the union method uses findRoot to join p and q by giving the p's root the root of q.

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
  // by checking to see if they have the same root

  connected = (p, q) => {
    return this.findRoot(p) === this.findRoot(q);
  }

  // connects p and q by making the root of p 
  // have the root of q as its parent

  union = (p, q) => {
    let i = this.findRoot(p);
    let j = this.findRoot(q);
    this.ids[i] = j;
  }
}

let newIds = new QuickUnion(10)

newIds.ids
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

newIds.connected(2, 5)
// false

newIds.union(2, 5)

newIds.ids
// [0, 1, 5, 3, 4, 5, 6, 7, 8, 9]

newIds.union(7, 2)

newIds.ids
// [0, 1, 5, 3, 4, 5, 6, 5, 8, 9]

newIds.union(2, 8)

newIds.ids
// [0, 1, 5, 3, 4, 8, 6, 5, 8, 9]

newIds.findRoot(2)
// 8

{% endhighlight %}

Below the class are some examples I plugged into CodePen, starting with the original array. I first created a union between 2 and 5, and when the array is printed we can see that ids[2] points to its parent at 5. At ids[5] we see 5, meaning that 5 is the root. After a second union between 7 and 2, we see that both 2 and 7 point to their parent at 5. The union method always changes the root of the first argument to the root of the second argument, so 7's root changes from 7 to 2's root, which is 5. The last example is a union between 2 and 8. If we follow the array here we see that 2 points to its parent 5 which now points to 8, its new root. The union method changed 2's root to 8's root, which is 8 (I checked this below with the findRoot method).

This isn't the most efficient solution, either, as the trees can get too tall. It can be improved in a couple different ways, but I'm not going to cover that here. Stay tuned for those improvements in Part II!

Working through the lessons and videos about this algorithm was difficult for me. It's been a while since I worked with higher-level math and wrapping my brain around the efficiency of these solutions and their logic took a long time. It helped to go through the solutions on CodePen and create visuals to better understand each improvement.

Source:
[Princeton University, Algorithms Part I, taught by Robert Sedgewick and Kevin Wayne](https://www.coursera.org/learn/algorithms-part1)