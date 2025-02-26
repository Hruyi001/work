### 306.累加数[***](https://leetcode.cn/problems/additive-number/)
```c++
class Solution {
public:
    //高精度加法
    string add(string a, string b) {
        vector<int> A, B, C;
        //高位到低位换成低位到高位
        for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
        for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
        int t = 0;
        for(int i = 0; i < A.size() || i < B.size() || t; i++) {
            int a = i < A.size() ? A[i] : 0;
            int b = i < B.size() ? B[i] : 0;
            t += a + b;
            C.push_back(t % 10);
            t /= 10;
        }
        string s;
        //逆置复原
        for(int i = C.size() - 1; i >= 0; i--) s += to_string(C[i]);
        return s;
    }
    bool isAdditiveNumber(string num) {
        
        for(int i = 0; i < num.size(); i++)
            for(int j = i + 1; j < num.size() - 1; j++) {
                //第一个数是num[a+1, b], 第二个数是num[b+1,c],第三个数是num[c+1, ?]
                int a = -1, b = i, c = j;
                while(true) {
                    //不能包含前导0的情况
                    if( b- a > 1 && num[a + 1] == '0' || c - b > 1 && num[b + 1] == '0') break;
                    string x = num.substr(a + 1, b - a), y = num.substr(b + 1, c - b);
                    //计算出正确的第三个数
                    auto z = add(x, y);
                    //第三个数不正确
                    if(num.substr(c + 1, z.size()) != z) break;
                    //继续向后验证
                    a = b, b = c, c += z.size();
                    //字符串全部符合
                    if(c + 1 == num.size()) return true;
                }
            }
        return false;
    }
};
```

### 307[***](https://leetcode.cn/problems/range-sum-query-mutable/description/)
```c++
class NumArray {
public:
    int n;
    // nums存储的是底层的数组
    vector<int> tr, nums;
    //得到当前二进制最低位1的数
    int lowbit(int x) {
        return x & -x;
    }
  
    //查询[1~x]的前缀和
    int query(int x) {
        int res = 0;
        for(int i = x; i; i -= lowbit(i)) res += tr[i];
        return res;
    }
    //修改树状数组，更新了nums[x]后，修改与之相关联的tr[i]
    void add(int x, int v) {
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v; 
    }
      //树状数组是从1~n
    NumArray(vector<int>& _nums) {
        nums = _nums;
        n = nums.size();
        tr.resize(n + 1);
        for(int i = 1; i <= n; i++) add(i, nums[i - 1]);
    }
    void update(int index, int val) {
        add(index + 1, val - nums[index]);
        nums[index] = val;
    }
    
    int sumRange(int left, int right) {
        return query(right + 1) - query(left);
    } 
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * obj->update(index,val);
 * int param_2 = obj->sumRange(left,right);
 */
 ```
### 322.零钱兑换[****](https://leetcode.cn/problems/coin-change/)
```c++
class Solution {
public:
// 硬币个数无限，计算amount金额的最少个数，f[j]是硬币个数, j表示金额
    int coinChange(vector<int>& coins, int amount) {
        vector<int>f(amount + 1, 1e9);
        // 金额为0，硬币个数为0
        f[0] = 0; 
        for(int i = 0; i < coins.size(); i++)
        for(int j = coins[i]; j <= amount; j++)
            f[j] = min(f[j], f[j - coins[i]] + 1);
        if(f[amount] == 1e9) return -1;
        return f[amount];
    }
};
```

### 326.3的幂
```c++
class Solution {
public:
    bool isPowerOfThree(int n) {
        // 3^19 = 1162261467, 如果n是幂函数，那么n一定能被它整除
        return n >0 && 1162261467 % n == 0;
    }
};
```

### 331.验证二叉树的前序序列化[****](https://leetcode.cn/problems/verify-preorder-serialization-of-a-binary-tree/)
```c++
class Solution {
public:
    int k;
    string s;
    bool isValidSerialization(string preorder) {
        k = 0;
        s = preorder + ",";
        if(!dfs()) return false;
        return k == s.size();
    }
    bool dfs() {
        // 已经遍历完
        if(k == s.size()) return false;
        // 当前节点为空
        if(s[k] == '#') {
            k += 2;
            return true;
        }
        // 当前节点非空，找到一个节点位置
        while(s[k] != ',') k++;
        k++;
        // 左子树和右子树都为真
        return dfs() && dfs();
    }
};
```
### 334.递增的三元子序列[***](https://leetcode.cn/problems/increasing-triplet-subsequence/description/)
```c++
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        // 预存两个数
        vector<int>q(2, INT_MAX);
        for(auto x : nums) {
            int k = 2;
            // 非递增的删掉，维持递增顺序
            while(k > 0 && q[k - 1] >= x) k--;
            // 三个递增的
            if(k == 2) return true;
            q[k] = x;
        }
        return false;
    }
};
```

### 337.打家劫舍III[***](https://leetcode.cn/problems/house-robber-iii/description/)
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
    int rob(TreeNode* root) {
        auto f = dfs(root);
        // 选根节点，不选根节点
        return max(f[0], f[1]);
    }
    vector<int> dfs(TreeNode* root) {
        if(!root) 
            return {0, 0}; 
        // x是左子树中的选和不选的最大值，y是右子树中的选和不选的最大值
        auto x = dfs(root->left), y = dfs(root->right);
        // 不选当前节点，则可以选子节点也可以不选
        // 选当前节点，则子节点一定不能选
        return {max(x[0], x[1]) + max(y[0], y[1]), root->val + x[0] + y[0]};
    }
};
```
### 338.比特位计数[***](https://leetcode.cn/problems/counting-bits/description/)
```c++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int>f(n + 1, 0);
        for(int i = 1; i <= n; i++) {
            f[i] = f[i >> 1] + (i & 1);
        }
        return f;
    }
};
```

### 341.扁平化嵌套列表迭代器[***](https://leetcode.cn/problems/flatten-nested-list-iterator/)
```c++
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */

