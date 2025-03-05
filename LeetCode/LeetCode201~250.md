### 201.数字范围按位与[****](https://leetcode.cn/problems/bitwise-and-of-numbers-range/description/)
```cpp
class Solution {
public:
    int rangeBitwiseAnd(int left, int right) {
        int res = 0;
        //前面k位相同，left第k+1位为0，right第k+1位为1，left到right中间一定包括后面全为0
        //从而使得相与结果的k+1位后面全为0
        for(int i = 30; i >= 0; i--) {
            if((left >> i & 1) != (right >> i & 1)) break;
            if(left >> i & 1) 
                res += 1 << i;
        }
        return res;
    }
};
```
### 202.快乐数[***](https://leetcode.cn/problems/happy-number/description/)
```cpp
class Solution {
public:
    bool isHappy(int n) {
        unordered_map<int,int> hash;
        hash[n] = 1;
        while(n != 1) {
            int x = 0;
            while(n) {
                x += (n % 10) * (n % 10);
                n /= 10;
            }
            if(hash[x]) return false;
            hash[x] = 1;  
            n = x;
        }
        return true;
    }
};
```
### 203.移除链表元素[***](https://leetcode.cn/problems/remove-linked-list-elements/)
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
    ListNode* removeElements(ListNode* head, int val) {  
        ListNode *pre = new ListNode();
        pre->next = head;
        ListNode *p = pre;
        while(p->next) {
            if(p->next->val == val)  
                p->next = p->next->next; 
            else p = p->next;
        }
        return pre->next;
    }
};
```
### 204.计算质数[****](https://leetcode.cn/problems/count-primes/)
```cpp
class Solution {
public:
    //朴素筛法
    int countPrimes(int n) {
        int cnt = 0;
        vector<bool> st(n + 1, 0);
        for(int  i = 2; i < n; i++) 
            if(!st[i]){
                cnt++;
                //把合数筛掉
                for(int j = i + i; j < n; j += i)
                    st[j] = 1;
          }
        return cnt;
    }
};

