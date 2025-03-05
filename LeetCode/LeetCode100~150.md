### 100.相同的树[***](https://leetcode.cn/problems/same-tree/description/)
```cpp
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
    bool isSameTree(TreeNode* p, TreeNode* q) {  
        if(!p && !q) return true;
        //p和q同为空
        if(!p || !q) return false; 
        //p和q有一个为空，另一个不空
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right) && p->val == q->val;
        //p,q左子树相同，右子树也相同，根节点值也相同
    }
};
```

### 101.对称二叉树[***](https://leetcode.cn/problems/symmetric-tree/)
```cpp
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
    bool dfs(TreeNode *p, TreeNode *q) {
        if(!p && !q) return true;
        if(!p || !q) return false;
        //左子树的左孩子与右子树的右孩子相同，左子树的右孩子与右子树的左孩子相同，则对称
        return dfs(p->left, q->right) && dfs(p->right, q->left) && p->val == q->val;
    }
    bool isSymmetric(TreeNode* root) {  
        if(!root) return true;
        //检验左右子树是否对称
        return dfs(root->left, root->right);
    }
};
```
### 102.二叉树的层序遍历[***](https://leetcode.cn/problems/binary-tree-level-order-traversal/description/)
```cpp
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
    vector<vector<int>> levelOrder(TreeNode* root) {    
        vector<vector<int>> v;
        if(!root) return v;
        queue<TreeNode*> q;
        q.push(root);
        while(q.size()) {
            int n = q.size();
            vector<int> res;
            for(int i = 0; i < n; i++) {
                //取出当前层的节点，把下一层的节点加入到队中，实现层序遍历
                auto p = q.front();
                res.push_back(p->val);
                q.pop();
                if(p->left) q.push(p->left);
                if(p->right) q.push(p->right);
            }
            v.push_back(res);
        }
        return v;
    }
};
```
### 103.二叉树的锯齿形层序遍历[***](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/description/)
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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) { 
        vector<vector<int>> v;
        if(!root) return v;
        queue<TreeNode*> q;
        q.push(root);
        int cnt = 1;
        while(q.size()) {
            int n = q.size();
            vector<int> res;
            for(int i = 0; i < n; i++) {
                auto p = q.front();
                q.pop();
                res.push_back(p->val);
                if(p->left) q.push(p->left);
                if(p->right) q.push(p->right);
            }
            if(cnt % 2 == 0) reverse(res.begin(), res.end());
            v.push_back(res);
            cnt++;
        }
        return v;
    }
};
```
### 104.二叉树的最大深度[***](https://leetcode.cn/problems/maximum-depth-of-binary-tree/description/)
```cpp
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
    int maxDepth(TreeNode* root) { 
        if(!root) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```
### 105.从前序与中序遍历序列构造二叉树[****](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/)
```cpp
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
private:
    unordered_map<int,int>p;
public:    
    TreeNode* dfs(vector<int>& pre, vector<int>& in, int pl, int pr, int il, int ir) {
        if(pl > pr) return nullptr;
        TreeNode *root = new TreeNode(pre[pl]);
        //快速找到根节点在中序的位置
        int k = p[pre[pl]];
        //构建左子树， k - il是左子树区间长度
        root->left = dfs(pre, in, pl + 1, pl + k - il, il, k - 1);
        //构建右子树
        root->right = dfs(pre, in, pl + k - il + 1, pr, k + 1, ir);
        return root;
    }
    TreeNode* buildTree(vector<int>& pre, vector<int>& in) {   
        int n = pre.size();
        for(int i = 0; i < in.size(); i++) 
            p[in[i]] = i;
        return dfs(pre, in, 0, n - 1, 0, n - 1);
    }
};
```
### 106.从中序与后序遍历序列构造二叉树[****](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
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
private:
    unordered_map<int, int> mp;
public:   
    TreeNode* dfs(vector<int>& in, vector<int>& pos, int pl, int pr, int il ,int ir) {
        if(pl > pr) return nullptr;
        TreeNode *root = new TreeNode(pos[pr]);
        int k = mp[pos[pr]];
        root->left = dfs(in, pos, pl, pl + k - il - 1, il, k - 1);
        root->right = dfs(in, pos, pl + k - il, pr - 1, k + 1, ir);
        return root;
    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {   
        int n = inorder.size();
        for(int i = 0; i < n; i++)
            mp[inorder[i]] = i;
        return dfs(inorder , postorder, 0, n - 1, 0, n - 1);
    }
};
```
### 107.二叉树的层序遍历 II[***](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/description/)
```cpp
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
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> v;
        if(!root) return v;
        queue<TreeNode*> q;
        q.push(root);
        while(q.size()) {
            int n = q.size();
            vector<int> res;
            for(int i =  0; i < n; i++) {
                auto p = q.front();
                q.pop();
                res.push_back(p->val);
                if(p->left) q.push(p->left);
                if(p->right) q.push(p->right);
            }
            v.push_back(res);
        }
        reverse(v.begin(), v.end());
        return v;
    }
};
```
### 108.将有序数组转换为二叉搜索树[***](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)
```cpp
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
    //通过归并划分将有序数组转化为平衡二叉树
    TreeNode* dfs(vector<int>& nums, int l, int r) {
        if(l > r) return nullptr;
        int mid = l + r >> 1;
        TreeNode *root = new TreeNode(nums[mid]);
        root->left = dfs(nums, l, mid - 1);
        root->right = dfs(nums, mid + 1, r);
        return root;
    }
    TreeNode* sortedArrayToBST(vector<int>& nums) {  
        return dfs(nums, 0, nums.size() - 1);
    }
};
```
### 109.有序链表转化二叉搜索树[****](https://leetcode.cn/problems/convert-sorted-list-to-binary-search-tree/description/)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
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
    ListNode* getMid(ListNode *head, ListNode *tail) {
        ListNode *p = head, *q = head;
        while(q->next != tail && q->next->next != tail) {
            p = p->next;
            q = q->next->next;
        }
        return p;
    }
    TreeNode* merge(ListNode *head, ListNode *tail) {
        if(head == tail) return nullptr;
        //找到链表中点
        ListNode* mid = getMid(head, tail);
        //链表中点是平衡二叉树的根
        TreeNode *root = new TreeNode(mid->val);
        root->left = merge(head, mid);
        root->right = merge(mid->next, tail);
        return root;
    }
    TreeNode* sortedListToBST(ListNode* head) {   
        return merge(head, nullptr);
    }
};
```
### 110.平衡二叉树[***](https://leetcode.cn/problems/balanced-binary-tree/description/)
```cpp
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
    int getHeight(TreeNode* root) {
        if(!root) return 0;
        return max(getHeight(root->left), getHeight(root->right)) + 1;
    }
    bool isBalanced(TreeNode* root) { 
        if(!root) return true;
        //左子树要平衡且当前根的左右子树高度差<=1
        return isBalanced(root->left) && isBalanced(root->right) && abs(getHeight(root->left) - getHeight(root->right)) <= 1;
    }
};
```
### 111.二叉树的最小深度[***](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)
```cpp
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
    int minDepth(TreeNode* root) { 
        if(!root) return 0;
        //左子树为空
        if(!root->left) return minDepth(root->right) + 1;
        //右子树为空
        if(!root->right) return minDepth(root->left) + 1;
        return min(minDepth(root->left), minDepth(root->right)) + 1;
    }
};
```
### 112.路径综合[**]()
```cpp
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
    bool dfs(TreeNode* root, int k) { 
        if(!root) return false;
        k -= root->val;
        if(!root->left && !root->right) 
            return k == 0;
        //左子树或者右子树存在路径
        return dfs(root->left, k) || dfs(root->right, k);
    }
    bool hasPathSum(TreeNode* root, int targetSum) {  
        return dfs(root, targetSum);
    }
};
```
### 113.路径综合II [***](https://leetcode.cn/problems/path-sum-ii/description/)
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;s
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {  
private:
    vector<vector<int>> v;
public:  
    void dfs(TreeNode* root, int sum, vector<int> &res) {
        if(!root) return;
        sum -= root->val;
        res.push_back(root->val);
        if(!root->left && !root->right) {
            if(!sum) v.push_back(res);
            //恢复现场
            res.pop_back();
            return;
        }
        dfs(root->left, sum, res);
        dfs(root->right, sum, res);
        //恢复现场
        res.pop_back();
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {  
        vector<int> res;
        dfs(root, targetSum, res);
        return v;
    }
};
```
### 114.二叉树展开为链表[****](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)
```cpp
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
    void flatten(TreeNode* root) { 
        while(root) {
            auto p = root->left;
            //如果左子树存在
            if(p) {
                while(p->right) p = p->right;
                将左子树的右链加作为根的右子树
                p->right = root->right;
                root->right = root->left;
                //构成链表节点，左子树为空
                root->left = nullptr;
            }
            //更新到右子树
           root = root->right;
        }
    }
};
```
## 115.不同的子序列[****](https://leetcode.cn/problems/distinct-subsequences/)
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        //s,t前加空格
        s = ' ' + s, t = ' ' + t;
        //f数组行和列要加1, 使用unsigned来自动取模
        vector<vector<unsigned>> f(n + 1, vector<unsigned>(m + 1));
        //f[i][j]表示s[1~i]的所有和T[1~j]相等的子序列个数
        for(int i = 0; i <= n; i++) 
            f[i][0] = 1;

        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= m; j++) {
                //s[i]与t[j]不相等，不选s[i]
                f[i][j] = f[i - 1][j];
                //选s[i],此时方案数
                if(s[i] == t[j])   f[i][j] += f[i - 1][j - 1]; 
            }
        return f[n][m];
    }
};
```
### 116.填充每个节点的下一个右侧节点指针[****](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/description/)
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) { 
        if(!root) return NULL;
        //存储root
        auto source = root;
        while(root->left) {
            //遍历当前层，
            for(auto p = root; p; p = p->next) {
                //p的左孩子连接有孩子
                p->left->next = p->right;
                //p的右孩子连接p->next的左孩子
                if(p->next) p->right->next = p->next->left;
            }
            //遍历下一层
            root = root->left;
        }
        return source;
    }

};
```
### 117.填充每个节点的下一个右侧节点指针II[****](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/description/)
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        if(!root) return NULL;
        auto cur = root;
        while(cur) {
            //构建下一层
            auto head = new Node(-1);
            Node* tail = head;
            //遍历当前层的节点
            for(auto p = cur; p; p = p->next) {
                //有孩子，则把孩子加到tail后，更新tail
                if(p->left) tail = tail->next = p->left;
                if(p->right) tail = tail->next = p->right; 
            }
            //更新下一层
            cur = head->next;
        }
        return root;
    }
};
```

### 118. 杨辉三角[****](https://leetcode.cn/problems/pascals-triangle/description/)
```cpp
class Solution {
public:
    vector<vector<int>> generate(int n) {
        vector<vector<int>> f(n); 
        for(int i = 0; i < n; i++)  {
            f[i].resize(i + 1);
            f[i][0] = f[i][i] = 1; 
            for(int j = 1; j < i; j++) {
                f[i][j] = f[i - 1][j] + f[i - 1][j - 1]; 
            }
        }
        return f;
    }
};
```
### 119. 杨辉三角II[*****](https://leetcode.cn/problems/pascals-triangle-ii/description/)
```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) { 
        int n = rowIndex;
        vector<vector<int>> f(2, vector<int>(n + 1)); 
        //用滚动数组，来代替二维数组
        for(int i = 0; i <= n; i++)  { 
            f[i & 1][0] = f[i & 1][i] = 1;
            //当前行与上一行一定属于不同的行
            for(int j = 1; j <= i; j++)
                f[i & 1][j] = f[i - 1 & 1][j] + f[i - 1 & 1][j - 1];
        }
        return f[n & 1];
    }
};
```
### 120.三角形最小路径和[***](https://leetcode.cn/problems/triangle/)
```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& f) {
        int n = f.size();
        //从下往上选择较小的路径，最后f[0][0]的值为最小路径和
        for(int i = n - 2; i >= 0; i--) {
            for(int j = 0; j <= i; j++) 
                f[i][j] += min(f[i + 1][j], f[i + 1][j + 1]);
        }
        return f[0][0];
    }
};
```
### 121.买卖股票的最佳时机[***](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        for(int i = 0, tmp = INT_MAX; i < prices.size(); i++) {
            //找到所有各天股票减去前面最小值股票的那天中，找到最大值
            res = max(res, prices[i] - tmp);
            tmp = min(tmp, prices[i]);
        }
        return res;
    }
};
```
### 122.买卖股票的最佳时机II[***](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/)
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        //把所有下一天与当天股票差值为正的情况累加得到的利润即最大
        for(int i = 0; i < prices.size() - 1; i++) 
            res += max(0, prices[i + 1] - prices[i]);
        return res;
    }
};
```
### 123.买卖股票的最佳时机III[*****](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/description/)
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<int> f(n + 1);
        //f[i]表示前i天（包括第i)的第一笔交易最大利润，以第i天作为分段点，第i天后为第二笔交易
        for(int i = 1, minp = INT_MAX; i <= n; i++) {
            f[i] = max(f[i - 1], prices[i - 1] - minp);
            minp = min(minp, prices[i - 1]);
        }
        int res = 0;
        for(int i = n, maxp = 0; i; i--) {
            //总的最大利润为f[i]+第二笔交易的最大值
            res = max(res, f[i] + maxp - prices[i - 1]);
            maxp = max(maxp, prices[i - 1]);
        }
        return res;
    }
};
```
### 124.二叉树中的最大路径和[****](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)
```cpp
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
private:
    int res = INT_MIN;
public:
    //返回root为根中路径和最大值
    int dfs(TreeNode *root) {
        if(!root) return 0;
        //左子树和右子树中最大路径和
        int left = max(0, dfs(root->left)), right = max(0, dfs(root->right));
        //以root为核心的最大路径和
        res = max(res, root->val + left + right);
        return max(left, right) + root->val;
    }
    int maxPathSum(TreeNode* root) {
        dfs(root);
        return res;
    }

};
```
### 125.验证回文串[***](https://leetcode.cn/problems/valid-palindrome/description/)
```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        if(s.empty()) return false;
        for(int i = 0, j = s.size() - 1; i < j; i++, j--) {
            //双指针扫描，找到第一个数字字母的字符，然后比较
            while(i < j && !isalnum(s[i])) i++;
            while(i < j && !isalnum(s[j])) j--;  
            if(i < j && tolower(s[i]) != tolower(s[j])) return false; 
        }
        return true;
    }

};
```