class NestedIterator {
public:
    vector<int> q;
    int k = 0;
    NestedIterator(vector<NestedInteger> &nestedList) {
        k = 0;
        // 递归遍历
        for(auto l : nestedList) dfs(l);
    }
    
    void dfs(NestedInteger l) {
        if(l.isInteger()) q.push_back(l.getInteger());
        else{
            for(auto &x  : l.getList())
                dfs(x);
        }
    }
    int next() {
        // 迭代
        return q[k++];
    }
    
    bool hasNext() {
        return k < q.size();
    }
};

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i(nestedList);
 * while (i.hasNext()) cout << i.next();
 */
 ```
### 342.4的幂[***](https://leetcode.cn/problems/power-of-four/)
```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        if(n <= 0) return false;
        int t = sqrt(n); 
        // 4的幂次方是平方数
        if(t * t != n) return false;
        // 所有的因子被2整除
        return (1<< 30) % n == 0;
    }
};
```

### 343.整数拆分[****](https://leetcode.cn/problems/integer-break/)
```c++
// a>=5时， a= 3+(a-3) 3*(a-3)>a =>a>4.5
// a=4时， 2*2 = 2+2
// 要么选3，要么选2，3*3 > 2*2*2,替换成3的乘积更大
// 方法一、
class Solution {
public:
    int integerBreak(int n) {
        if(n <= 3) return 1 * (n - 1);
        vector<int> f(n + 1, 0);
        f[2] = 2, f[3] = 3;
        // 4以上的整数拆分
        for(int i = 4; i <= n; i++)
        for(int j = 1; j <= i - 1; j++)
            f[i] = max(f[i], f[i - j] * j);
        return f[n];
    }
};

// 方法二、
class Solution {
public:
    int integerBreak(int n) {
        if(n <= 3) return 1*(n - 1);
        int res = 1;
        if(n % 3 == 1) n -= 4, res = 4;
        else if(n % 3 == 2) n-=2, res = 2;
        while(n) n -=3, res*=3;
        return res;
    }
};
```
### 344.反转字符串[***]()
```c++
class Solution {
public:
    void reverseString(vector<char>& s) {
        for(int i = 0, j = s.size() - 1; i < j; i++, j--)
            swap(s[i], s[j]);
        
    }
};
```
### 345.反转字符串中的元音字母[**](https://leetcode.cn/problems/reverse-vowels-of-a-string/description/)
```c++
class Solution {
public:
    string reverseVowels(string s) {
        unordered_map<char,int> mp;
        mp['a'] = 1;
        mp['e'] = 1;
        mp['i'] = 1;
        mp['o'] = 1;
        mp['u'] = 1;
        int i = 0, j = s.size() - 1;
        while(i < j) {
            while(i < j && !mp.count(tolower(s[i]))) i++;
            while(i < j && !mp.count(tolower(s[j]))) j--;
            if(i < j) swap(s[i++], s[j--]);
        }
        return s;
    }
};
```
### 347.前k个高频元素[***](https://leetcode.cn/problems/top-k-frequent-elements/description/)
```c++
// 方法一、大根堆
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // 大根堆方法
        unordered_map<int,int> mp;
        for(auto x : nums) mp[x]++;
        priority_queue<pair<int,int>> heap;
        for(auto x : mp) 
        // 先比较次数
            heap.push({x.second, x.first});
        vector<int> v;
        // 出现频率前k多的
        for(int i = 0; i < k; i++) {
            v.push_back(heap.top().second);
            heap.pop();
        }
        return v;
    }
};

// 方法二、找前k个频率的阈值
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) { 
        vector<int> v;
        int n = nums.size();
        unordered_map<int,int> mp;
        for(auto x : nums) mp[x]++;
        vector<int> s(n+1);
        // 把频率出现次数存到s中
        for(auto x : mp)
            s[x.second]++;
        // 找到频率前K个的阈值
        int t = 0, i = n;
        while(t < k) t += s[i--];
        for(auto x : mp)
            if(x.second > i) 
                v.push_back(x.first);
        return v;
    }
};
```
### 349.两个数组的交集[**](https://leetcode.cn/problems/intersection-of-two-arrays/description/)
```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        set<int> S;
        for(auto x : nums1) S.insert(x);
        vector<int> v;
        for(auto x : nums2)
            if(S.count(x)) {
                v.push_back(x);
                S.erase(x);
            }
        return v;
    }
};
```

### 350. 两个数组的交集[***](https://leetcode.cn/problems/intersection-of-two-arrays-ii/)
```c++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        if(nums1.size() < nums2.size()) {
            return intersect(nums2, nums1);
        }
        vector<int> v;
        unordered_map<int,int> mp;
        for(auto x : nums1) mp[x]++;
        for(auto x : nums2) 
            if(mp.count(x)) {
                mp[x]--;
                v.push_back(x);
                if(mp[x] == 0) {
                    mp.erase(x);
                }
            }
        return v;
    }   
};
```