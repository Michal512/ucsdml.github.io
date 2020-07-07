---
layout: post
mathjax: true
title:  "Explainable 2-means Clustering: Five Lines Proof"
date:   2020-07-04 10:00:00 -0700
categories: jekyll update
tags: explainable
author: bla
excerpt: We will show <em>why</em> only one feature is enough to define a good 2-means clustering. And we will do it using only 5 inequalities (!)
---

**TL;DR:** we will show *why* only one feature is enough to define a good 2-means clustering. And we will do it using only 5 inequalities (!)

### Explainable clustering 
In a previous post we discussed why explainability is important, defined it as a small decision tree, and suggested an algorithm to find such a clustering. But why is it even possible?? Let's focus on the 2-means problem, where there are only two clusters and the cost of each clustering C, cost(C), is defined as the sum of the Euclidean distances squared of each point to its center. For those who like to see their definitions in math 
\begin{equation}
 cost(C)=\sum_x \\|x-c(x)\\|^2, 
\end{equation} the sum is over all points in the dataset and $c(x)$ is the closest center to x.

 In this post we want to show that for every dataset there is **one** feature $i$ and **one** threshold $\theta$ such that the following simple clustering $C'=(C'_1,C'_2)$ is a good one:
 \begin{equation}
 \text{if } x_i\leq\theta \text{ then } x\in C'_1 \text{ else } x\in C'_2.
