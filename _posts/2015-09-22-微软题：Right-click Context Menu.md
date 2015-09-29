---
layout: default
title: 微软题：Right-click Context Menu
comments: true
kind: OJ
---

题目链接：[Right-click Context Menu](http://hihocoder.com/contest/hiho64/problem/1)

##分析
本题是树形的贪心。采用dfs序，每个顶点依次贪心算得最优解。难点在于贪心策略的选取。还有数据结构怎么建，有点繁琐，有点绕人。

###数据结构
每个Row结构中存所有“子Sec”结构，Sec结构存其所包括的Row结构。看代码：

```c++
typedef struct Row;

typedef struct Sec{
	int w, sh;
	vector<Row*> rson;
	Sec() :w(0), sh(0){};
};

typedef struct Row{
	int id, h;
	vector<Sec*> sson;
	Row(int id) :id(id), h(0){};
};

vector<Row*> rows;
```

在Sec结构中，w表示Sec中Row的个数，sh表示Sec中所有的Row重新排序，考虑到每个Row的子Sec，能够达到的最低的高度。通过贪心策略获得。

在Row结构中，h表示Row中所有Sec重新排序，达到的最低高度。

###贪心策略
Sec中Row的最优：Row的孩子所占用的空间，进行从上到下，从大到小排序。

Row中Sec最优：求Sec的sh - w的大小，进行从上到下，从大到小排序。

上代码：

```C++
bool rcmp(Row *a, Row *b){ return a->h > b->h; }
bool scmp(Sec *a, Sec *b){ return (a->sh - a->w) > (b->sh - b->w); }
```

###dfs递归时
以Row为递归对象。依次遍历所有Row中的Sec，Sec中所有的Row。

之后根据贪心策略，遍历每个Sec的子Row，进行排序，求得每Sec的sh。

再根据贪心策略，遍历Row的每个子Sec，求得Row的h。

上代码：

```c++
void dfs(Row* root)
{
	for (int i = 0; i < root->sson.size(); i++)
	{
		Sec *sec = root->sson[i];
		for (int j = 0; j < sec->rson.size(); j++)
			dfs(sec->rson[j]);
		root->h += sec->rson.size();
	}

	for (int i = 0; i < root->sson.size(); i++)
	{
		Sec *sec = root->sson[i];
		sort(sec->rson.begin(), sec->rson.end(), rcmp);
		sec->w = sec->rson.size();
		for (int j = 0; j < sec->rson.size(); j++)
		{
			int h = sec->rson[j]->h + j;
			sec->sh = max(sec->sh, h);
		}
	}

	sort(root->sson.begin(), root->sson.end(), scmp);

	int lev = 0;
	for (int i = 0; i < root->sson.size(); i++)
	{
		Sec *sec = root->sson[i];
		int h = lev + sec->sh;
		root->h = max(root->h, h);
		lev += sec->w;
	}
}

```

###绕人的输入
直接上代码：

```C++

int main()
{
	//freopen("t.txt", "r", stdin);
	cin >> N;
	for (int i = 0; i <= N; i++)
		rows.push_back(new Row(i));

	for (int i = 0; i <= N; i++)
	{
		int M;
		cin >> M;
		if (M != 0)
		{
			Sec *sec = new Sec();
			rows[i]->sson.push_back(sec);

			while (M > 0)
			{
				int id;
				cin >> id;
				if (id != 0)
				{
					sec->rson.push_back(rows[id]);
					M--;
				}
				else
				{
					sec = new Sec();
					rows[i]->sson.push_back(sec);
				}
			}
		}
	}

	dfs(rows[0]);

	cout << rows[0]->h;

	return 0;
}
```

估计数据没有卡人，一次就AC了，蛮开心。


