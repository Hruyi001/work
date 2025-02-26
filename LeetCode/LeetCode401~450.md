### 437. 路径总和[**](https://leetcode.cn/problems/path-sum-iii/submissions/601742272/?envType=study-plan-v2&envId=top-100-liked)
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    unordered_map<long long,int> mp;
    int res;
    // 前缀和 + 哈希
    void dfs(TreeNode *root,  int targetSum, long long sum) {
         if(!root) return;
         sum += root->val; 
        //  先序路径和targetSum出现次数等于sum-targetSum的出现次数
         res += mp[sum - targetSum];
        //  计算完再统计
        //  记录前缀和出现的次数
         mp[sum]++;
        dfs(root->left, targetSum, sum);
        // 恢复现场
        dfs(root->right, targetSum, sum); 
        mp[sum]--;
    }
    int pathSum(TreeNode* root, int targetSum) {
        mp[0] = 1;
        dfs(root, (long)targetSum, 0);
        return res;
    }
};
```