### 126.单词接龙II[****](https://leetcode.cn/problems/word-ladder-ii/description/)
```cpp
class Solution {
public:
    unordered_map<string, int> dist;
    vector<vector<string>> v;
    vector<string> res;
    string beginWord;
    vector<vector<string>> findLadders(string _beginWord, string endWord, vector<string>& wordList) {
        beginWord = _beginWord;
        unordered_set<string> S;
        for(auto word : wordList) S.insert(word); 
        dist[beginWord] = 0;
        queue<string> q;
        q.push(beginWord);
        while(q.size()) {
            auto t = q.front();
            q.pop();
            string r = t;
            for(int i = 0; i < t.size(); i++) {
                t = r;
                //用26个字母依次替换t[i]
                for(char j = 'a'; j <= 'z'; j++) {
                    t[i] = j;
                    //t在集合中且没有被遍历过，则加入图中
                    if(S.count(t) && !dist.count(t)) {
                        dist[t] = dist[r] + 1;
                        //找到目标位置
                        if(t == endWord) 
                            break;
                        //加入图中
                        q.push(t);
                    }
                }
            }
        } 
        if(!dist[endWord]) return v;
        res.push_back(endWord);
        dfs(endWord);
        return v;
    }
    void dfs(string t) { 
        if(t == beginWord) {
            reverse(res.begin(), res.end());
            v.push_back(res);
            reverse(res.begin(), res.end());   
        }
        else {
            string r = t;
            for(int i = 0; i < t.size(); i++) {
                t = r;
                for(char j = 'a'; j < 'z'; j++) {
                    t[i] = j;
                    if(dist.count(t) && dist[r] == dist[t] + 1) {
                        res.push_back(t);
                        dfs(t);
                        res.pop_back();
                    }
                }
            } 
        }
    }
};
```

