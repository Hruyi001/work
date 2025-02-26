### 257.二叉树的所有路径[***](https://leetcode.cn/problems/binary-tree-paths/description/)
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
    vector<string> v;
    void dfs(TreeNode* root, vector<int> &path) {
        if(!root) return;
        path.push_back(root->val);
        if(!root->left && !root->right) {
            string s;
            for(int i = 0; i < path.size(); i++)
                if(i < path.size() - 1) s += to_string(path[i]) + "->";
                else s += to_string(path[i]);
            v.push_back(s);
            path.pop_back();
            return;
        }
        dfs(root->left, path);
        dfs(root->right, path);
        path.pop_back();
    }
    vector<string> binaryTreePaths(TreeNode* root) { 
        vector<int> path;
        dfs(root, path);
        return v;
    }
};
```
### 258.各位相加[**](https://leetcode.cn/problems/add-digits/description/)
```cpp
class Solution {
public:
    int addDigits(int n) {
        while(n > 9) {
            int t = n, x = 0;
            while(t) x += t % 10, t /= 10;
            n = x;
        }
        return n;
    }
};
```
### 260.只出现一次的数字 III [****](https://leetcode.cn/problems/single-number-iii/)
```cpp
class Solution {
public:
    //找到所有第k位为s的异或结果，分别是a，b
    int get(vector<int> &nums, int k, int s) {
        int t = 0;
        for(auto x : nums)
            if((x >> k & 1) == s) 
                t ^= x;
        return t;
    }
    vector<int> singleNumber(vector<int>& nums) {
        int ab = 0;
        //找到最小的两数ab异或的结果
        for(auto x : nums) ab ^= x;
        int t = ab, k = 0;
        //找到ab中出现1的位数，因为a和b不同，异或的2进制结果一定会出现1，作为两者的区别
        while((t>>k & 1) == 0) k++;
        return {get(nums, k, 0), get(nums, k, 1)};
    }
};
```

### 263.丑数[**](https://leetcode.cn/problems/ugly-number/)
```cpp
class Solution {
public:
    bool isUgly(int n) {
        if(!n) return false;
        while(n % 2 == 0) n /= 2;
        while(n % 3 == 0) n /= 3;
        while(n % 5 == 0) n /= 5;
        return n == 1;
    }
};
```
### 264.丑数2[****](https://leetcode.cn/problems/ugly-number-ii/)
```cpp
class Solution {
public:
    int nthUglyNumber(int n) {
        //S是丑数序列   
        //3种情况，2*S, 3*S, 5*S，对这三路进行归并
        //v中预先设置1，开始归并
        vector<int> v(1, 1);
        //1也是丑数，再得到n-1个丑数，可得到所求
        for(int i = 0, j = 0, k = 0; v.size() < n;) {
            //三路中最小值归并到v中
            int t = min(2 * v[i], min(3 * v[j], 5 * v[k]));
            v.push_back(t);
            //下面三个都是if，会出现相等的情况
            if(t == 2 * v[i]) i++;
            if(t == 3 * v[j]) j++;
            if(t == 5 * v[k]) k++;
        }
        return v.back();
    }
};
```

### 268.丢失的数字[]()
 
### 257.二叉树的所有路径[***](https://leetcode.cn/problems/binary-tree-paths/description/)
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
    vector<string> v;
    void dfs(TreeNode* root, vector<int> &path) {
        if(!root) return;
        path.push_back(root->val);
        if(!root->left && !root->right) {
            string s;
            for(int i = 0; i < path.size(); i++)
                if(i < path.size() - 1) s += to_string(path[i]) + "->";
                else s += to_string(path[i]);
            v.push_back(s);
            path.pop_back();
            return;
        }
        dfs(root->left, path);
        dfs(root->right, path);
        path.pop_back();
    }
    vector<string> binaryTreePaths(TreeNode* root) { 
        vector<int> path;
        dfs(root, path);
        return v;
    }
};
```
### 258.各位相加[**](https://leetcode.cn/problems/add-digits/description/)
```cpp
class Solution {
public:
    int addDigits(int n) {
        while(n > 9) {
            int t = n, x = 0;
            while(t) x += t % 10, t /= 10;
            n = x;
        }
        return n;
    }
};
```
### 260.只出现一次的数字 III [****](https://leetcode.cn/problems/single-number-iii/)
```cpp
class Solution {
public:
    //找到所有第k位为s的异或结果，分别是a，b
    int get(vector<int> &nums, int k, int s) {
        int t = 0;
        for(auto x : nums)
            if((x >> k & 1) == s) 
                t ^= x;
        return t;
    }
    vector<int> singleNumber(vector<int>& nums) {
        int ab = 0;
        //找到最小的两数ab异或的结果
        for(auto x : nums) ab ^= x;
        int t = ab, k = 0;
        //找到ab中出现1的位数，因为a和b不同，异或的2进制结果一定会出现1，作为两者的区别
        while((t>>k & 1) == 0) k++;
        return {get(nums, k, 0), get(nums, k, 1)};
    }
};
```

