---
layout: post
mathjax: true
title:  "Explainable k-means Clustering: Provable Guarantees"
date:   2020-07-04 10:00:00 -0700
categories: jekyll update
tags: explainable
author: bla
excerpt:  we define tree-based explainable clustering, show that popular algorithms can be arbitrarily bad, and present a new algorithm that is provably good.
---

**TL;DR:** we define tree-based explainable clustering, show that popular algorithms can be arbitrarily bad, and (most importantly) present a new algorithm that is provably good.

### Explainability: why?

Machine learning models are mostly "black box", they make good prediction with high accuracy, but it is unclear why. These days,  machine learning is entering fields like healthcare and transportation. In these fields, accuracy is not the only objective. No matter how well a computer is making its predictions, we can’t even imagine a doctor performing a surgery, just because a computer told him so. 

We want something more. We want to understand why the predictions are the way they are.  We want to have explanations for the predictions. But what is an explanation? There are several suggestions, we focus on one --- tree-based explanation.

### Tree-based explainable clustering
By now we all agree that explainability is important, but how can we define it? and how can we generate explanations? Those are hard and important questions and we answer them within the k-means clustering problem. In this problem we are given a dataset and the goal is to partition it to a few clusters such that the k-menas cost is minimial. See for example the picture on the left where points were divided into 5 groups. 

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/intro_IMM_blog_pic_1.png" width="40%" style="margin: 0 auto">
  <figcaption>
    Near optimal 5-means clusteringfigcaption
  </figcaption>
</figure>
{:refdef}

We want to make this clustering explainable. It is not clear what is a explanation exactly. But everyone will agree that a small flow chart is pretty explainable. Or in machine learning language, we want to find a small decision tree. In this tree, each inner node corresponds to a coordinate and threshold and a leaf corresponds to a cluster label. The smallest possible decision tree has $k$ leaves. See the next tree for an illustration. 

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/intro_IMM_blog_pic_2.png" width="40%" style="margin: 0 auto">
  <figcaption>
    threshold tree
  </figcaption>
</figure>
{:refdef}

A decision tree imposes a partition aligned to the axis, as you can see in the picture below. It looks close to the optimal clustering that we started with. Which is great. But can we do it for all data sets? How?

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/intro_IMM_blog_pic_3.png" width="40%" style="margin: 0 auto">
  <figcaption>
    geometric representation of the threshold tree
  </figcaption>
</figure>
{:refdef}

### General scheme 
Unsupervised learning is, in general, a hard problem, and clustering, in particular, too. So how can we find a clustering defined by a threshold tree? Transform the problem into a supervised learning problem! How you might ask? We can use any clustering algorithm that will return a good, but not explainable clustering. These will form the labeling. The goal now is to be close to our labels - but in an explainable way. Next, we can use a supervised algorithm that learns a decision tree. Let's sum up these three steps:
1. Find a clustering using some clustering algorithm
2. Label each example according to its cluster
3. Call a supervised algorithm that learns a decision tree

 
Which algorithm can we use in step 3? Maybe the popular ID3 algorithm? 

### Can we use ID3 algorithm? 
Short answer: no.  

One might hope that in step 3, in the previous scheme, we might be able to use the known [ID3](https://link.springer.com/content/pdf/10.1007/BF00116251.pdf) algorithm. But there are datasets where ID3 will perform poorly. Here is an example: 

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/intro_IMM_blog_pic_4.png" width="40%" style="margin: 0 auto">
  <figcaption>
    ID3 perform poorly on this dataset
  </figcaption>
</figure>
{:refdef}

The dataset is composed of three clusters, as you can see in the figure above. Two large clusters (0 and 1 in the figure) have centers (-2, 0) and (2, 0) accordingly and small noise. The third cluster (2 in the figure) is composed of only two points that are very very (very) far away from clusters 0 and 1. Given these data, ID3 will prefer to maximize the information gain and split between clusters 0 and 1. Recall that the final tree has only three leaves. This means that in the final tree one point in cluster 2 must be with either cluster 0 or cluster 1, thus the cost is enormous.
To solve this problem we design a new algorithm called Iterative Mistake Minimization (IMM). 

### IMM algorithm for explainable clustering
After we learned that ID3 has an inhearent problem and before we give up on the general scheme, can we use  different decision-tree algorithm?  Well, since we write this post you probably know the answer: there is such an algorithm.
The main definition that we need is a mistake:

<center>
<span style="font-family:Papyrus; font-size:2em;align-self: center;">If a point and its center diverge,
 <br> then it counts as a mistake</span>
</center>

To exemplify the concept of mistake, let's go back to the previous dataset where ID3 failed on. Focus on the first split again (there are three clusters, so the final tree will contain 3 leaves and 2 inner nodes). The ID3 split has one mistake since one of the points in cluster 2 will be separated from its center. On the other hand, the horizontal split has $0$ mistakes: the two large clusters will go with their centers to one side of the tree and the small cluster will go with its center to the other side of the tree.  

This is all we need in order to describe the IMM algorithm:

{% highlight python %}
def IMM(points, centers):
  if |centers| > 1:
    i, theta = split that minimizes number of mistakes
    create node that contains i, theta
    points_left = points[points[:,i]<=theta]
    points_right = points[points[:,i]>theta]
    split centers similarly 
  IMM(points_left, centers_left)
  IMM(points_right, centers_right)
end
{% endhighlight %}

Here is an illustration of the IMM algorithm. Each point is colored with its (almost) optimal cluster label. At each node, we choose a split with a minimal number of mistakes. We stop if a node contains only one node. Thus, in the end, we stop where each of the $k=5$ centers is in its own leaf. 
<center>
<img src="/assets/2020-06-06/imm_example_slow.gif" width="600" height="320" />
</center>

### What's next
1. Check our [code](https://github.com/navefr/ExKMC).
2. Read our other posts... 