class Solution {
public:
    //线性筛法
    int countPrimes(int n) {
        int cnt = 0;
        vector<bool> st(n + 1, 0); 
        vector<int> primes;
        //线性筛法
        for(int i  = 2; i < n; i++) {
            if(!st[i]){
                primes.push_back(i); 
            }
            for(int j = 0; primes[j] <= n / i; j++) {
                    st[primes[j] * i] = 1;
                    if(i % primes[j] == 0) break;
                }
        }
        return primes.size();
    }
};
```
### 205.同构字符串[****](https://leetcode.cn/problems/isomorphic-strings/description/)
```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        if(s.size() != t.size()) return false;
        unordered_map<char, char> s1, s2;
        for(int i = 0; i < s.size(); i++) {
           if(s1.count(s[i])) {
                //s[i]已经在s1中有映射记录，但不是映射到t[i]
                if(s1[s[i]] != t[i]) return false; 
           }
           else s1[s[i]] = t[i];
           if(s2.count(t[i])) {
            //t[i]已经在s2中有映射记录，但不是映射到s[i]
                if(s2[t[i]] != s[i]) return false;
           }
           //将没有记录的t[i]加入到s2中
           else s2[t[i]] = s[i];
        }
        return true;
    }
};
```
### 206.反转链表[****](https://leetcode.cn/problems/reverse-linked-list/)
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
    ListNode* reverseList(ListNode* head) {
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
};
```
### 207.课程表[****](https://leetcode.cn/problems/course-schedule/description/)
```cpp
class Solution {
public:
    bool canFinish(int n, vector<vector<int>>& prerequisites) {
        //先定义长度为n的二维数组，后面再添加
        vector<vector<int>> edges(n);
        vector<int> d(n);
        for(auto x : prerequisites) {
            int a = x[0], b = x[1];
            edges[a].push_back(b);
            //a指向b
            d[b]++;
        }
        stack<int> stk;
        for(int i = 0; i < n; i++)
            if(d[i] == 0) stk.push(i);
        int cnt = 0;
        while(stk.size()) {
            auto p = stk.top();
            cnt++;
            stk.pop();
            for(auto x : edges[p]) 
                if(--d[x] == 0) 
                    stk.push(x);
        }
        return cnt == n;
    }
};
```
### 208.实现Tire（前缀树）[****](https://leetcode.cn/problems/implement-trie-prefix-tree/description/)
```c++
class Trie {
public:
    struct Node {
        Node *lson[26];
        bool is_end;
        Node(){
            //每层26个字母，一层层的存储字符
            for(int i = 0; i < 26; i++) lson[i] = NULL;
            //以该节点的字符作为结尾
            is_end = false; 
        }
    }*root;
    Trie() {
        //初始化根节点
        root = new Node();
    }
    
    void insert(string word) {
        Node *p = root;
        //插入字符串，从根节点开始向下层遍历，如果word字符没查到，则创建子节点，直到把word遍历完
        for(auto c : word) {
            int u = c - 'a';
            if(!p->lson[u]) p->lson[u] = new Node();
            p = p->lson[u];
        }
        //作为字符串结束的标志
        p->is_end = true;
    }
    
    bool search(string word) {
        Node *p = root;
        for(auto c : word) {
            int u = c - 'a';
            //word的字符在tire树中不存在
            if(!p->lson[u]) return false;
            p = p->lson[u];
        }
        //遍历的字符串与word匹配
        return p->is_end;
    }
    
    bool startsWith(string prefix) {
        Node *p = root;
        for(auto c : prefix) {
            int u = c - 'a';
            if(!p->lson[u]) return false;
            p = p->lson[u];
        }
        //遍历此处结束，则表示存在前缀
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```
### 209.长度最小的子数组[****](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)
```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int res = INT_MAX;
        int sum = 0;
        //双指针维护一个前缀和的窗口
        for(int i = 0, j = 0; i < nums.size(); i++) {
            sum += nums[i];
            //将队头的出队
            while(sum - nums[j] >= target) sum -= nums[j++];
            //符合最小的连续子数组
            if(sum >= target) res = min(res, i - j + 1);
        }
        //不存在符合条件
        if(res == INT_MAX) return 0;
        return res;
    }
};
```
### 210.课程表II[****](https://leetcode.cn/problems/course-schedule-ii/description/)
```cpp
class Solution {
public:
    vector<int> findOrder(int n, vector<vector<int>>& prerequisites) {
        vector<int> v;
        vector<vector<int>> edges(n);
        vector<int> d(n, 0);
        for(auto x : prerequisites) {
            int b = x[0], a = x[1];
            edges[a].push_back(b);
            d[b]++;
        }
        stack<int> stk;
        for(int i = 0; i < n; i++)
            if(!d[i]) stk.push(i);
        while(stk.size()) {
            auto p = stk.top();
            stk.pop();
            v.push_back(p);
            for(auto x : edges[p]) 
                if(--d[x] == 0)  stk.push(x);
        }
        //拓扑排序遍历顶点个数<n，则返回空数组
        if(v.size() < n) return {};
        return v;
    }
};
```
### 211.添加与搜索单词-数据结构设计[*****](https://leetcode.cn/problems/design-add-and-search-words-data-structure/description/)
```cpp
class WordDictionary {
public:
    struct Node{
        Node *lson[26];
        bool is_end;
        Node() {
            for(int i =  0; i < 26; i++) lson[i] = NULL;
            is_end = false;
        }
    }*root;
    WordDictionary() {
        root = new Node();
    }
    
    void addWord(string word) {
        Node *p = root;
        for(auto c : word) {
            int u = c - 'a';
            if(!p->lson[u]) p->lson[u] = new Node();
            p = p->lson[u];
        } 
        //作为字符串的终止位
        p->is_end = true;
    }
    
    bool search(string word) {
        return dfs(word, root, 0);
    }
    bool dfs(string word, Node *p, int u) {
        if(u == word.size()) 
        //word在tire中存在，再判断是最后一位否时终止位
            return p->is_end;
        int k = word[u] - 'a';
        //不是通配符，就只有一种匹配的情况
        if(word[u] != '.') {
            if(!p->lson[k]) return false;
            return dfs(word, p->lson[k], u + 1);
        }
        //通配符，有26中情况
        else {
            for(int i = 0; i < 26; i++)
                if(p->lson[i] && dfs(word, p->lson[i], u + 1))    
                    return true;
            return false;
        }
    }
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary* obj = new WordDictionary();
 * obj->addWord(word);
 * bool param_2 = obj->search(word);
 */
```
### 212.单词搜索[****](https://leetcode.cn/problems/word-search-ii/description/)
```cpp
class Solution {
public:
    //dfs+前缀树，总的时间复杂度是n*n * 3 ^ l (l是字符串的最大长度)
    unordered_set<int> ids;
    struct Node{
        Node *son[26];
        //id用作字符的结束，并且标记是哪个字符串
        int id;
        Node(){
            for(int i = 0; i < 26; i++) son[i] = NULL;
            id = -1;
        }
    }*root;
    //四个方向用大括号{}
    int dr[4][2] = {{1,0}, {-1, 0}, {0, 1}, {0, -1}};
    void insert(string word, Node *p, int id) {
        for(auto c : word) {
            int u = c - 'a';
            if(!p->son[u]) p->son[u] = new Node();
            p = p->son[u];
        }
        p->id = id;
    }
    void dfs(vector<vector<char>>& board, int x, int y, Node *p) {
        if(p->id != -1) {
            //找在tire树的字符串，有可能存在前缀，oa, oaa都满足oa的情况，所以还得继续探寻
            ids.insert(p->id); 
        }
        //保留
        char t = board[x][y];
        board[x][y] = '.';
        for(int i  = 0; i < 4; i++) {
            int u = x + dr[i][0], v = y + dr[i][1];
            if(u < 0 || u >= board.size() || v < 0 || v >= board[0].size() || board[u][v] == '.') continue;
            int k = board[u][v] - 'a';
            //p-son[k]判断board[u][v]字符是否满足要求
            if(p->son[k]) dfs(board, u, v, p->son[k]);
        }
        //恢复现场
        board[x][y] = t; 
    }
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        //初始化
        root = new Node();
        //构建tire树
        for(int i = 0; i < words.size(); i++)
            insert(words[i], root, i);
        for(int i = 0; i < board.size(); i++)
        for(int j = 0; j < board[i].size(); j++){
            int u = board[i][j] - 'a';
            //root->son[u]存在，则tire树中存在boar[i][j]
            if(root->son[u]) dfs(board, i, j, root->son[u]);
        }
        vector<string> res;
        //将所有被标记的字符串存储
        for(auto id : ids) res.push_back(words[id]);
        return res;
    }
};
```
### 213.打家劫舍II[*****](https://leetcode.cn/problems/house-robber-ii/submissions/518149278/)
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 1) return nums[0];
        //可以将环在第1个位置分为两种情况，选择第一个，不选择第一个
        vector<int> f(n + 1), g(n + 1);
        //不选择第一个的情况
        int res = 0;
        for(int i = 2; i <= n; i++) {
            //选了第i个，不能选i-1
            f[i] = g[i - 1] + nums[i - 1];
            g[i] = max(f[i - 1], g[i - 1]);
        }
        //此时，可以选n，也可以不选n
        res = max(f[n], g[n]);
        f[1] = nums[0];
        //选了第1个，就不存在不选第1个的情况
        g[1] = INT_MIN;
          for(int i = 2; i <= n; i++) {
            f[i] = g[i - 1] + nums[i - 1];
            g[i] = max(f[i - 1], g[i - 1]);
        }
        //选了。第一个，第n个一定不能选，两者相邻
        res = max(res, g[n]);
        return res;
    }
};
```
### 214.最短回文串[****](https://leetcode.cn/problems/shortest-palindrome/description/)
```cpp
class Solution {
public:
    string shortestPalindrome(string s) {
        //找到s中最短的非回文部分，找到s中的最大回文前缀，可以找s+"#"+s.reverse字符串的最大匹配的前后缀
        int n = s.size();
        //将s旋转之后赋给t
        string t(s.rbegin(), s.rend());
        s = " " + s + "#" + t;
        vector<int> ne(2 * n + 2);
        //找新s的最大匹配前后缀，就是找旧s最大的回文前缀
        for(int i = 2, j = 0; i <= 2 * n + 1; i++) {
            while(j && s[i] != s[j + 1]) j = ne[j];
            if(s[i] == s[j + 1]) j++;
            ne[i] = j;
        }
        int len = ne[2 * n + 1];
        string left = s.substr(1, len), right = s.substr(len + 1, n - len);
        //right.reverse是最小的非回文部分弥补之后，整个字符串就是回文的
        return string(right.rbegin(), right.rend()) + left + right;
    }
};
```

### 786.第k个数[****](https://www.acwing.com/problem/content/788/)
```c++
#include<iostream>
using namespace std;
const int N = 1e5 + 10;
int n, k;
int a[N];
//在l到r之间找到第k个数
int quicksort(int l, int r, int k) {
    if(l == r) return a[l];
    int x = a[l], i = l - 1, j = r + 1;
    while(i < j) {
        while(a[++i] < x);
        while(a[--j] > x);
        if(i < j) swap(a[i], a[j]);
    }
    //i,第一个>=x的数不一定能找到,但是第一个<=x的数是一定可以找到的，所以用j 
    //如过第k个数在左区间
    if(k <= j) return quicksort(l, j, k);
    //如果第k个数在右区间
    return quicksort(j + 1, r, k);
}
int main()
{
    cin >> n >> k;
    for(int i = 0; i < n; i++) cin >> a[i];
    //k先剪1
    cout << quicksort(0, n - 1, k - 1);
    return 0;
}
```
### 215.数组中的第K个最大元素[****](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/)
```cpp
class Solution {
public:
    // 是从大到小找到第k个元素，因此，基准元素左边找到第一个小的，右边找到第一个大的
    int quicksort(vector<int>& nums, int l, int r, int k) {
        if(l == r) return nums[l];
        int x = nums[l], i = l - 1, j = r + 1;
        while(i < j) {
            // 找到第一个比x小的元素，++i哪怕比x大，也能往后找
            while(nums[++i] > x) ;
            // 找到第一个比x大的元素
            while(nums[--j] < x) ;
            if(i < j) swap(nums[i], nums[j]);
        } 
        // 这里要以j为界限，不能用i（i比j大1）
        // 基准元素是第j大，在左边区间查找第k大的元素
        if(k <= j) return quicksort(nums, l, j, k);
        else return quicksort(nums, j + 1, r, k);
    }
    int findKthLargest(vector<int>& nums, int k) {
        return quicksort(nums, 0, nums.size() -1, k - 1);
    }
};
```
### 216.组合总和III[****](https://leetcode.cn/problems/combination-sum-iii/)
```c++
class Solution {
public:
    vector<vector<int>> v;
    vector<int> path;
    //start作为选择元素的七点，n是选择元素的和，k是选择元素的个数
    void dfs(int start, int n, int k) {
        if(!n) {
            if(!k) {
                //k个元素构成的和为n
                v.push_back(path);
                return;
            }
        }
        else {
            if(k) {
                //从start开始选
                for(int i = start; i <= 9; i++) {
                    path.push_back(i);
                    dfs(i + 1, n - i, k - 1);
                    path.pop_back();
                }
            }
        }
    }
    vector<vector<int>> combinationSum3(int k, int n) {
        dfs(1, n, k);
        return v;
    }
};
```
### 217.存在重复元素[**]()
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_set<int> st;
        for(auto x : nums) 
            if(st.count(x)) return true;
            else st.insert(x);
        return false;
    }
};
```
### 218.天际线问题[*****](https://leetcode.cn/problems/the-skyline-problem/description/)
```c++
class Solution {
public:
    vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
        vector<vector<int>> res;
        vector<pair<int, int>> points;
        multiset<int> heights;
        for(auto &b : buildings) {
            points.push_back({b[0], -b[2]});
            points.push_back({b[1], b[2]});
        }
        sort(points.begin(), points.end());
        heights.insert(0);
        for(auto &p : points) {
            int x = p.first, h = abs(p.second);
            //左端点，相同x时，左端点先取出高度大的点
            if(p.second < 0) {
                if(h > *heights.rbegin()) 
                    res.push_back({x, h});
                heights.insert(h);
            }
            //右端点， 相同的y时，右端点线取高度较小的
            else {
                heights.erase(heights.find(h));
                if(h > *heights.rbegin()) 
                    res.push_back({x, *heights.rbegin()});
            }
        }
        return res;
        } 
};
```
### 219.存在重复元素II[***](https://leetcode.cn/problems/contains-duplicate-ii/description/)
```cpp
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_map<int,int> hash;
        for(int i = 0; i < nums.size(); i++) 
            if(hash.count(nums[i]) && i - hash[nums[i]] <= k) return true;
            else hash[nums[i]] = i;
        return false;
    }
};
```
### 220.存在重复元素III[****]()
```cpp
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int indexDiff, int valueDiff) {
        typedef long long LL;
        //multiset有自动排序
        multiset<LL> S;
        //增加哨兵功能，所以是LL，为了避免lower_bound不返回空
        S.insert(1e18), S.insert(-1e18);
        for(int i = 0, j = 0; i < nums.size(); i++) {
            //找到第一个nums[j]的位置，将其删除，维持indexDiff窗口
            if(i - j > indexDiff) S.erase(S.find(nums[j++]));
            //找到第一个>=nums[i]的位置
            auto it = S.lower_bound(nums[i]);

            if(*it - nums[i] <= valueDiff)  return true;
            it--;
            //前一个位置就是小于nums[i]的位置
            if(nums[i] - *it <= valueDiff) return true;
            S.insert(nums[i]);
        }
        return false;
    }
};
```
### 221.正方形关系[****](https://leetcode.cn/problems/maximal-square/)
```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int n = matrix.size(), m = matrix[0].size();
        if(!n || !m) return 0;
        vector<vector<int>> f(n + 1, vector<int>(m + 1));
        int res = 0;
        for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++) 
            if(matrix[i - 1][j - 1] == '1')
            {
                //状态转移方程，正方形边的关系
                f[i][j] = min(f[i - 1][j], min(f[i - 1][j - 1], f[i][j - 1])) + 1;
                res = max(f[i][j], res);
            }
        return res * res;
    }
};
```
### 222.完全二叉树的节点个数[***](https://leetcode.cn/problems/count-complete-tree-nodes/)
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
    int countNodes(TreeNode* root) {
        if(!root) return 0;
        return countNodes(root->left) + countNodes(root->right) + 1;
    }
};
```
### 223.矩形面积[****](https://leetcode.cn/problems/rectangle-area/)
```cpp
class Solution {
public:
    typedef long long LL;
    int computeArea(int ax1, int ay1, int ax2, int ay2, int bx1, int by1, int bx2, int by2) {
        //一维的交集是右端点的最小值-左端点的最大值
        int x = max(0, min(ax2, bx2) - max(ax1, bx1));
        int y = max(0, min(ay2, by2) - max(ay1, by1));
        //x的交集*y的交集则是二维的交集
        //总面积等于A+B-(A-B)
        return (LL)(ax2 - ax1) * (ay2 - ay1) + (LL)(bx2 - bx1) * (by2 - by1) - (LL)x * y;
    }
};
```
### 3302.表达式求值[****](https://www.acwing.com/problem/content/description/3305/)
```cpp
#include<iostream>
#include<stack>
#include<unordered_map>
using namespace std;
stack<int> stk;
stack<char> op;
void eval()
{
    int b = stk.top();
    stk.pop();
    int a = stk.top();
    stk.pop(); 
    char c = op.top();
    op.pop();
    int t;
    if(c == '+') t = a + b;
    else if(c == '-') t = a - b;
    else if(c == '*') t = a * b;
    else t = a / b;
    stk.push(t);
}
int main()
{
    string s;
    cin >> s;
    unordered_map<char,int> pr = {{'+', 1}, {'-', 1}, {'*', 2}, {'/', 2}};
    for(int i  = 0; i < s.size(); i++) {
        if(isdigit(s[i])) {
            int j = i + 1;
            while(j < s.size() && isdigit(s[j])) j++;
            int x = stoi(s.substr(i, j - i));
            i = j - 1;
            stk.push(x);
        }
        else if(s[i] == '(') op.push('(');
        else if(s[i] == ')') {
            while(op.top() != '(') eval();
            op.pop();
        }
        else{
            while(op.size() && pr[op.top()] >= pr[s[i]]) eval(); 
            op.push(s[i]);
        }
    }
    while(op.size()) eval();
    cout << stk.top();
}
```

### 224.基本计算器[]()
```c++
class Solution {
public:
    //运算
    void cal(stack<char> &op, stack<int> &num) {
        int b = num.top(); num.pop();
        int a = num.top(); num.pop();
        char c = op.top(); op.pop();
        int x;
        if(c == '+') x = a + b;
        else if(c == '-') x = a - b;
        else if(c == '*') x = a * b;
        else x = a / b;
        num.push(x);
    }
    int calculate(string rs) {
        stack<char> op;
        stack<int> num; 
        string s;
        //设置优先级
        unordered_map<char,int> pr = {{'+', 1}, {'-', 1}, {'*', 2}, {'/', 2}};
        //去掉空格
        for(auto x : rs) 
            if(x != ' ') 
                s += x;
        for(int i = 0; i < s.size(); i++) { 
            char c = s[i];
            if(isdigit(c)) {
                int k = i;
                while(k < s.size() && isdigit(s[k])) k++;
                //把数字存到num
                int x = stoi(s.substr(i, k - i));
                num.push(x);
                i = k - 1;
            }
            else if(c == '(')   op.push(c);
            else if(c == ')') {
                //一直运算到左括号
                while(op.size() && op.top() != '(') 
                    cal(op, num);
                op.pop();
            }
            else { 
                //运算符在第一位，运算发前面有括号，运算符前面有-或+,表示正数或负数 
                if(!i || s[i - 1] == '(' || s[i - 1] == '-' || s[i - 1] == '+') 
                    num.push(0);
                while(op.size() && pr[op.top()] >= pr[c]) cal(op, num);
                op.push(c);
            }
        }
        //有符号没有运算完
        while(op.size()) cal(op, num);
        return num.top();
    }
};
```
### 225.用队列实现栈[****](https://leetcode.cn/problems/implement-stack-using-queues/)
```cpp
class MyStack {
public:
    //利用两个队列实现一个栈，q是存储的队列，w是缓存队列
    queue<int> q, w;
    MyStack() {

    }
    