### 263.丑数[**](https://leetcode.cn/problems/ugly-number/)
```cpp
class Solution {
public:
    bool isUgly(int n) {
        if(!n) return false;
        while(n % 2 == 0) n /= 2;
        while(n % 3 == 0) n /= 3;
        while(n % 5 == 0) n /= 5;
        return n == 1;
    }
};
```
### 264.丑数2[****](https://leetcode.cn/problems/ugly-number-ii/)
```cpp
class Solution {
public:
    int nthUglyNumber(int n) {
        //S是丑数序列   
        //3种情况，2*S, 3*S, 5*S，对这三路进行归并
        //v中预先设置1，开始归并
        vector<int> v(1, 1);
        //1也是丑数，再得到n-1个丑数，可得到所求
        for(int i = 0, j = 0, k = 0; v.size() < n;) {
            //三路中最小值归并到v中
            int t = min(2 * v[i], min(3 * v[j], 5 * v[k]));
            v.push_back(t);
            //下面三个都是if，会出现相等的情况
            if(t == 2 * v[i]) i++;
            if(t == 3 * v[j]) j++;
            if(t == 5 * v[k]) k++;
        }
        return v.back();
    }
};
```
### 268.丢失的数字[]()
```c++
 class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int res = 0;
        for(int i  = 0; i <= nums.size(); i++) res += i;
        for(auto x : nums) res -= x;
        return res;
    }
};
```
### 273.整数转换英文表示[*****](https://leetcode.cn/problems/integer-to-english-words/description/)
```c++
class Solution {
public:
    //个位数
    string num0_19[20] = {
        "Zero", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten",
        "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"
    };
    //十位数
    string num20_90[8] = {
        "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", 
        "Eighty", "Ninety"
    };
   
    string num1000[4] = {
        "Billion ", "Million ", "Thousand ",
    };
     //返回1~999的英文显示
    string get(int x) {
        string res;
        //转换百位数的字符
        if(x >= 100) {
            res += num0_19[x / 100] + " Hundred ";
            x %= 100;
        }
        //转换十位数
        if(x >= 20) {
            res += num20_90[x / 10 - 2] + " ";
            x %= 10;
            //转换个位数
            if(x ) 
                res += num0_19[x] + " ";
        }
        //转换20以下的数
        else if(x) 
            res += num0_19[x] + " ";
        return res;
    }
    string numberToWords(int num) {
        if(!num) return "Zero";
        string res;
        for(int i = 1e9, j = 0; i; i /= 1000, j++) 
            if(num >= i) {
                res += get(num / i) + num1000[j];
                num %= i;
            }
        //最后多一个空格
        res.pop_back();
        return res;
    }
};
```
### 274.H指数[****](https://leetcode.cn/problems/h-index/)
```cpp
class Solution {
public:
    int hIndex(vector<int>& citations) {
        //从大到小排序
        sort(citations.begin(), citations.end(), greater<int>());
        //从高位开始遍历，从小的数开始
        for(int h = citations.size(); h; h--)  
        //小的数>=h，则前面大的数都>=h
            if(citations[h - 1] >= h) 
                return h;
        return 0;
    }
};
```
### 275. H指数II[]()
```c++
class Solution {
public:
    int hIndex(vector<int>& c) {
        int n = c.size();
        int l = 0, r = n;
        while(l < r) {
            int mid = l + r + 1>> 1;
            //找到最大的mid
            //有mid个引用>=mid
            if(c[n - mid] >= mid) l = mid;
            else r = mid - 1;
        }
        return l;
    }
};
```
### 278.第一个错误的版本[***](https://leetcode.cn/problems/first-bad-version/description/) 
```c++
// The API isBadVersion is defined for you.
// bool isBadVersion(int version);

class Solution {
public:
    int firstBadVersion(int n) {
        int l = 1, r = n;
        while(l < r) {
            //防止溢出
            int mid = (long long)l + r >> 1;
            if(isBadVersion(mid)) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```
