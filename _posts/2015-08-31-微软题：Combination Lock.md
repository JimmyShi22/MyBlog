---
layout: default
title: 微软题：Combination Lock
comments: true
kind: OJ
---

链接：[hihocoder:week 61](http://hihocoder.com/contest/hiho61/problem/1)

###题目
时间限制:10000ms

单点时限:1000ms

内存限制:256MB
描述

![](http://media.hihocoder.com//problem_images/20141019/14136954837870.png)
![](http://media.hihocoder.com//problem_images/20141019/14136954928568.png)

Finally, you come to the interview room. You know that a Microsoft interviewer is in the room though the door is locked. There is a combination lock on the door. There are N rotators on the lock, each consists of 26 alphabetic characters, namely, 'A'-'Z'. You need to unlock the door to meet the interviewer inside. There is a note besides the lock, which shows the steps to unlock it.

Note: There are M steps totally; each step is one of the four kinds of operations shown below:

Type1: *CMD 1 i j X*: (i and j are integers, 1 <= i <= j <= N; X is a character, within 'A'-'Z')

>This is a sequence operation: turn the ith to the jth rotators to character X (the left most rotator is defined as the 1st rotator)

>For example: ABCDEFG => CMD 1 2 3 Z => A*ZZ*DEFG

Type2: *CMD 2 i j K*: (i, j, and K are all integers, 1 <= i <= j <= N)

>This is a sequence operation: turn the ith to the jth rotators up K times ( if character A is turned up once, it is B; if Z is turned up once, it is A now. )

>For example: ABCDEFG => CMD 2 2 3 1 => A*CD*DEFG

Type3: *CMD 3 K*: (K is an integer, 1 <= K <= N)

>This is a concatenation operation: move the K leftmost rotators to the rightmost end.

>For example: ABCDEFG => CMD 3 3 => DEFG*ABC*

Type4: *CMD 4 i j*(i, j are integers, 1 <= i <= j <= N):

>This is a recursive operation, which means:

>
```
If i > j:
	Do Nothing
Else:
	CMD 4 i+1 j
	CMD 2 i j 1
```

>For example: ABCDEFG => CMD 4 2 3 => A*CE*DEFG

####输入
1st line:  2 integers, N, M ( 1 <= N <= 50000, 1 <= M <= 50000 )

2nd line: a string of N characters, standing for the original status of the lock.

3rd ~ (3+M-1)th lines: each line contains a string, representing one step.

####输出
One line of N characters, showing the final status of the lock.

####提示
Come on! You need to do these operations as fast as possible.



####样例输入

```
7 4
ABCDEFG
CMD 1 2 5 C
CMD 2 3 7 4
CMD 3 3
CMD 4 1 7
```

####样例输出

```
HIMOFIN
```

###分析
本题是：环的思想+线段树。
具体解法参考:[hihocoder](http://hihocoder.com/discuss/question/2120)

本题在处理cmd == 3的情况的时候，不需要移动字符串，而是采用“环”的思想，直接实现，处理i，j的时候直接加上start，再mod N：

```c++
int i, j;
cin >> i >> j;
i = (i - 1 + start + N) % N;
j = (j - 1 + start + N) % N;
if (i > j)
{
	query4(i, N - 1, 1);
	query4(0, j, N - i + 1);
}
else
	query4(i, j, 1);
```

再这基础上，有两种解法，一种是普通的，不能AC。而另一种采用线段树，AC。

###解法1：普通解法，复杂度O(nm)


```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int N, M;
vector<char> cs;

inline void transf(char &c, int i)
{
	c = (c - 'A' + i + 26) % 26 + 'A';
}

void query1(int i, int j, int x)
{
	for (; i <= j; i++)
		cs[i] = x;
}

void query2(int i, int j, int k)
{
	for (; i <= j; i++)
		transf(cs[i], k);
}

void query4(int i, int j, int lev)
{
	for (; i <= j; i++)
		transf(cs[i], lev++);
}

int main()
{
	//freopen("t.txt", "r", stdin);
	cin >> N >> M;
	cs.resize(N, '#');
	for (int i = 0; i < N; i++)
		cin >> cs[i];

	string s;
	int start = 0, cmd;
	for (int m = 0; m < M; m++)
	{
		cin >> s >> cmd;
		if (cmd == 1)
		{
			int i, j;
			char x;
			cin >> i >> j >> x;
			i = (i - 1 + start + N) % N;
			j = (j - 1 + start + N) % N;
			if (i > j)
			{
				query1(i, N - 1, x);
				query1(0, j, x);
			}
			else
				query1(i, j, x);
		}
		else if (cmd == 2)
		{
			int i, j, k;
			cin >> i >> j >> k;
			i = (i - 1 + start + N) % N;
			j = (j - 1 + start + N) % N;
			if (i > j)
			{
				query2(i, N - 1, k);
				query2(0, j, k);
			}
			else
				query2(i, j, k);

		}
		else if (cmd == 3)
		{
			int in;
			cin >> in;
			start = (start + in + N) % N;
		}
		else if (cmd = 4)
		{
			int i, j;
			cin >> i >> j;
			i = (i - 1 + start + N) % N;
			j = (j - 1 + start + N) % N;
			if (i > j)
			{
				query4(i, N - 1, 1);
				query4(0, j, N - i + 1);
			}
			else
				query4(i, j, 1);

		}

	}

	for (int i = start; i < start + N; i++)
		cout << cs[i % N];

	return 0;
}

```

###解法2：线段树，复杂度O(mlogn)
调了很久才AC，原因：习惯不好，在写程序的时候，要把所有的都想清楚，在实现的时候，某个tag不产生作用，其它的与此相关的变量也需要清零，而
之前为了图一时的方便，没有清零，导致各种各样的错误。总之还是需要一个整体的思想，tag产生作用就把所有变量赋值，作用消失就清零。代码如下：

```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int N, M;

typedef struct Node{
	char c;
	int lt; //1 set, 2 level up, 4 increase up
	int lev, delta, l, r, m;
	Node *lc, *rc;
	Node(char c, int l, int r) : c(c), l(l), r(r), m((l + r) / 2), lt(0), lev(0), delta(0), lc(NULL), rc(NULL){};
};

Node *root;
vector<Node*> nodes;

void build(Node * &p, int l, int r)
{
	if (l == r)
		p = nodes[l];
	else
	{
		p = new Node('#', l, r);
		build(p->lc, l, (l + r) / 2);
		build(p->rc, (l + r) / 2 + 1, r);
	}
}

void query(int cmd, Node *root, int i, int j, char x, int lev, int delta)
{

	if (i == root->l && j == root->r)
	{
		if (cmd == 1)
		{
			root->lt = 1;
			root->c = x;
			root->lev = 0;
			root->delta = 0;
		}
		else if (cmd == 2 || cmd == 4)
		{
			if (root->lt != 4)
				root->lt = cmd;

			if (x != '#')
			{
				root->c = x;
				root->lev = lev;
				root->delta = delta;
			}
			else
			{
				root->lev += lev;
				if (cmd == 4)
					root->delta += delta;
			}

		}
		return;
	}

	if (root->lt != 0)
	{
		query(root->lt, root->lc, root->l, root->m, root->c, root->lev, root->delta);
		query(root->lt, root->rc, root->m + 1, root->r, root->c,
			root->lt == 4 ? root->lev + root->delta * (root->m - root->l + 1) : root->lev, root->delta);
		root->lt = 0;
		root->lev = 0;
		root->c = '#';
		root->delta = 0;
	}

	if (j <= root->m)
		query(cmd, root->lc, i, j, x, lev, delta);
	else if (i >= root->m + 1)
		query(cmd, root->rc, i, j, x, lev, delta);
	else
	{
		query(cmd, root->lc, i, root->m, x, lev, delta);
		query(cmd, root->rc, root->m + 1, j, x, cmd == 4 ? lev + delta * (root->m - i + 1) : lev, delta);
	}

}

void printc(Node *root, int i, int j)
{
	if (root->lt != 0 && root->l == i && root->r == j && root->c != '#')
	{
		for (; i <= j; i++, root->lev += root->lt == 4 ? root->delta : 0 )
			cout << 
			(char)(root->lt == 1 ? root->c : 
				(
				root->lt == 2 ? ((root->c - 'A' + root->lev + 26) % 26 + 'A') 
				: ((root->c - 'A' + root->lev + 26) % 26 + 'A')
				)
			);
		return;
	}

	if (root->lt == 0 && root->l == root->r)
	{
		cout << root->c;
		return;
	}
	
	if (root->lt != 0)
	{
		query(root->lt, root->lc, root->l, root->m, root->c, root->lev, root->delta);
		query(root->lt, root->rc, root->m + 1, root->r, root->c,
			root->lt == 4 ? root->lev + root->delta * (root->m - root->l + 1) : root->lev, root->delta);
		root->lt = 0;
		root->lev = 0;
		root->c = '#';
		root->delta = 0;
	}

	if (i > root->m)
		printc(root->rc, i, j);
	else if (j <= root->m)
		printc(root->lc, i, j);
	else
	{
		printc(root->lc, i, root->m);
		printc(root->rc, root->m + 1, j);
	}
}

int main()
{
	//freopen("t.txt", "r", stdin);
	cin >> N >> M;
	for (int i = 0; i < N; i++)
	{
		char c;
		cin >> c;
		nodes.push_back( new Node(c, i, i));
	}

	build(root, 0, N - 1);

	int start = 0;
	for (int n = 0; n < M; n++)
	{
		int cmd;
		string s;

		cin >> s >> cmd;
		if (cmd == 1)
		{
			int i, j; char x;
			cin >> i >> j >> x;
			i = (i - 1 + start + N) % N; j = (j - 1 + start + N) % N;

			if (i > j)
			{
				query(cmd, root, i, N - 1, x, 0, 0);
				query(cmd, root, 0, j, x, 0, 0);
			}
			else
				query(cmd, root, i, j, x, 0, 0);
		}
		else if (cmd == 2)
		{
			int i, j, k;
			cin >> i >> j >> k;
			i = (i - 1 + start + N) % N; j = (j - 1 + start + N) % N;

			if (i > j)
			{
				query(cmd, root, i, N - 1, '#', k, 0);
				query(cmd, root, 0, j, '#', k, 0);
			}
			else
				query(cmd, root, i, j, '#', k, 0);
		}
		else if (cmd == 3)
		{
			int in;
			cin >> in;
			start = (start + in + N) % N;;
		}
		else if (cmd == 4)
		{
			int i, j;
			cin >> i >> j;
			i = (i - 1 + start + N) % N; j = (j - 1 + start + N) % N;

			if (i > j)
			{
				query(cmd, root, i, N - 1, '#', 1, 1);
				query(cmd, root, 0, j, '#', 1 + N - i, 1);
			}
			else
				query(cmd, root, i, j, '#', 1, 1);
		}

	}

	if (start == 0)
		printc(root, 0, N - 1);
	else
	{
		printc(root, start, N - 1);
		printc(root, 0, start - 1);
	}

	return 0;
}

```