    void push(int x) {
        q.push(x);
    }
    
    int pop() {
        //将q.size()-1个数从队头弹出，加入到队列w中
        while(q.size() > 1) w.push(q.front()), q.pop();
        //取出的就是栈顶
        auto p = q.front();
        q.pop();
        //再把缓存队列的数存到队列q中
        while(w.size()) q.push(w.front()), w.pop();
        return p;
    }
    
    int top() {
        while(q.size() > 1) w.push(q.front()), q.pop();
        auto p = q.front();
        q.pop();
        w.push(p);
        while(w.size()) q.push(w.front()), w.pop(); 
        return p;
    }
    
    bool empty() {
        return q.size() == 0;
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

### 226.翻转二叉树[***](https://leetcode.cn/problems/invert-binary-tree/description/)
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
    TreeNode* invertTree(TreeNode* root) { 
        if(!root) return NULL;
        auto p = root->left;
        root->left = invertTree(root->right);
        root->right = invertTree(p);
        return root;
    }
};
```
### 227.基本计数器 II[****](https://leetcode.cn/problems/basic-calculator-ii/description/)
```cpp
class Solution {
public:
     void eval(stack<int> &stk, stack<char> &op) {
        int b = stk.top(); stk.pop();
        int a = stk.top(); stk.pop();
        char c = op.top(); op.pop();
        int x;
        if(c == '+') x = a + b;
        else if(c == '-') x = a - b;
        else if(c == '*') x = a * b;
        else if(c == '/') x = a / b;
        stk.push(x);
     }
    int calculate(string sr) { 
        unordered_map<char,int> pr = {{'-', 1}, {'+', 1}, {'*', 2}, {'/', 2}};
        stack<int> stk;
        stack<char> op;
        string s;
        for(auto x : sr) 
            if(x != ' ') s += x;
        for(int i = 0; i < s.size(); i++) {
            char c = s[i];
            if(isdigit(c)) {
                int j = i + 1;
                while(j < s.size() && isdigit(s[j])) j++;
                int x = stoi(s.substr(i, j - i));
                stk.push(x);
                i = j - 1;
            }
            else if(c == '(') op.push('(');
            else if(c == ')') {
                while(op.size() && op.top() != '(') eval(stk, op);
                op.pop();
            }
            else {
                while(op.size() && pr[op.top()] >= pr[c])   eval(stk, op);
                op.push(c);
            }
        }
        while(op.size()) eval(stk, op);
        return stk.top();
    }
};
```
### 228.汇总区间[***](https://leetcode.cn/problems/summary-ranges/description/)
```c++
class Solution {
public:
    vector<string> summaryRanges(vector<int>& nums) {
        vector<string> res;
        for(int i = 0; i < nums.size(); i++) {
            int j = i + 1;
            while(j < nums.size() && nums[j] == nums[j - 1] + 1) j++;
            if(j == i + 1) res.push_back(to_string(nums[i]));
            else res.push_back(to_string(nums[i]) + "->" + to_string(nums[j - 1]));
            i = j - 1;
        }
        return res;
    }
};
```
### 229.多数元素II[****](https://leetcode.cn/problems/majority-element-ii/)
```c++
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int n = nums.size();
        //建立两个仓库，c1，c2仓库的次数
        int r1, r2, c1 = 0, c2 = 0;
        for(auto x : nums) {
            //如果x在1号仓库
            if(c1 && r1 == x) c1++;
            //x在2号仓库
            else if(c2 && r2 == x) c2++;
            //1号仓库为空
            else if(!c1) r1 = x, c1++;
            //2号仓库为空
            else if(!c2) r2 = x, c2++;
            //即不在1号，2号
            else c1--, c2--;
        }
        //重新统计c1，c2
        c1 = 0, c2 = 0;
        for(auto x : nums) 
            {
                if(x == r1) c1++;
                else if(x == r2) c2++;
            }
            