### 279.完全平方数[*****](https://leetcode.cn/problems/perfect-squares/)
```c++
class Solution {
public:
    //背包问题
    int numSquares(int n) { 
        vector<int> f(n + 1, 1e9);
        f[0] = 0;
        for(int i = 1; i <= n / i; i++) {
            int w = i * i;
            for(int j = w; j <= n; j++)
                f[j] = min(f[j], f[j - w] + 1);
        }
        return f[n];
    }
};
```

### 283.移动零[***](https://leetcode.cn/problems/move-zeroes/description/)
```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int k = 0;
        for(auto x : nums)
            if(x) nums[k++] = x;
        while(k < nums.size()) nums[k++] = 0; 
    }
};
```
### 284.窥视迭代器[****](https://leetcode.cn/problems/peeking-iterator/)
```c++
/*
 * Below is the interface for Iterator, which is already defined for you.
 * **DO NOT** modify the interface for Iterator.
 *
 *  class Iterator {
 *		struct Data;
 * 		Data* data;
 *  public:
 *		Iterator(const vector<int>& nums);
 * 		Iterator(const Iterator& iter);
 *
 * 		// Returns the next element in the iteration.
 *		int next();
 *
 *		// Returns true if the iteration has more elements.
 *		bool hasNext() const;
 *	};
 */

class PeekingIterator : public Iterator {
public:
    int cur;
    bool has_next;
	PeekingIterator(const vector<int>& nums) : Iterator(nums) {
	    // Initialize any member here.
	    // **DO NOT** save a copy of nums and manipulate it directly.
	    // You should only use the Iterator interface methods.
	    has_next = Iterator::hasNext();
        if(hasNext()) cur = Iterator::next();
	}
	
    // Returns the next element in the iteration without advancing the iterator.
	int peek() {
        return cur;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	int next() {
	    int t = cur;
        has_next = Iterator::hasNext();
        if(has_next)    cur = Iterator::next();
        return t;
	}
	
	bool hasNext() const {
	    return has_next;
	}
};
```
### 287.寻找重复数[****](https://leetcode.cn/problems/find-the-duplicate-number/description/)
```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        //环的入口就是重复的数，0是起点，入口会被索引两次
        int a = 0, b = 0;
        while(true) {
            a = nums[a];
            b = nums[nums[b]];
            if(a == b) {
                a = 0;
                while(a != b) {
                    a = nums[a];
                    b = nums[b];
                }
                return a;
            }
        }
        return -1;
    }
};
```
### 289.生命游戏[****]()
```c++
class Solution {
public:
    void gameOfLife(vector<vector<int>>& board) {
        if(board.empty() || board[0].empty()) return;
        int n = board.size(), m = board[0].size();
        //将board[i][j]二进制第二位作为状态的更新位，从而保持O(1)空间
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++) {
                int live = 0;
                //探索八个位置的细胞存活情况
                for(int x = max(0, i - 1); x <= min(i + 1, n - 1); x++)
                for(int y = max(0, j - 1); y <= min(j + 1, m - 1); y++)
                    //(x, y) != (i, j)
                    if((x != i || y != j) && (board[x][y] & 1))
                        live++;
                int cur = board[i][j] & 1, next;
                if(cur) {
                    if(live < 2 || live > 3) next = 0;
                    else next = 1;
                }
                else {
                    if(live == 3)   
                        next = 1;
                    else 
                        next = 0;
                }
                board[i][j] |= next << 1; 
            }
            for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                board[i][j] >>= 1;
            
    }
};
```
### 290.单词规律[****](https://leetcode.cn/problems/word-pattern/description/)
```c++
class Solution {
public:
    bool wordPattern(string pattern, string s) {
        vector<string> words;
        stringstream ssin(s);
        string word;
        while(ssin >> word) words.push_back(word);
        unordered_map<char, string> pw;
        unordered_map<string, char> wp;
        if(pattern.size() != words.size())  return false;
        //要求pattern和words是全射，a映射b，b映射a
        for(int i = 0; i < pattern.size(); i++) {
            auto a = pattern[i];
            auto b = words[i];
            if(pw.count(a) && pw[a] != b) return false;
            if(wp.count(b) && wp[b] != a) return false;
            pw[a] = b;
            wp[b] = a;
        }
        return true;
    }
};
```
### 292.Nim游戏[***](https://leetcode.cn/problems/nim-game/description/)
```c++
class Solution {
public:
    bool canWinNim(int n) {
        //0必输，1，2，3，必赢，4必输。。。
        return n % 4;
    }
};
```
### 295.数据流的中位数[*****](https://leetcode.cn/problems/find-median-from-data-stream/)
```c++
class MedianFinder {
public:
    // 大根堆
    priority_queue<int> down;
    // 小根堆
    priority_queue<int, vector<int>, greater<int>> up;
    MedianFinder() {
        
    }
    时间复杂度log(n)
    void addNum(int num) {
        // 大根堆为空或者元素<=大根堆堆顶
        if(down.empty() || num <= down.top()) {
            down.push(num);
            // 大根堆个数<= 小根堆个数+1
            if(down.size() -1 > up.size()) {
                up.push(down.top());
                down.pop();
            }
        }
        else {
            up.push(num);
            // 大根堆超过小根堆时
            if(up.size() > down.size()) {
                down.push(up.top());
                up.pop();
            }
        }
    }
        // 时间复杂度o(1)
    double findMedian() {
        if(down.size() == up.size()) return (down.top() + up.top()) / 2.0;
        else return down.top();
    }
};
 

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder* obj = new MedianFinder();
 * obj->addNum(num);
 * double param_2 = obj->findMedian();
 */
```
### 297.二叉树的序列化与反序列化[*****](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Codec {
public:
    string path;
    // Encodes a tree to a single string.
    //前序序列化，将树的前序遍历转成字符串，val+,  #,
    string serialize(TreeNode* root) {
        dfs_s(root);
        return path;
    }
    void dfs_s(TreeNode* root) {
        if(!root) {
            path += "#,";
            return;
        }
        path += to_string(root->val) + ",";
        dfs_s(root->left);
        dfs_s(root->right);
        return;
    }
    // Decodes your encoded data to tree.
    //将序列化字符串转成前序遍历的二叉树
    TreeNode* deserialize(string data) {
        int u = 0;
        return dfs_d(data, u);
    }
    TreeNode* dfs_d(string data, int &u) {
        if(data[u] == '#') {
            u += 2;
            return NULL;
        }
        int k = u;
        while(k < data.size() && data[k] != ',') k++;
        TreeNode *root = new TreeNode(stoi(data.substr(u, k - u)));
        //跳过逗号
        u = k + 1;
        root->left = dfs_d(data, u);
        root->right = dfs_d(data, u);
        return root;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec ser, deser;
// TreeNode* ans = deser.deserialize(ser.serialize(root));
```
### 299.猜数字游戏[****](https://leetcode.cn/problems/bulls-and-cows/)
```c++
class Solution {
public:
    string getHint(string secret, string guess) {
        unordered_map<char, int> hash;
        for(auto c : secret) hash[c]++;
        int tot = 0;
        //找到secret和guess所有匹配的个数
        for(auto c : guess) 
            if(hash[c]) {
                hash[c]--;
                tot++;
            }
        int bull = 0;
        //bull是对应位置相同的个数
        //cow是总数-bull
        for(int i = 0; i < secret.size(); i++)
            if(secret[i] == guess[i]) 
                bull++;
        return to_string(bull) + "A" + to_string(tot-bull) + "B";
    }
};
```
### 300.最长递增子序列[*****](https://leetcode.cn/problems/longest-increasing-subsequence/description/)
```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n, 0);
        int res = 0;
        //动态规划找最长上升子序列，时间复杂度O(n*n)
        for(int i = 0; i < n; i++) { 
            f[i] = 1;
            for(int j = 0; j < i; j++)
                if(nums[j] < nums[i]) 
                    f[i] = max(f[i], f[j] + 1);
            res = max(res, f[i]);
        }
    
        return res;
    }
};
```
```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        //构造单调队列q,希望单调队列尽可能长，则要让单调队列上升尽可能慢
        vector<int> q;
        for(auto x : nums) {
            if(q.empty() || q.back() < x) q.push_back(x);
            else {
                int l = 0, r = q.size() - 1;
                while(l < r) {
                    int mid = l + r >> 1;
                    //在单调队列中找到第一个>=x的位置，然后替换
                    if(q[mid] >= x) r = mid;
                    else l = mid + 1;
                }
                q[l] = x;
            }
        }
        return q.size();
    }
};
```