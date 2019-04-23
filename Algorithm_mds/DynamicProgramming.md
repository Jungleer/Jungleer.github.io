# Dynamic Programming

[TOC]

### Introduce

​	What is Dynamic Programming(DP)? In Baidu Encyclopedia, it is a branch of operations research and a mathematical method to solve the optimization of decision process. Yeah, it's alright. But in this charpter, it is mainly some problem which have Most Substructure Properties. Not only like this, they have something characters in common like:

- If you use   former recorsion, you may caculate some problem in duplicate. And also, the asymptotic time complexity may reach 2^n.
- May use some table to remember some record.
-  Have Most Substructure Properties

​		And most remarkble characteristic of Dynamic Programming is that the problem have most substructure properties. A former recorsion which have most substructure properties means that the complexity of it may declined from O(2^n) to O(n^3) or O(n^2) or even O(nlgn).

​		And how we judge a problem have the most substructure property? Here is some example.

​		**First example: Cutting of Steel Bar**

​		***Given a section of steel bar with length N and a price list PI (i from I to n), the scheme of cutting steel bar is obtained to maximize the sales revenue rn***

​		*The table of cutting steel bar:*

| length   i | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| value pi   | 1    | 5    | 8    | 9    | 10   | 17   | 17   | 20   | 24   | 30   |

​		**Second example: Matrix Continuous Multiplication**

​		***Give you  a string of Matirx (A1  to An), to find a most less computational way to caculate its product***

​		*e.g:*

​		We assume that there have a string of Matrix call <A1, A2, A3>, their scale are 10*100,  100 * 50, and 5 *50. If we caculate the product via ((A1A2)A3), the caculation scale will reach 7500, and if we caculate the product via (A1(A2,A3)), the caculation scale will reach 75000. Obviously, the first kind of caculating method is more efficient than the second one.

​		Why is that so? because it have most substructure property!!

​		If a problem can devide into two or three small part, we call it sub problem. The optinum solution of a problem depend on its sub problem , and then the sub problem can solve like the main problem, that means this problem have most substructure property!



---

### How to solve it?

 		It's the topic of this chapter. For the first example, we konw that what we want to caculate is that:

​		 If we cut the bar into two part, one is i part(length == i) and n-i part. The the rn = ri + r(n-i). but the bar can be cut into many part. So the rn=Max(pn,r1+r(n-1),r2+r(n-2),...,r(n-1)+r1). And it's easy for us to write a recorsion to solve it. The pseudo code of this program is like this:

```pseudocode
cut-rod(p,n)
if n==0 
	return 0
q =  - Int.Max
for i=1 to n:
	q=max(q,p[i]+cut-rod(p,n-i));
return q;

```

​		But it is inefficiency!

​		Why? Look at the formula of this problem.
$$
T(n)=1+\sum_{i=1}^{n-1}{T(n-i)}
$$
​		We can feel that it is a O(2^n). Look the derivation process:

```pseudocode
T(1)=1
T(2)=1+T(1)
T(3)=1+T(1)+T(2)=2+2*T(1)
T(4)=1+T(1)+T(2)+T(3)=1+T(1)+(1+T(1))+(2+2*T(1))=4+4*T(1)
so,T(n)=n+2^n*T(1)
```
​		Maybe you know it, but don't know the recursion for certain. So,I give a graph to show how it work when n = 4.

![1555757569880](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1555757569880.png)



​		Sorry , I do not know how to draw a beautiful graph, but I think it is just-so-so, not too bad!