        vector<int> res;
        if(c1 > n / 3) res.push_back(r1);
        if(c2 > n / 3) res.push_back(r2);
        return res;
    }
};
```
### 230.二叉搜索树中的第K小的元素[***](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)
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
    int res = -1;
    //k要加引用，才能找到第K个节点
    void dfs(TreeNode *root, int &k) {
        if(!root || !k) return; 
        dfs(root->left, k);
        k--;
         if(!k) {
            res = root->val;
            return;
        }
        dfs(root->right, k);  
    }
    int kthSmallest(TreeNode* root, int k) { 
        dfs(root, k);
        return res;
    }
};
```
### 231.2的幂[**](https://leetcode.cn/problems/power-of-two/description/)
```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        int cnt = 0;
        while(n) {
            cnt += n % 2 == 1 ? 1 : 0;
            if(cnt > 1) return false;
            n /= 2;
        }
        return cnt == 1;
    }
};
```
### 232.用栈实现队列[****](https://leetcode.cn/problems/implement-queue-using-stacks/)
```cpp
class MyQueue {
public:
    //两个栈实现一个队列
    stack<int> a, b;
    MyQueue() {

    }
    //在a都栈顶插入元素
    void push(int x) {
        a.push(x);
    }
    //在a的栈底删除元素
    int pop() {
        while(a.size() > 1) b.push(a.top()), a.pop();
        int p = a.top();
        a.pop();
        while(b.size()) a.push(b.top()), b.pop();
        return p;
    }
    //取出栈底元素
    int peek() {
        while(a.size() > 1) b.push(a.top()), a.pop();
        int p = a.top(); 
        while(b.size()) a.push(b.top()), b.pop();
        return p;
    }
    //栈为空，则队列为空
    bool empty() {
        return a.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

### 233.数字1的个数[****](https://leetcode.cn/problems/number-of-digit-one/description/)
```cpp
class Solution {
public:
    int countDigitOne(int n) {
        vector<int> nums;
        while(n) nums.push_back(n % 10), n /= 10;
        reverse(nums.begin(), nums.end());
        int res = 0;
        for(int i = 0; i < nums.size(); i++) {
            int p = 1, left = 0, right = 0;
            //left高位的数
            for(int j = 0; j < i; j++) left = 10 * left + nums[j];
            //right低位的数
            for(int j = i + 1; j < nums.size(); j++) {
                right = 10 * right + nums[j];
                p *= 10;
            }
            // 第i位为0的情况，则高位在0~left-1，低位0~p-1
            if(nums[i] == 0) res += left * p;
            //第i位位为1的情况，则高位在0~left-1且低位0~p-1，高位为left且低位为0~right+1
            else if(nums[i] == 1) res += left * p + right + 1;
            //第i位大于1的情况，则高位在0~left-1且低位0~p-1，高位为left且低位为0~p-1
            else res += left * p + p;
        }
        return res;
    }
};
```
### 234.回文链表[***](https://leetcode.cn/problems/palindrome-linked-list/description/)
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
    ListNode *getMid(ListNode *head) {
        ListNode *p = head, *q = head;
        while(q->next && q->next->next) {
            p = p->next;
            q = q->next->next;
        }
        return p;
    }
    ListNode *reverseList(ListNode *head) {
        ListNode *pre = new ListNode();
        ListNode *p = head, *q;
        while(p) {
            q = p->next;
            p->next = pre->next;
            pre->next = p;
            p = q;
        }
        return pre->next;
    }
    bool isPalindrome(ListNode* head) {  
        ListNode *mid = getMid(head);
        ListNode *p = head, *q = reverseList(mid->next);
        while(q) {
            if(p->val != q->val) return false;
            p = p->next;
            q = q->next;
        }
        return true;
    }
};
```
### 235.二叉搜索树的最近公共祖先[****](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/description/)
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) { 
        if(!root) return NULL;
       //p和q都在左子树中，则它们的祖先也在左子树中
        if(p->val < root->val && q->val < root->val) return lowestCommonAncestor(root->left, p, q);
        //p和q都在右子树中，则它们的祖先也在左子树中
        if(p->val > root->val && q->val > root->val) return lowestCommonAncestor(root->right, p, q); 
         // if(p->val <= root->val && root->val <= q->val || q->val <= root->val && root->val <= p->val) return root;
         //p和q分别在左子树或者右子树，或者p或者q在根节点，则祖先必定是根节点
        return root;
    }
};
```
### 236.二叉树的最近公共祖先[]()
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    TreeNode* res;
public: 
    //判断p或q是否在以root为根的树中
    bool dfs(TreeNode* root, TreeNode *p, TreeNode *q) {
        if(!root) return false; 
        bool lson = dfs(root->left, p, q);
        bool rson = dfs(root->right, p, q);
        //1. 一个在左子树另一个在右子树；   2. 一个为根，另一个在左子树或右子树中
        if(lson && rson || (lson || rson) && (root->val == p->val || root->val == q->val)) { 
            res  = root; 
        }   
        //在左子树中、在右子树中、p或q在根上
        return lson || rson || root->val == p->val || root->val == q->val;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) { 
        dfs(root, p, q);
        return res;
    }
};
```
### 237.删除链表中的节点[****](https://leetcode.cn/problems/delete-node-in-a-linked-list/description/)
```cpp
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
    void deleteNode(ListNode* node) {
        //删除下一个节点，交换两个值，就是删除当前的节点
        ListNode *q = node->next;
        swap(node->val, q->val);
        node->next = node->next->next; 
    }
};
```
### 238.除自身以外数组的乘积[****](https://leetcode.cn/problems/product-of-array-except-self/description/)
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> v(n, 1);
        //算出各个位置的前缀乘积
        for(int i = 1; i < n; i++) v[i] = v[i - 1] * nums[i - 1];
        //再算出各个位置的后缀乘积
        for(int i = n - 1, s = 1; i >= 0; i--) {
            v[i] *= s;
            s *= nums[i];
        }
        return v;
    }
};
```
### 239.滑动窗口最大值[****](https://leetcode.cn/problems/sliding-window-maximum/description/)
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> q;
        vector<int> v;
        //维护一个长度为k的递减的双端队列，队首是窗口k的最大值位置
        for(int i = 0; i < nums.size(); i++) {
            //队列长度超过K时
            if(q.size() && i - q.front() >= k) q.pop_front();
            //加入nuns[i]之前，将队列中比nums[i]小的位置删除
            while(q.size() && nums[q.back()] <= nums[i]) q.pop_back();
            //加入队列中
            q.push_back(i);
            if(i >= k - 1) v.push_back(nums[q.front()]);
        }
        return v;
    }
};
```
### 240.搜索二维矩阵II[****](https://leetcode.cn/problems/search-a-2d-matrix-ii/description/)
```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), m = matrix[0].size();
        //从右上角开始找
        int i = 0, j = m - 1;
    
        while(i < n && j >= 0) {
            int t = matrix[i][j];
            //找到目标
            if(t == target) return true;
            //当前值比目标大，则这一列都比它大，去掉这一列
            else if(t > target) j--;
            //当前值比目标小，则这一行都比它小，去掉这一行
            else i++;
        }
        return false;
    }
};
```
### 241.为运算表达式设置优先级[****](https://leetcode.cn/problems/different-ways-to-add-parentheses/description/)
```
c++class Solution {
public:
    vector<string> v;
    //把所有的运算符中序二叉树遍历出来
    vector<int> dfs(int l, int r) {
        //区间只有一个元素，运算数
        if(l == r) return {stoi(v[l])};
        vector<int> res;
        //运算数和运算符隔一位，i+=2
        for(int i = l + 1; i < r; i += 2) {
            //left左子树的计算结果，right右子树的计算结果
            auto left = dfs(l, i - 1), right = dfs(i + 1, r);
            for(auto x : left) 
            for(auto y : right) {
                if(v[i] == "+") res.push_back(x + y);
                else if(v[i] == "-") res.push_back(x - y);
                else res.push_back(x * y);
            }
        }
        return res;
    }
    vector<int> diffWaysToCompute(string exp) { 
        //将运算数和运算符分离出来
        for(int i  = 0; i < exp.size(); i++)
            if(isdigit(exp[i])) {
                int j = i + 1;
                while(j < exp.size() && isdigit(exp[j])) j++;
                v.push_back(exp.substr(i, j - i));
                i = j - 1;
            }
            else 
            //取出一个字符构成字符串
                v.push_back(exp.substr(i, 1));
            int n = v.size();
            return dfs(0, n - 1);
    }
};
```
### 242.有效的字母异位词[]()
```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char, int> a, b;
        for(auto x : s) a[x]++;
        for(auto x : t) b[x]++;
        return a == b;
    }
};
```
### 243.