### 127.单词接龙[*****](https://leetcode.cn/problems/word-ladder/)
```cpp
class Solution {
public:
    //这是一个图的问题，dijkstra算法
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
         unordered_set<string> S;
        for(auto word : wordList) S.insert(word);
        unordered_map<string, int> dist;
        dist[beginWord] = 0;
        queue<string> q;
        q.push(beginWord);
        while(q.size()) {
            auto t = q.front();
            q.pop();
            string r = t;
            for(int i = 0; i < t.size(); i++) {
                t = r;
                //用26个字母依次替换t[i]
                for(char j = 'a'; j <= 'z'; j++) {
                    t[i] = j;
                    //t在集合中且没有被遍历过，则加入图中
                    if(S.count(t) && !dist.count(t)) {
                        dist[t] = dist[r] + 1;
                        //找到目标位置
                        if(t == endWord) 
                            return 1 + dist[t];
                        //加入图中
                        q.push(t);
                    }
                }
            }
        }
        return 0;
    }
};
```
### 128.最长连续序列[****](https://leetcode.cn/problems/longest-consecutive-sequence/)
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) { 
        unordered_set<int> st;
        for(auto x : nums) st.insert(x);
        int res = 0;
        for(auto x : nums) {
            // 挑选可以作为首个元素的位置，去查找
            if(!st.count(x - 1)) {
                int y = x; 
                while(st.count(y)) {
                    // 遍历完就删除，防止被重复使用，使得复杂度n*n
                    st.erase(y);
                    y++; 
                }
                res = max(res, y - x);
            }
        }
        return res;
    }
};
```
### 129.求根节点到叶子节点数字之和[***](https://leetcode.cn/problems/sum-root-to-leaf-numbers/description/)
```cpp
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
    int res = 0;
    void dfs(TreeNode *root, int k) {
        if(!root) return;
        k = 10 * k + root->val;
        if(!root->left && !root->right) {
            res += k;
            return;
        }
        dfs(root->left, k);
        dfs(root->right, k);
    }
    int sumNumbers(TreeNode* root) {
        dfs(root, 0);
        return res;
    }
};
```
### 130.被围绕的区域[****](https://leetcode.cn/problems/surrounded-regions/)
```cpp
class Solution {
public:
    int dr[4][2] = {{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
    int n, m;
    vector<vector<char>> board;
    void dfs(int x, int y) {
        board[x][y] = '#';
        for(int i = 0; i < 4; i++) {
            int u = x + dr[i][0], v = y + dr[i][1];
            if(u < 0 || u >= n || v < 0 || v >= m || board[u][v] != 'O') continue;
            dfs(u, v);
        }
    }
    void solve(vector<vector<char>>& _board) {
        board = _board;
        n = board.size();
        if(!n) return;
        m = board[0].size();
        //从边界O出发将所有经过的O进行标记
        for(int i = 0; i < n; i++) {
            if(board[i][0] == 'O')   dfs(i, 0);
            if(board[i][m - 1] == 'O')   dfs(i, m - 1);; 
        }
        for(int i = 0; i < m; i++) {
            if(board[0][i] == 'O')   dfs(0, i);
            if(board[n - 1][i] == 'O')   dfs(n - 1, i); 
        }
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++) {
                if(board[i][j] == '#') board[i][j] = 'O';
                else board[i][j] = 'X';
            }
        //复原board
        _board = board;
    }
};
```
### 131.分割回文串[****](https://leetcode.cn/problems/palindrome-partitioning/)
```cpp
class Solution {
public:
    vector<vector<string>> v;
    vector<string> res;
    vector<vector<bool>> f;
    void dfs(string s, int u) {
        if(u == s.size()) {
            v.push_back(res);
            return;
        }
        //[u,i],   将[u , n]分割成回文串 ，再将[i+1, n]分割成回文串
        for(int i = u; i < s.size(); i++) 
            if(f[u][i]) {
                res.push_back(s.substr(u, i - u + 1));
                dfs(s, i + 1);
                res.pop_back();
            }
    }
    vector<vector<string>> partition(string s) { 
        int n = s.size();
        f = vector<vector<bool>>(n, vector<bool>(n));
        //f[i][j]=true表示s中i到j的字符是回文串
        //先标记回文串，然后使用dfs找到s中的回文字串
        //要使用j-1，先for循环j，i到j，i不会超过j
        for(int j = 0; j < n; j++)
            for(int i = 0; i <= j; i++) {
                //一个字符
                if(i == j) f[i][j] = true; 
                else if(s[i] == s[j]) {
                    //两个相等的字符或者i+1到j-1是回文串
                    if(j - i < 2 || f[i + 1][j - 1]) 
                        f[i][j] = true;
                }
            }
        dfs(s, 0);
        return v;
    }
};
```
### 132.分割回文串II[*****](https://leetcode.cn/problems/palindrome-partitioning-ii/)
```cpp
class Solution {
public:
    int minCut(string s) {
        int n = s.size();
        s = " " + s;
        vector<vector<bool>> g(n + 1, vector<bool>(n + 1));
        vector<int> f(n + 1,2e8);
        //f[i][j]记录s的第i到第j是否能被分割
        for(int j = 1; j <= n; j++) 
            for(int i = 1; i <= j; i++) {
                if(i == j) g[i][j] = true;
                else if(s[i] == s[j]) {
                    if(j - i < 2 || g[i + 1][j - 1]) g[i][j] = true;
                }
            }
        f[0] = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= i; j++)
                if(g[j][i])
                //第j到第i可以被分割，则找到可以被分割的最小值
                    f[i] = min(f[i], f[j - 1] + 1);
        //分割次数是多了1
        return f[n] - 1;
    }
};
```
### 133.克隆图[****](https://leetcode.cn/problems/clone-graph/description/)
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

class Solution {
public:
    unordered_map<Node*, Node*> hash;
    void dfs(Node *node) {
        hash[node] = new Node(node->val);
        for(auto u : node->neighbors)
            if(!hash[u]) 
                dfs(u);
    }
    Node* cloneGraph(Node* node) {
        if(!node) return NULL;
        //复制所有的点
        dfs(node);
        //复制所有的边, s是原来顶点，d是克隆后的顶点
        for(auto [s, d] : hash)
            for(auto ver : s->neighbors) 
            //把原来顶点的相邻顶点边的关系复制到新图中
                d->neighbors.push_back(hash[ver]);
        return hash[node];
    }
};
```
### 134.加油站[****](https://leetcode.cn/problems/gas-station/description/)
```cpp
// 1. 如果start可以往后走n步，那就可以绕一圈，返回start即可
// 2. 如果start只能走step步（step<n)到某个点j，那么下一个起点只能从start+step+1开始尝试。从中间的某个点k开始也是不可能跨过j点的，因为从start可以到k点，说明start到k可以带大于等于0的油量过来。带了油过来都跨越不了j点，那以k为起点肯定更加跨越不了j点。
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        //贪心做法
        int n = gas.size();
        //从第i点出发，步伐是j,走一圈
        for(int i = 0; i < n; ) {
            int left = 0, j;
            for(j = 0; j < n; j++) {
                int k = (i + j) % n;
                left += gas[k] -cost[k];
                if(left < 0) break;
                //如果无法到达下一点，则从i+1~k-1出发都无法到达k+1点，直接退出循环
            }
            //遍历了一圈
            if(j == n) return i;
            i = i + j + 1;
        }
        return -1;
        //每个点最多被遍历两次，所以时间复杂度为O(n)
    }
};
```
### 135.分发糖果[****](https://leetcode.cn/problems/candy/description/)
```cpp
class Solution {
public:
    // f[i]表示第i个孩子至少需要多少糖果，由第i-1个孩子的糖果和第i+1个孩子的谈过转移而来
    vector<int> f;
    vector<int> w;
    int n;
    // 记忆化搜索，
    int dp(int x) {
        // 如果之前已经搜索过
        if(f[x] != -1) return f[x];
        // 第一次搜索，糖果初始化为1
        f[x] = 1;
        //左边的评分比它小，糖果至少+1
        if(x && w[x - 1] < w[x]) f[x] = max(f[x], dp(x - 1) + 1);
         //右边的评分比它小，糖果至少+1， x+1还没搜索到，用dp搜索
        if(x < n - 1 && w[x] > w[x + 1]) f[x] = max(f[x], dp(x + 1) + 1);
        return f[x];
    }
    int candy(vector<int>& ratings) {
        n = ratings.size();
        f.resize(n, -1);
        w = ratings;
        int res = 0;
        for(int i = 0; i < n; i++) res += dp(i);
        return res;
    }
};
```
### 136.只出现一次的数字[***](https://leetcode.cn/problems/single-number/description/?envType=study-plan-v2&envId=top-100-liked)
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for(auto x : nums)  {
            res ^= x;
        }
        return res;
    }
};
```
### 137.[***](https://leetcode.cn/problems/single-number-ii/description/)
```c++
class Solution {
public:
//状态机
    int singleNumber(vector<int>& nums) {
        int two = 0, one = 0;
        for(auto x : nums) {
            one = (one ^ x) & ~two;
            two = (two ^ x) & ~one;
        }
        return one;
    }
};
```
### 138.随机链表的复制[****](https://leetcode.cn/problems/copy-list-with-random-pointer/)
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {  
        if(!head) return head;
        //复制原链表的val
        for(Node *p = head, *t; p; p = p->next->next) {
            t = p->next;
            p->next = new Node(p->val);
            p->next->next = t;
        }
        //复制原链表的random
        for(Node *p = head; p; p = p->next->next) {
            if(p->random != NULL) p->next->random = p->random->next;
            else p->next->random = NULL;
        }
        Node *r = head->next; 
        for(Node *p = head; p; p = p->next) {
            Node *q = p->next;
            //把原链表连接起来
            p->next = p->next->next;  
            //把复制的链表连接起来
            q->next = q->next ? q->next->next : NULL;
        }
        //返回复制后的链表
        return r;
    }
};
```
### 139.单词划分[****](https://leetcode.cn/problems/word-break/description/)
```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        typedef unsigned long long ULL;   //64位
        const int P = 131;
        //使用ULL和P来查找字符串，快速将字符串转化为唯一的整数
        unordered_set<ULL> hash;
        for(auto &word : wordDict) {
            ULL h = 0;
            for(auto c : word) h = h * P + c;
            hash.insert(h);
        }
        int n = s.size();
        vector<int> f(n + 1);
        //f[i]表示1~i的字符可以被分成wordDict中的字符串
        f[0] = true;
        s = ' ' + s;
        for(int i = 0; i < n; i++) 
            if(f[i]) {
                ULL h = 0;
                //1~i符合，i+1~j也符合，则1~j也能有wordDict字符串组成
                for(int j = i + 1; j <= n; j++) {
                    h = h * P + s[j];
                    if(hash.count(h)) f[j] = true;
                }
            }
        return f[n];
    }
};
```
### 140.单词拆分 II[*****]()
```cpp
class Solution {
public:
    vector<string> res;
    unordered_set<string> hash;
    vector<bool> f;
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        //把wordDict单词哈希存储
        for(auto x : wordDict) hash.insert(x);
        int n = s.size();
        f.resize(n + 1);
        //f[i]表示i到n-1的字符串能有wordDict构成
        f[n] = true;
        for(int i = n - 1; ~i; i--)
            for(int  j = i; j < n; j++) 
            //i到j在wordDict中，j+1~n-1可以右wordDict单词组成
                if(hash.count(s.substr(i, j - i + 1)) && f[j + 1])
                    f[i] =  true;
        dfs(s, 0, "");
        return res;
    }
    void dfs(string s, int u, string path) {
        if(u == s.size()) {
            //最后多加了空格，需要删除
            path.pop_back();
            res.push_back(path);
            return;
        }
        //判断从u到i是否能构成wordDict的单词，同时i+1到n-1有也可以有wordDict中
        for(int i = u; i < s.size(); i++)
            if(hash.count(s.substr(u, i - u + 1)) && f[i + 1]) 
                dfs(s, i + 1, path + s.substr(u, i - u + 1) + ' ');
    }
}
```






