---
layout: default
title: leetcode Edit Distance
comments: true
---
题目链接：
https://leetcode.com/problems/edit-distance/

Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

a) Insert a character

b) Delete a character

c) Replace a character

本题与是标准的DP，与最长子串，最长子序列类似。

##状态转移公式

以word1为y轴，word2为x轴建表。dp[y][x]表示word1[ <= y] 与 word2[ <= x]的最小Edit Distance。

dp[y][x] = min(a, b, c);

其中：

a = dp[y][x - 1] + 1 表示当前word1[y]在x 之前已经考虑，当前的只是单纯的添加word2[x]使得其相同（insert过程），所以为之前的情况 + 1。

b = dp[y - 1][x - 1] + (word2[x] != word1[y]) 若两个字母不相同，表示replace过程，若相同则直接相等， step不变。

c = dp[y - 1][x] + 1 表示delete过程。

##实现
实现的时候有细节需要注意，超出数组范围的情况:dp[-1][x] = x + 1; dp[y][-1] = y + 1; dp[-1][-1] = 0;

并且，在最后一列，增加一行NTL，表示word1考虑的字母能够考虑到word2最后的空字符（考虑"ab", "a"的情况，答案是1）。
而且，最后一个空字符不需要进行操作就能够匹配，所以a 变成 a = dp[y][x - 1]。在考虑完这些以后，写出代码：

```c++
class Solution {
public:
	int min(int a, int b){ return a < b ? a : b; }

	int minDistance(string word1, string word2) {
		if (word1 == word2)
			return 0;
		int xsize = word2.length() + 1, ysize = word1.length();

		if (xsize - 1 == 0)
			return ysize;
		else if (ysize == 0)
			return xsize - 1;
		vector<vector<int>> dp(ysize, vector<int>(xsize, 0));
		for (int y = 0; y < ysize; y++)
		for (int x = 0; x < xsize; x++)
		{
			int a = x == 0 ? y + 1 : dp[y][x - 1] + (x < xsize - 1),
				b = (y == 0 ? x : (x == 0 ? y : dp[y - 1][x - 1])) + (x >= xsize - 1 || word2[x] != word1[y]),
				c = y == 0 ? x + 2 : dp[y - 1][x] + 1;
			dp[y][x] = min(min(a, b), c);
		}
		return dp[ysize - 1][xsize - 1];
	}
};
```