​		We observe the graph, we can see that we caculate most of the points in duplicate! The 0 Node already caculate 8 times! What we want caculate is the direct link between the father node and its son node. So, if we merge the graph(It's a tree in DataStructure) into a graph(a kind of DataStructure).And the link number of it is 
$$
n=(n-1)+(n-2)+...+1=\sum_{i=1}^{n-1}{i}
$$
​		Obviously, the complexity of it is O(n^2)!

​		And then, what we should do is to remember the result we have caculated! It is easy for us to use a array to memory it!

```pseudocode
main()
{
    r[0...n] = a new array;
    for i = 0 to n:
    	r[i]=- Int.MaxValue;
    cut-rod(p,n,r);
}

cut-rod(p,n,r)
if n==0 
	return 0
q =  - Int.Max
for i=1 to n:
	singleone=r[n-i];
	if singleone<0:
		q=max(q,p[i]+cut-rod(p,n-i));
	else
		q=max(q,p[i]+singleone);
return q;

```

​		Now we know that it is a tree structure, and if we need r[n] ,we need r[n-1], we need r[n-2], ... , we need  r[1]. So why don't we caculate it from r[1] to r[n]?

```pseudocode
cut-rod(p,n):
	r[0...n] = a new array;
	for j=1 to n:
		q = -Int.MaxValue;
		for i=1 to j:
			q=Max(q,p[i]+r[j-i]);
		r[j]=q;
```

​		*summary:*

​		Here, we always names the first kind method as **'from top to buttom with a memorandum method'**  (hereinafter referred to as "TTB"),here I and the second one is called as **'from buttom to top'**; (hereinafter referred to as "BTT")

​		In this example, you can see if we want to know the r[n], we should caculate the whole array r[0...n]. So, the BTT method is  a little fast than TTB. But in some case, we do not need to caculate the whole array, install, if you are lucky, you may just caculate one times or twice times, or just a part of the whole array, and in this situation, the TTB method is working better than BTT.



---

### Evolution---Matrix Continuous Multiplication

​		We have already know that matrix continuous multiplication have the most substructure property. And what we do now is to use this property to solve the main problem by cutting the matrix into parts!

​		The problem now is how to cutting it? Well, Let's cut it into two part in the k-index place. And here we use a two dimentional array m[i,j] to remember the min num of cacuation of the substring (Ai  to Aj).Now we can easyly know that:
$$
m[i,j]=\begin{cases} 0，i=j\\ \min_{i\leq k \leq j}{m[i,k]+m[k+1,j]+p_{i-1}p_kp_j}， i<j\end{cases}
$$
​		If we use BTT method, we can estimate how much for-loop we need.

​		Look at the second formula, there are three varible, i, j and k. So we estimate that we may need three for-loop! The code may be like this:

```pseudocode
for i = 1 to n:
	for j = i to n:
		m[i,j]=Int.MaxValue;
		for k = i to j:
		//Do some caculation here...
```

​		Now , let us finish the program:

```pseudocode
for i = 1 to n:
	m[i,i] = 0;

for i = 1 to n:
	for j = i to n:
		m[i,j]=Int.MaxValue;
		for k = i to j:
		q = m[i,k]+m[k+1,j]+p(i-1)pkpj
		if q<m[i,j]
		m[i,j]=q
		s[i,j]=k
		
```

​		**Pay attention here:**

​		1.Here we explain the p array. It is a array to store the row and col of a matrix;

​		2.The row of Ai is recorded in p[i-1] place, column of Ai is recorded in p[i] place so that make sure the column of Ai equal to row A(i+1);

​		3.The size of matrix A(i..j)(product from Ai to Aj) is p(i-1)*p(j);

​		4.So product A(i..k) and A(k+1...j) need anther caculation p(i-1)pkpj;

​		5.s[i,j] here is used to recored the best cutting place of (Ai....Aj)



---

### Some associated Problem

- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

- [Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

- [Edit Distance](https://leetcode.com/problems/edit-distance/)

- [Longest Common Subsequence](https://en.wikipedia.org/wiki/Longest_common_subsequence_problem)

  ​												

------

**Reference resources:**

- Thomas H.Corme,Charles E.Leiserson,Ronald L.Rivest,etc.Introduction to Algorithms, Third Edition[M].机械工业出版社:北京市,2013:204-236
- [Wikipedia](https://en.wikipedia.org/wiki/Longest_common_subsequence_problem)
- [Jianshu blog](https://www.jianshu.com/p/866c0dd53619)
- Other problem in leetcode(already show in Some associated Problem)





---

### 																						   Thanks for reading.

### 													Contack with me if there have problem:

​																																				emial:271669603@qq.com