---
layout: default
title: leetcode First Missing Positive
comments: true
kind: OJ
---

##leetcode-41 First Missing Positive

链接：https://leetcode.com/problems/first-missing-positive/

Given an unsorted integer array, find the first missing positive integer.

For example,

Given [1,2,0] return 3,

and [3,4,-1,1] return 2.

Your algorithm should run in O(n) time and uses constant space.

###分析
本题要求时间复杂度O(n), 并且没有额外空间。可以这样做，遍历整个数组，将当前位置i的数x与位置x的数进行交换，
若当前位置的数仍然不等于i，就while一直交换下去，直到为i为止，或者当前位置的数x大于等于数组长度（要找到的数肯定小于x，所以此数可以视为和负数是一样的）或为负数为止。
遍历之后，期望成为一个从0开始依次递增的数组，若其中某一位不正确，则这一位就是答案。

由于在遍历i时候，每一次计算，都会至少会有一个数回到它正确的位置上，所以复杂度是接近于O(n)的。

需要注意的是，测试数据如1， 2， 3时，由于3大于等于数组长度，但实际上3是需要检查的，所以在开始时，先push_back一个负数，增加数组长度。

还要注意的是，测试数据中居然有重复数，如2,2,1，本人在此处被坑了，所以在while处加上条件nums[i] != nums[nums[i]]，避免死循环，
两个相同的数，只需要一个回到其正确的位置上就好。

while的作用是：确保了所有非负的数都能回到其正确的位置上，如例子 1，2，3， while不能改成if


##实现代码

```c++
class Solution {
public:
	void swap(int &a, int &b){ a ^= b; b ^= a; a ^= b; }

	int firstMissingPositive(vector<int>& nums) {
		nums.push_back(-1);
		for (int i = 0; i < nums.size(); i++)
		{
			while (nums[i] < nums.size() && nums[i] != i 
			&& nums[i] >= 0 && nums[i] != nums[nums[i]])
			{
				swap(nums[i], nums[nums[i]]);
			}
		}

		int j = 1;
		for (; j < nums.size(); j++)
		{
			if (nums[j] != j)
				return j;
		}

		return j;
	}
};
```



