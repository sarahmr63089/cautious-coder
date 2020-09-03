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