\end{equation}
We call such a clustering a *threshold cut*. There might be many threshold cuts that are good, bad, or somewhere in between. We want to show that there is at least one that is good. In the paper we prove that there is always a threshold cut, $C'$, that is almost as good as the optimal clustering:
\begin{equation}
 cost(C')\leq4\cdot cost(opt),
\end{equation} 
where $cost(opt)$ is the cost of the optimal clustering. This means that there is a simple explainable clustering $C'$ that is only $4$ times worse than the optimal one. Sounds crazy, right? Let's see how we can prove it!

### The minimal-changes threshold cut
We want to compare two clusterings: the optimal clustering and the best threshold cut. The best threshold cut  is hard to analyze, so we introduce an intermediate clustering: *the minimal-changes threshold cut*, $C{''}$. Even though this clustering will not be the best threshold cut, it will be good enough, we will be able to prove that $cost(C'')$ will be at most $4cost(opt)$. In this post we will show a slightly worse bound of $11cost(opt)$ instead of $4cost(opt)$.
Let's define what the minimal-changes cut is. For two clusterings $C$ and $C'$, we define the number of changes as the minimal number of points that their cluster assignments needs to be changed to get from clustering $C$ to $C'$. The *minimal-changes clustering* is the threshold cut that has the minimal changes compared to the optimal clustering.  Take a look at the next figure for an example.


{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/IMM_blog_pic_1.png" width="40%" style="margin: 0 auto">
  <figcaption>
    Data set contains $16$ points. The optimal $2$-means cluster is shown with circles and stars. The vertical cut has $1$ change (the orange-circle point) and this cluster is represented with orange and blue. This is a minimal-changes threshold cut, as any threshold cut has at least $1$ change. Note  that the optimal centers are different form the centers defined by the minimal-changes threshold cut.
  </figcaption>
</figure>
{:refdef}

To prove that the minimal-changes threshold cut gives a good clustering we will do something that might look strange at first: we analyse the quality of this clustering with the optimal centers for the unexaplainable clustering C. One might except that the centers we use are the optimal centers for the minimal-changes threshold cut. This step will only increase the cost, so why are we doing it - because it will ease our analysis. Also, if there are not a lot of changes, then the centers do not change much, so it's not much of an increase.

### Playing with cost: warm-up
Before we present the proof, let's familiarize ourselves with the k-means cost and explore several of its properties. It will be helpful later on!  
#### Changing centers 
If we change the centers of a clustering from their means (which are their optimal centers) to different centers $c=(c_1, c_2)$, then the cost can only increase. Putting this into math, denote by $cost(C,c)$ the cost of clustering $C=(C_1,C_2)$ when $c_1$ is the center of cluster $C_1$ and $c_2$ is the center of cluster $C_2$, then 

\begin{equation}
 cost(C')\leq cost(C,c),
\end{equation} 
What if we further want to change the centers from some arbitrary centers (c¹, c²) to other arbitrary centers (m¹, m²)? How the cost changes? Can we bound it? For our rescue comes the (almost) triangle inequality that states that for any two vectors $x,y$: 
\begin{equation}
\\|x+y\\|^2=\sum_x 2\\|x\\|^2+2\\|y\\|^2, 
\end{equation}
This implies that the cost of changing the centers from c=(c¹, c²) to m=(m¹, m²) is bounded by
\begin{equation}
 cost(C,c)\leq 2cost(C,m)+2|C_1|\\|c_1-m_1\\|^2+2|C_2|\\|c_2-m_2\\|^2,
\end{equation} 

#### Decomposing the cost
The cost can be easily decomposed both with the data points and with the features. With the data points-for any partition of the points in C to S₁ and S₂, we are summing over all the points in the dataset, so the cost can be rewritten as
\begin{equation}
cost(C,c)=cost(S_1,c)+cost(S_2,c)
\end{equation}
The cost can also be decomposed with the features because we are using the Euclidean distance. To be more specific, if we define the cost induced by the $i$-th feature as $ cost\_i(C,c)=\sum\_i(x\_i-c(x)\_i)^2, $ then

\begin{equation}
 cost(C,c)=\sum_i cost_i(C,c),
\end{equation} 
If the last equation is unclear just recall the definition of the cost ($c(x$) is the center of a point $x$):
\begin{equation}
cost(C,c)=\sum\_{x\in C}\\|x-c(x)\\|^2=\sum\_i\sum\_{x\in C}(x\_i-c(x)\_i)^2=\sum\_icost\_i(C,c)
\end{equation}

### The 5-line proof
Now we are ready to prove that the threshold cut is only a constant factor worse than the optimal 2-means clustering. To make the proof as short as possible, we prove something a bit weaker: the minimal-changes threshold cut, C'', is at most 11(see the next section to see how me can make it into 4) times worse compared to the optimal:
\begin{equation}
cost(C'')\leq 11\cdot cost(opt)
\end{equation}
We first change the centers to the optimal centers c* - recall from the warm-up that this can only increase the cost:
\begin{equation}
cost(C'')\leq cost(C'',c^{\*}) \quad (1)
\end{equation}
Next we use one of the decomposition properties of the cost. We partition the dataset into the set of points that are correctly labeled, $X^{cor}$, and those that are not, $X^{wro}$.

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/IMM_blog_pic_2.png" width="40%" style="margin: 0 auto">
  <figcaption>
    The same data set as before. $X^{wro}$ represents the point that is in the wrong class in the threshold cut. All other points have the same cluster assignment as the optimal clustering.
  </figcaption>
</figure>
{:refdef}

Thus, we can rewrite the last term as
\begin{equation}
cost(C'',c^{\*})=cost(X^{cor},c^{\*})+cost(X^{wro},c^{\*}) \quad (2)
\end{equation}

The first term in (2) is easy to bound: it's at most cost(opt). So we focus from now on on the second term.

Let's change the centers once more, so that $X^{wro}$ will have the correct centers. The correct centers of $X^{wro}$ is the same centers $c^\*$, but the order is different. 
Using the decomposition property we discussed earlier, the second term in (2) is at most

\begin{equation}
2cost(opt)+2|X^{wro}|\cdot\\|c^{\*}\_1-c^{\*}\_2\\|^2 \quad (3)
\end{equation}

Now we reached the main step in the proof. We show that the second term in (3) is bounded by $8cost(opt)$. We first decompose $cost(opt)$ using the features. Then, all we need to show is that:

\begin{equation}
cost_i(opt)\geq\left(\frac{|c^{1}\_{1,i}-c^{1}\_{2,i}|}{2}\right)^2|X^{wro}| \quad (4)
\end{equation}

The trick is, for each feature, to focus on the threshold cut defined by the middle point between the two optimal centers. Since $C''$ is the minimal-changes clustering we know that in every threshold cut there are at least $\|X^{wro}\|$ mistakes.  Each mistake contributes at least half the distance between the two centers.

{:refdef: style="text-align: center;"}
<figure class="image">
  <img src="/assets/2020-06-06/IMM_blog_pic_3.png" width="40%" style="margin: 0 auto">
  <figcaption>
    Proving step $4.$
  </figcaption>
</figure>
{:refdef}

Summing everything together we achieve our goal:
\begin{equation}
 cost(C')\leq1 1\cdot cost(opt) \quad (5)
\end{equation} 
That's it!

### Epilogue: improvements
The bound that we got, $11$, is not the best possible. With more tricks we can get a bound of $4$. One of them is using Hall's theorem. Similar ideas provide a $2$-approximation to the optimal $2$-medians clustering as well.
To complement our upper bounds, we also prove lower bounds showing that any threshold cut must incur a (roughly) $3$-approximation for $2$-means or $2$-approximation for $2$-medians