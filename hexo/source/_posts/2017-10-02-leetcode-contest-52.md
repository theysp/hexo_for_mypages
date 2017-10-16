---
title: 身在法国凌晨起不来只能之后慢慢做题解馋的leetcode竞赛
date: 2017-10-02 10:40:08
categories: "技术积累" #文章分類目錄 可以省略
tags:
    - leetcode
---

leetcode上写代码最激动人心的一刻应该是提交后看到一个绿色的accept吧，啊哈哈。

这次的4题难度不小，以我的水平解出四题已经超过2小时了~~。


1. 第一题是送分题，不说了, 虽然是用python写的，但完全是C++的样子，代码如下：

``` python
class Solution(object):
    def repeatedStringMatch(self, A, B):
        """
        :type A: str
        :type B: str
        :rtype: int
        """
        ret = 0
        #if B is substring of A
        if A.find(B) >=0:
            return 1
        #if a is substring of b
        elif B.find(A) >=0:
            tmpi = B.find(A)
            ret = 0
            if tmpi>0:
                ret += 1
                if B[:tmpi] != A[-tmpi:]:
                    return -1
            tr = (len(B)-tmpi)%len(A)
            tc = int((len(B)-tmpi)/len(A))
            #print("tr{} tc{}".format(tr,tc))
            ret += tc
            for i in range(tc):
                if B[tmpi+i*len(A):tmpi+i*len(A)+len(A)] != A:
                    return -1
            if tr > 0:
                ret += 1
                #print(B[-tr:])
                if A.find(B[-tr:]) != 0:
                    return -1
            return ret
        #if a is not substring of b
        elif len(B)<len(A)*2:
            #find the begin part
            start = max(1,len(B)-len(A)+1)
            #print(start)
            for i in range(start,len(B)):
                if A[-i:] == B[:i] and A[:len(B)-i] == B[i:]:
                    return 2
        return -1
```

2. 第二题主要的思路是最长的长度就是一个节点左边最长的同值链长度加上右边最长的同值链长度，使用动态规划算法深度优先递归求得各节点向下最长的长度，然后供上层节点调用即可。
这里，用了lendown map来缓存子节点的最长向下长度。

``` c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
#include <map>
using namespace std;

class Solution {
public:
    map<TreeNode *,int> lendown;
    map<int, int> maxlen;
    
    int longestDown(TreeNode *node){
        if(lendown.find(node) != lendown.end())
            return lendown[node];
        int lenl = 0;
        int lenr = 0;
        if(node->right && node->right->val == node->val){
            lenr = 1 + longestDown(node->right);
        }
        if(node->left && node->left->val == node->val){
            lenl = 1+longestDown(node->left);
        }
        if(maxlen.find(node->val)==maxlen.end())
            maxlen[node->val]=0;
        maxlen[node->val]=max(maxlen[node->val],lenl+lenr);
        lendown[node] = max(lenl,lenr);
        return lendown[node];
    }
        
    int longestUnivaluePath(TreeNode* root) {
        vector<TreeNode *> q;
        if(root)
            q.push_back(root);
        int ret = 0;
        while(q.size()>0){
            TreeNode *node = q.back();
            q.pop_back();
            longestDown(node);
            ret = max(maxlen[node->val],ret);
            if(node->right)
                q.push_back(node->right);
            if(node->left)
                q.push_back(node->left);
        }
        return ret;
    }
};
```

3. 第三题一开始看到概率，小数点我就头疼了。但是其实这题还是动态规划的题。设f(i,j,k)为棋盘上第（i,j）格子k步内留在棋盘上的概率。则设k步后的概率为8个方向各走一步后，再走k-1步后概率的平均。