### 141.环形链表[***](https://leetcode.cn/problems/linked-list-cycle/)
``` c++ 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {   
        ListNode *p = head, *q = head;
        //q和q->next不为空则一直循环下去
        while(q && q->next) {
            p = p->next; 
            q = q->next->next;
            //p和q相遇，则有环
            if(p == q) return true;
        }
        //退出上面循环，则无环
        return false;
    }
};
```
### 142.环形链表II [****](https://leetcode.cn/problems/linked-list-cycle-ii/)
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {    
        ListNode *p = head, *q = head;
        while(q && q->next) {
            p = p->next;
            q = q->next->next;
            //p走一步，q走两步，相遇时
            if(p == q) {
                //将p指向head
                p = head;
                //再相遇时，就是环的入口
                while(p != q) {
                    p = p->next;
                    q = q->next;
                }
                return p;
            }
        }
        return NULL;
    }
};
```

### 143.重排链表[****](https://leetcode.cn/problems/reorder-list/description/)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public: 
    ListNode* getMid(ListNode *head) {
        ListNode *p = head, *q = head;
        while(q->next && q->next->next) {
            p = p->next;
            q = q->next->next;
        }
        return p;
    }
    ListNode *reverseList(ListNode *head) {
        ListNode *pre = new ListNode();
        ListNode *p = head, *r;
        while(p) {
            r = p->next;
            p->next = pre->next;
            pre->next = p;
            p = r;
        }
        return pre->next;
    }
    void reorderList(ListNode* head) { 
        //找到中间节点
        ListNode *mid = getMid(head);
        //将后方链表旋转
        ListNode *right = reverseList(mid->next);
        mid->next = NULL;
        ListNode *p = head, *q = right;
        ListNode  *u, *v;
        while(p && q) {
            //将前半部分与后半部分逐个连接
            u = p->next;
            v = q->next;
            p->next = q; 
            //与下一轮进行连接
            q->next = u;
            p = u, q= v;
        } 
    }
};
```
### 144.二叉树的前序遍历[****](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)
```cpp
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
    vector<int> preorderTraversal(TreeNode* root) { 
        vector<int> v;
        if(!root) return v;
        stack<TreeNode*>stk;
        stk.push(root);
        while(stk.size()) {
            auto p = stk.top();
            stk.pop();
            v.push_back(p->val);
            //先存右子树，再存左子树，这样栈先根左子树、右子树实现先序
            if(p->right) stk.push(p->right);
            if(p->left) stk.push(p->left);
        }
        return v;
    }
};
```
### 145.后序遍历[****](https://leetcode.cn/problems/binary-tree-postorder-traversal/)
```cpp
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
    vector<int> postorderTraversal(TreeNode* root) { 
        vector<int> v;
        if(!root) return v;
        stack<TreeNode*> stk1, stk2;
        stk1.push(root);
        while(stk1.size()) {
            auto p = stk1.top();
            stk1.pop();
            stk2.push(p);
            //对stk1,先存左子树、再右子树，再将栈弹出的结果存到stk2中
            if(p->left) stk1.push(p->left);
            if(p->right) stk1.push(p->right);
        }
        //最后逆序输出的结果就是后序
        while(stk2.size()) {
            auto p = stk2.top();
            v.push_back(p->val);
            stk2.pop();
        }
        return v;
    }
};
```
### 146.LRU缓存[*****](https://leetcode.cn/problems/lru-cache/description/)
```cpp
class LRUCache {
public:
    struct Node{
        int key, val;
        Node *left, *right;
        Node(int _key, int _val):key(_key), val(_val), left(NULL), right(NULL){}
    }*L, *R;
    unordered_map<int,Node*> hash;
    //双链表+hash实现LRU机制
    int n;

    LRUCache(int capacity) {
        n = capacity;
        L =  new Node(-1, -1), R = new Node(-1, -1);
        //构建双链表头尾节点
        L->right = R;
        R->left = L;
    }
    //在双链表头节点处插入，即在L后面插入p节点
    void insert(Node *p) { 
        p->right = L->right;
        L->right->left = p;
        L->right = p;
        p->left = L;
    }
    void remove(Node *p) { 
        p->left->right = p->right;
        p->right->left = p->left;
    }
    //从双链表中获取对应节点的信息，然后将其更新到头节点后
    int get(int key) {
        //查找失败
        if(!hash.count(key)) return -1;
        auto p = hash[key];
        //实现LRU
        remove(p);
        insert(p);
        return p->val;
    }
    
    void put(int key, int value) { 
        if(hash.count(key)) {
            //更新对应节点值，同时更新到头节点后
            auto p = hash[key];
            p->val = value;
            remove(p);
            insert(p);
        }
        else {
            //hash表中未存储，如果hash表已经满，则把最少使用的链表节点删除（即尾节点前一个节点）
            if(hash.size() == n) {
                auto p = R->left;
                remove(p);
                //从哈希表中删除
                hash.erase(p->key);
                delete(p);
            }
            //在头节点后插入新节点
            Node *p = new Node(key, value);
            hash[key] = p;
            insert(p);
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```
### 147.对链表进行插入排序[***](https://leetcode.cn/problems/insertion-sort-list/)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* insertionSortList(ListNode* head) { 
        ListNode *pre = new ListNode();
        ListNode *p = head;
        //插入排序
        while(p) {
            ListNode *r = pre, *q = p->next;
            //找到插入节点的位置
            while(r->next && r->next->val < p->val) r = r->next;
            //插入到r后方
            p->next = r->next;
            r->next = p;
            p = q;
        }
        return pre->next;
    }
};
```
### 148.排序链表[*****](https://leetcode.cn/problems/sort-list/description/)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
   //常数级空间，则采用非递归归并排序
    ListNode* sortList(ListNode* head) { 
        int n = 0;
        for(auto p = head; p; p = p->next) n++;
        //经过LogN次遍历
        for(int i = 1; i < n; i *= 2)  {
            auto dummy = new ListNode(-1), cur = dummy;
            //把1~n划分成包含2个长度i段，对这2个长度为i的区间进行归并
            for(int j = 1; j <= n; j += 2 * i) {
                //p是左区间的起始位置
                auto p = head, q = p;
                for(int k = 0; k < i && q; k++) q = q->next;
                //q是右区间的起始位置 
                int l = 0, r = 0;
                while(l < i && r < i && p && q) 
                    if(p->val < q->val) cur = cur->next = p, p = p->next, l++;
                    else cur = cur->next = q, q = q->next, r++;
                while(l < i && p) cur = cur->next = p, p = p->next, l++;
                while(r < i && q) cur = cur->next = q, q = q->next, r++;
                //上面对左右区间进行归并
                 //q是下一个2*i区间的起始位置, 下一个2*i区间同样的操作
                head =  q;
            }
            //最后一个节点后设置NULL
            cur->next = NULL;
            //进行下一轮归并
            head = dummy->next;
        }
        return head;
    }
};
```
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public: 
    ListNode* sortList(ListNode* head) { 
        int  n = 0;
        for(ListNode *p = head; p; p = p->next) n++; 
        // 自底向上归并排序，进行logn层归并
        for(int i = 1; i < n; i *= 2) {
            // 将head作为旧的链表，pre新的归并链表
            ListNode *pre = new ListNode();
            ListNode *p = head, *q = p, *u = pre;
            // //把1~n划分成包含2个长度i段，对这2个长度为i的区间进行归并，每一层，各个分治区间进行归并
            for(int j = 1; j <=n; j += 2 * i) { 
                
                q = p;
                for(int k = 0; k < i && q; k++) q = q->next;
                int l = 0, r = 0;
                //  p是左区间起始位置，q是右区间起始位置， 还要限制区间长度范围内
                while(l < i && r < i && p && q) {
                    if(p->val < q->val) {
                        u->next = p;
                        p = p->next;
                        l++;
                    }
                    else {
                        u->next = q;
                        q = q->next;
                        r++;
                    }
                    u = u->next;
                } 
                while(l < i && p) {
                    u->next = p;
                    u = u->next;
                    p = p->next;
                    l++;
                }
                while(r < i && q) {
                    u->next = q;
                    u = u->next;
                    q = q->next;
                    r++;
                }
                // 迭代到下一个归并区间起始位置
                p = q; 
            }
            u->next = NULL;
            head = pre->next;
        }
        return head;
    }
};
```
### 149.直线上最多的点数[****](https://leetcode.cn/problems/max-points-on-a-line/description/)
```cpp
class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        typedef long double LD;
        int res = 0;
        for(auto p : points) {
            //计算以p点为中心的直线
            unordered_map<LD,int> hash;
            //ss是和p点重合的个数，vs是过p点垂线
            int ss = 0, vs = 0;
            for(auto q : points)
                if(p == q) ss++;
                else if(p[0] == q[0]) vs++;
                else {
                    //计算斜率，为了保证精度，斜率用long double的精度
                    LD k = (LD)(p[1] - q[1]) / (p[0] - q[0]);
                    hash[k]++;
                }
            //找到过p点最多点的直线
            int c = vs;
            for(auto [u,v] : hash)    
                c = max(c, v);
            res = max(res, c + ss);
        }
        return res;
    }
};
```
### 150.逆波兰表达式求值[****](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)
```cpp
class Solution {
public:
    stack<int> stk;
    void eval(string s) {
        //栈中先弹出b，再弹出a
        int b = stk.top(); stk.pop();
        int a = stk.top(); stk.pop();
        if(s == "+") stk.push(a + b);
        else if(s == "-") stk.push(a - b);
        else if(s == "*") stk.push(a * b);
        else if(s == "/") stk.push(a / b); 
    }
    int evalRPN(vector<string>& tokens) {
        unordered_set<string> S = {"+", "-", "*", "/"};
        for(auto x : tokens) 
        //stoi函数将字符串转化为整数
            if(!S.count(x)) stk.push(stoi(x));
            else eval(x);
        return stk.top();
    }
};
```