``` c++
class Solution {
public:
    double *dp;
    int myN;
    int myK;
    int mydpWidth;
    
    double OutP(int i,int j, int k){
        if(dp[i*mydpWidth+j*(myK+1)+k]>=0.0)
            return dp[i*mydpWidth+j*(myK+1)+k];
        int ni,nj;
        double oc = 0.0;
        static int step[8][2] = {{-2,-1},{-2,1},{-1,2},{1,2},{2,1},{2,-1},{1,-2},{-1,-2}};
        for(int idx=0;idx<8;++idx)
        {
            ni=i+step[idx][0];
            nj=j+step[idx][1];
            if(ni>=myN || nj >=myN || ni<0 || nj<0){
                oc += 1.0;
            }
            else 
                oc += OutP(ni,nj, k-1);
        }

        dp[i*mydpWidth+j*(myK+1)+k] = oc/8.0;
        return dp[i*mydpWidth+j*(myK+1)+k];
    }
    
    double knightProbability(int N, int K, int r, int c) {
        dp = new double[N*N*(K+1)];  //possibility of i,j th cell go out by k steps  
        for(int i=0;i<N*N*(K+1);++i)
            dp[i] = -1.0;
        for(int i=0;i<N*N;++i)
            dp[i*(K+1)] = 0.0;
        myN = N;
        myK = K;
        mydpWidth = N*(K+1);
        return 1-OutP(r,c,K);
    }
};
```

4. 第4题咋一看头疼啊，其实头疼就是因为人脑算不过来啊，但是电脑可以让他记啊，分而治之，动态规划。又是动态规划。
可恨啊，写代码手生了，写写改改就过去一小时了。
要点有2个：1. 当前位置向后k个值相加的结果存到一个单独的数组中。2. 两个动态规划缓存，一个存储当前位置后，一个最大子序列的起始位置。另一个存储当前位置后，两个不重叠最大子序列的开始位置。

代码如下：

``` c++
class Solution {
public:
	vector<int> sumk;
	vector<int> dp2;
	vector<int> dp1;
	int k;

	int maxSum(int start) {
		if (dp1[start] >= 0) {
			return dp1[start];
		}
		int tmp = sumk[start];
		int maxnow = sumk[maxSum(start + 1)];
		if (maxnow > tmp)
			dp1[start] = dp1[start + 1];
		else
			dp1[start] = start;
		return dp1[start];
	}

	int maxSum2(int start) {
		if (dp2[start] >= 0)
			return dp2[start];
		int maxpost = sumk[maxSum2(start + 1)] + sumk[maxSum(dp2[start+1]+k)];
		int maxnow = sumk[start] + sumk[maxSum(start + k)];
		if(maxnow >= maxpost)
			dp2[start] = start;
		else
			dp2[start] = dp2[start + 1];
		return dp2[start];
	}

	vector<int> maxSumOfThreeSubarrays(vector<int>& nums, int k) {
		sumk.resize(nums.size() - k + 1);
		sumk[0] = 0;
		this->k = k;
		for (int i = 0; i<k; ++i) {
			sumk[0] += nums[i];
		}
		for (int i = 1; i<nums.size() - k + 1; ++i) {
			sumk[i] = sumk[i - 1] - nums[i - 1] + nums[i + k - 1];
			//cout<<i<<" "<<sumk[i]<<endl;
		}
		dp2.resize(sumk.size());
		dp1.resize(sumk.size());
		for (int i = 0; i<sumk.size(); ++i) {
			dp1[i] = -1;
			dp2[i] = -1;
		}
		dp1[sumk.size() - 1] = sumk.size() - 1;
		dp2[sumk.size() - 1 - k] = sumk.size() - 1 - k;
		//iteratively check
		vector<int> ret;
		ret.resize(3);
		int maxsum = 0;
		for (int i = nums.size() - k * 3; i >= 0; --i) {
			int tmp = sumk[i] + sumk[maxSum2(i + k)] + sumk[maxSum(maxSum2(i + k) + k)];
			if (tmp>=maxsum)
			{
				ret[0] = i;
				ret[1] = maxSum2(i + k);
				ret[2] = maxSum(maxSum2(i + k) + k);
				maxsum = tmp;
			}
		}
		return ret;
	}
};
```