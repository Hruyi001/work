### 51.N皇后[*****](https://leetcode.cn/problems/n-queens/)
```cpp
class Solution {
public: 
    vector<vector<string>> res;
    vector<bool> col, ag, ug;
    vector<string> path;
    int n;
    //皇后一定处于不同行，u代表行，i代表列，不能在同行同列同主副对角线
    void dfs(int u) {
        //N皇后可能存在多种情况
        if(u == n) {
            res.push_back(path);
            return;
        }
        //检查各列的情况
        for(int i = 0; i < n; i++)
            if(!col[i] && !ag[u + i] && !ug[n -i + u]) {
                col[i] = ag[u + i] = ug[n -i + u] = 1;
                path[u][i] = 'Q';
                dfs(u + 1);
                col[i] = ag[u + i] = ug[n -i + u] = 0;
                path[u][i] = '.';
            }
    }
    vector<vector<string>> solveNQueens(int _n) {
        n = _n;
        col = vector<bool>(n);
        ag = ug = vector<bool>(2*n);
        path =  vector<string> (n, string(n, '.')); 
        dfs(0);
        return res;
    }
};
```
### 52.N皇后II[***](https://leetcode.cn/problems/n-queens-ii/description/)
```cpp
class Solution {
public: 
    vector<bool> col, ag, ug; 
    int n, res;
    //皇后一定处于不同行，u代表行，i代表列，不能在同行同列同主副对角线
    void dfs(int u) {
        //N皇后可能存在多种情况
        if(u == n) { 
            res++;
            return;
        }
        //检查各列的情况
        for(int i = 0; i < n; i++)
            if(!col[i] && !ag[u + i] && !ug[n -i + u]) {
                col[i] = ag[u + i] = ug[n -i + u] = 1; 
                dfs(u + 1);
                col[i] = ag[u + i] = ug[n -i + u] = 0; 
            }
    }
    int totalNQueens(int _n) {
        n = _n;
        col = vector<bool>(n);
        ag = ug = vector<bool>(2*n); 
        dfs(0);
        return res;
    }
};
```

### 53.最大子数组和[***](https://leetcode.cn/problems/maximum-subarray/)
```c++
//动态规划
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        //f[i]表示所有以nums[i]结尾的区间中的最大值
        //f[i] = max{nums[i], f[i - 1] + nums[i]}
        //     = nums[i] + max(f[i - 1], 0)
        int res = INT_MIN, last = 0;
        for(int i = 0; i < nums.size(); i++) {
            last = nums[i] + max(0, last);
            res = max(res, last);
        }
        return res;
    }
};

//分治法
class Solution {
public:
    struct Node{
        int sum, s, ls, rs; //总和，总的最大和，最大前缀，最大后缀
    };
    Node build(vector<int>& nums, int l, int r) {
        
        if(l == r) {
            int v = max(nums[l], 0);
            return {nums[l], v, v, v};
        }
        int mid = l + r >> 1;
        Node L = build(nums, l, mid), R = build(nums, mid + 1, r);
        Node res;
        res.sum = L.sum + R.sum;
        //总的最大和是左区间最大和，右区间最大和，左区间的最大后缀+右区间的最大前缀 的最大值
        res.s = max(max(L.s, R.s), L.rs + R.ls);
        //总的最大前缀是左区间最大前缀，左区间和+右区间最大前缀 的最大值
        res.ls = max(L.ls, L.sum + R.ls);
        //总的最大后缀是右区间最大后缀，右区间和+左区间最大前后缀 的最大值
        res.rs = max(R.rs, R.sum + L.rs);
        return res;
    }
    int maxSubArray(vector<int>& nums) { 
        int res = INT_MIN;
        for(auto x: nums) res = max(res, x);
        if(res < 0) return res;
        Node t = build(nums, 0, nums.size() - 1);
        return t.s; 
    }
};
```
### 54.螺旋矩阵[***](https://leetcode.cn/problems/spiral-matrix/description/)
``` c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int n = matrix.size();
        if(!n) return res;
        int m = matrix[0].size();
        //定义方向，向右，向下，向左，向上  循环次过程
        int dr[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        //判断各位置有没有被访问过
        vector<vector<bool>> st(n, vector<bool>(m, 0));
        for(int i = 0, x = 0, y = 0, k = 0; i < n * m; i++) {
            res.push_back(matrix[x][y]);
            st[x][y] = 1;
            //(x, y) ===> (u,v)
            int u = x + dr[k][0];
            int v = y + dr[k][1];
            //如果下个位置越界，那么需要调整前进方向
            if(u < 0 || u >= n || v < 0 || v >=m || st[u][v]) {
                k = (k + 1) % 4;
                u = x + dr[k][0];
                v = y + dr[k][1];
            }
            //更新为新位置
            x = u, y = v;
        }
        return res;
    }
};
```
### 55.跳跃游戏[***](https://leetcode.cn/problems/jump-game/)
``` c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        //贪心做法
        for(int i = 0, j = 0; i < nums.size(); i++) {
            if(j < i) return false;
            j = max(j, i + nums[i]);
        }
        return true;
    }
};
``` 
### 56.合并区间[***](https://leetcode.cn/problems/merge-intervals/description/)
``` c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        //满足两个原则即可合并成功，
        //1. 按照区间左边界排序 2. 如果两个区间有交集，则前面区间的右边界大于后面区间的左边界
        vector<vector<int>> res;
        sort(intervals.begin(), intervals.end());
        int l = intervals[0][0], r = intervals[0][1];
        for(int i = 1; i < intervals.size(); i++) {
            // 无交集
            if(intervals[i][0] > r) {
                res.push_back({l, r});
                l = intervals[i][0], r = intervals[i][1];
            }
            else 
            // 有交集
                r = max(r, intervals[i][1]);
        }
        res.push_back({l, r});
        return res;
    }
};
```
### 57.插入区间[***](https://leetcode.cn/problems/insert-interval/)
``` c++
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& a, vector<int>& b) { 
        vector<vector<int>> res; 
        int k = 0;
        //找到第一个区间的有边界>b的左边界（有交集）
        while(k < a.size() && a[k][1] < b[0]) res.push_back(a[k++]);
        //b与区间进行合并
        if(k < a.size()) {
            b[0] = min(a[k][0], b[0]);
            int r = b[1];
            //a[k][0] == r时，左边界与新增区间右边界可以继续合并，找到第一个区间的左边界> b[1]（无交集）
            while(k < a.size() && a[k][0] <= r) b[1] = max(b[1], a[k++][1]); 
        }
        // k >= a.size()时，插入b
        res.push_back(b);
        while(k < a.size()) res.push_back(a[k++]);
        return res;
    }
};
```
### 58.最后一个单词的长度[*](https://leetcode.cn/problems/length-of-last-word/description/)
```c++
class Solution {
public:
    int lengthOfLastWord(string s) { 
            for(int i = s.size() - 1; i >= 0; i--) {
                if(s[i] == ' ') continue;
                int j = i - 1;
                while(j >= 0 && s[j] != ' ') j--;
                return i - j;
            }
            return 0; 
    }
};
```
### 59.螺旋矩阵II[***](https://leetcode.cn/problems/spiral-matrix-ii/description/)
```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        //螺旋矩阵模板
        vector<vector<int>> matrix(n, vector<int>(n, 0));
        int dr[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        for(int i = 0, x = 0, d = 0, y = 0; i < n * n; i++) {
            matrix[x][y] = i + 1;
            int u = x + dr[d][0];
            int v = y + dr[d][1];
            if(u < 0 || u >= n || v < 0 || v >= n || matrix[u][v]) {
                d = (d + 1) % 4;
                u = x + dr[d][0];
                v = y + dr[d][1];
            }
            x = u, y = v;
        }
        return matrix;
    }
};
```
### 60.排列序列[***](https://leetcode.cn/problems/permutation-sequence/description/)
```cpp
class Solution {
public:
    string getPermutation(int n, int k) {
        string res;
        bool st[10];
        //填充n位
        for(int i = 0; i < n; i++) {
            //填充第i位后，有(n - i - 1)!种情况
            int fact = 1;
            for(int j = 1; j < n - i; j++)
                fact *= j;
            //累加fact，直到找到k<=0
            for(int j = 0; j < n; j++)
                if(!st[j]) {
                    //没有达到k
                    if(k > fact) k -= fact;
                    else {
                        //找到填充的位数， 第i位填充的时'j+1'
                        res += to_string(j + 1);
                        st[j] = 1;
                        break;
                    }
                }
        }
        return res;
    }
};
```
### 61.循环链表[***](https://leetcode.cn/problems/rotate-list/)
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
    ListNode* rotateRight(ListNode* head, int k) {   
        int n = 0;
        for(ListNode *p = head; p; p = p->next) n++;
        if(!n) return head;
        //k可能大于n，对n取模
        k %= n;
        ListNode *pre = new ListNode(0, head);
        ListNode *p = pre ,*q = pre;
        //q先走n步
        for(int i = 0; i < k; i++) q = q->next;
        //p与q都一步步走
        while(q->next) {
            p = p->next;
            q = q->next;
        }
        //尾节点与头节点相连，构成循环链表
        q->next = head;
        //p的下个位置r就是循环右移的头节点
        ListNode *r = p->next;
        //p作为尾节点
        p->next = nullptr;
        return r;
    }
};
```
### 62.不同路径[****](https://leetcode.cn/problems/unique-paths/description/)
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> f(n, vector<int>(m, 0));
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++) {
                //一种方案
                if(!i && !j) f[i][j] = 1;
                //从上方过来
                if(i) f[i][j] += f[i - 1][j];
                //从左侧过来
                if(j) f[i][j] += f[i][j - 1];
            }
        return f[n - 1][m - 1];
    }
};
```

### 63.不同路径II[***](https://leetcode.cn/problems/unique-paths-ii/description/)
```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& ob) {
        int n = ob.size(), m = ob[0].size();
        vector<vector<int>>f(n, vector<int>(m, 0));
        for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++) {
            if(ob[i][j]) continue;
            if(!i && !j) f[i][j] = 1;
            if(i) f[i][j] += f[i - 1][j];
            if(j) f[i][j] += f[i][j - 1];
        }
        return f[n - 1][m - 1];
    }
};
```
### 64.最小路径和[***](https://leetcode.cn/problems/minimum-path-sum/description/)
```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int n = grid.size(); 
        if(!n) return 0;
        int m = grid[0].size();
        //将f初始化最大值，通过min找到最小值和
        vector<vector<int>> f(n, vector<int>(m, INT_MAX));
        for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++) { 
            if(!i && !j) f[i][j] = grid[i][j];
            else {
                //从上方
                if(i) f[i][j] = min(f[i][j], f[i - 1][j] + grid[i][j]);
                //从左方
                if(j) f[i][j] = min(f[i][j], f[i][j - 1] + grid[i][j]);
            }
        }
        return f[n - 1][m - 1];
    }
};
```
### 65.有效的数据[***](https://leetcode.cn/problems/valid-number/description/)
```cpp
class Solution {
public:
    bool isNumber(string s) {
        int l = 0, r = s.size() -1;
        //去掉前后多余空格
        while(l <= r && s[l] == ' ') l++;
        while(l <= r && s[r] == ' ') r--;
        if(l > r) return false;
        s = s.substr(l, r - l + 1);
        if(s[0] == '+' || s[0] == '-') 
            s = s.substr(1); 
        if(s.empty()) return false;
        //只有一个'.'或者后面为'e','E'
        if(s[0] == '.' && (s.size() == 1 || s[1] == 'e' || s[1] == 'E')) 
            return false;
        int dot = 0, e = 0;
        for(int i = 0; i < s.size(); i++)
            if(s[i] == '.') { 
                //'.'只有一个e后面不能出现'.'
                if(dot > 0 || e > 0) return false;
                dot++;
            }
            else if(s[i] == 'e' || s[i] == 'E') { 
                //e的前或后没有数据，e的次数不为1
                if(!i || i + 1 == s.size() || e > 0) return false;
                //e的后面符号后面没有数据
                if(s[i + 1] == '+' || s[i + 1] == '-') {
                    if(i + 2 == s.size()) return false;
                    i++;
                } 
                e++;
                }
                //出现其他非法数据
                else if(s[i] < '0' || s[i] > '9') return false; 
        return true;
    }
};
```
### 66.加一[**](https://leetcode.cn/problems/plus-one/description/)
```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) { 
        int t = 1; 
        vector<int> res;
        for(int i = digits.size() -1; i >= 0; i--) {
            int x = (digits[i] + t) / 10;
            res.push_back((digits[i] + t) % 10);
            t = x;
        }
        while(t) {
            res.push_back(t % 10);
            t /= 10;
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```
### 67.二进制求和[***](https://leetcode.cn/problems/add-binary/description/)
```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        vector<int> A,B, C;
        for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
        for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
        for(int i = 0, t = 0; t || i < a.size() || i < b.size(); i++) {
            if(i < a.size()) t += A[i];
            if(i < b.size()) t += B[i];
            C.push_back(t % 2);
            t /= 2;
        }
        string res;
        for(int i = C.size() - 1; i >= 0; i--) 
            res += '0' + C[i];
        return res;
    }
};
```
### 68.左右文本对齐[***](https://leetcode.cn/problems/text-justification/description/)
```cpp
class Solution {
public:
    vector<string> fullJustify(vector<string>& words, int maxWidth) {
        vector<string> res;
        for(int i = 0; i < words.size(); i++) {
            int j = i + 1;
            int len = words[i].size();
            //单词+单词之间的空格不能超过maxWidth或者单词用完了
            while(j < words.size() && len + 1 +words[j].size() <= maxWidth) {
                len += 1 + words[j].size();
                j++;
            }
            string line;
            //左对齐：单词用完了（最后一行）或者只有一个单词
            if(j == words.size() || i + 1 == j) {
                line += words[i];
                for(int k = i + 1; k < j; k++)  
                    line += ' ' + words[k];
                while(line.size() < maxWidth) line += ' ';
            }
            //左右对齐，计算有多少个间隙，每个间隙放多少空格，前面间隙空格数大于后面间隙空格数
            else{
                //cnt是间隙数，r是总的空格数量
                int cnt = j - i - 1, r = maxWidth - len + cnt;
                //第一个单词直接放入，之后的是空隙+单词
                line += words[i];
                int k = 0;
                //第一个单词之后的r%cnt个单词的间隙空格数是r/cnt+1，多了一个空格
                while(k < r % cnt) line += string(r / cnt + 1, ' ') + words[i + k + 1], k++;
                //剩余的单词前的间隙空格数是r/cnt
                while(k < cnt) line += string(r / cnt, ' ') + words[i + k + 1], k++;
            }
            res.push_back(line);
            //i跳过使用过的单词到j - 1
            i = j - 1;
        }
        return res;
    }
};
```
### 69.x的平方根[**](https://leetcode.cn/problems/sqrtx/description/)
```c++
//二分复杂度低
class Solution {
public:
    int mySqrt(int x) {
        int l = 0, r = x;
        while(l < r) {
            int mid = l + r + 1ll >> 1;
            if(mid <= x / mid) l = mid;
            else r = mid - 1;
        }
        return l;
    }
};
```
### 70.爬楼梯[***](https://leetcode.cn/problems/climbing-stairs/)
```c++
class Solution {
public:
    int climbStairs(int n) {
        //斐波拉契数列
        int a = 1, b = 1;
        //第n级阶梯需要迭代n - 1次
        for(int i = 0; i < n - 1; i++) {
            int c = a + b;
            a = b ;
            b = c;
        }
        return b;
    }
};
```
### 71.简化路径[***](https://leetcode.cn/problems/simplify-path/description/)
```cpp
class Solution {
public:
    string simplifyPath(string path) {
        //res时真实简略路径，name是当前路径文件名
        string res, name;
        //用"/"代表路径结束
        if(path.back() != '/') path += '/';

        for(auto c : path) {
            //不是"/"就将路径名或.存放到name中
            if(c != '/') name += c;
            else {
                //上一路径名为..表示返回父目录
                if(name == "..") {
                    //将res存储的当前路径删除直到/
                    while(res.size() && res.back() != '/') res.pop_back();
                    //再将/删除
                    if(res.size()) res.pop_back();
                }
                //  ./表示当前目录， //是/，两个都可以忽略  
                //不是这两种情况时，可以将/前的name存储到res中
                else if(name != "." && name != "") {
                    res += '/' + name;
                }
                name.clear();
            }
        }
        //res为空，则返回根目录
        if(res.empty()) res = "/";
        return res;
    }
};
```
### 72.编辑距离[***](https://leetcode.cn/problems/edit-distance/description/)
```cpp
class Solution {
public:
    int minDistance(string a, string b) { 
        int n = a.size(), m = b.size();
        a = " " + a;
        b = " " + b;
        // f[i][j]表示将A[1~i]变成B[1~j]的按顺序最小操作次数
        vector<vector<int>> f(n + 1, vector<int>(m + 1));
        //将a的前i个变成0个，需要i步
        for(int i = 0; i <= n; i++) f[i][0] = i;
        for(int j = 0; j <= m; j++) f[0][j] = j;
        //f[i][j]是a的前i个变成b的前j个
        for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++) {
            //删除或插入, f[i-1][j]删除a中第i个或者b中插入第j+1个, f[i][j-1]:插入a中第i+1个或者b中删除第j个
            f[i][j] = min(f[i - 1][j], f[i][j - 1]) + 1;
            //如果a[i] == b[j]，则不需要修改，否则要修改一次
            int t = a[i] != b[j];
            f[i][j] = min(f[i][j], f[i - 1][j - 1] + t);
        }
        return f[n][m];
    }
};
```
### 73.矩阵置零[***](https://leetcode.cn/problems/set-matrix-zeroes/description/)
```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        if(matrix.empty() || matrix[0].empty()) return;
        int n = matrix.size(), m = matrix[0].size();
        //row，col分别表示第一行、第一列是否刷0
        int row = 0, col = 0;
        //第一列刷0
        for(int i = 0; i < n; i++)
            if(!matrix[i][0]) col = 1;
        //第一行刷0
        for(int i = 0; i < m; i++)
            if(!matrix[0][i]) row = 1;
        //除去第一行和第一列，matrix[i][0]用来判断第i行是否刷0, matrix[0][j]用来判断第j列是否刷0
        for(int i = 1; i < n; i++)
        for(int j = 1; j < m; j++)
            if(!matrix[i][j]) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
       
        //先对除去第一行、第一列外的刷0
        for(int i = 1; i < n; i++)
            if(!matrix[i][0])
                for(int j = 1; j < m; j++) 
                    matrix[i][j] = 0;

        for(int i = 1; i < m; i++)
            if(!matrix[0][i]) 
                for(int j = 1; j < n; j++)
                    matrix[j][i] = 0;
        //第一行或第一列归0操作应放在最后执行，不然全为0
        if(row == 1)
            for(int i = 0; i < m; i++) matrix[0][i] = 0;
        if(col == 1)
            for(int i = 0; i < n; i++) matrix[i][0] = 0;
    }
};
```
### 74.搜索二维矩阵[****](https://leetcode.cn/problems/search-a-2d-matrix/)
``` c++
//o(n)做法
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int i = 0;
        while(i < matrix.size() && matrix[i][0] <= target) i++; 
        if(!i) return false;
        i--;
        for(int j = 0; j < matrix[i].size(); j++)
            if(matrix[i][j] == target) 
                return true;
        return false;
    }
};
```
```cpp
class Solution {
//二分做法
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if(matrix.empty() || matrix[0].empty()) return false;
        int n = matrix.size(), m = matrix[0].size();
        int l = 0, r = n * m - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(matrix[mid / m][mid % m] >= target) r = mid;
            else l = mid + 1;
        }
        return matrix[l / m][l % m] == target;
    }
};
```
### 75.颜色分类[***](https://leetcode.cn/problems/sort-colors/description/)
```c++
//三指针
class Solution {
public:
    void sortColors(vector<int>& nums) {
        // 0 ~ j-1表示的是0，  j ~ i-1表示的是1,   k+1 ~ nums.size()-1表示的是2
        for(int i = 0, j = 0, k = nums.size() - 1; i <= k;) {
            if(nums[i] == 0) {
                swap(nums[i], nums[j]);
                i++, j++;
            }
            else if(nums[i] == 2) {
                swap(nums[i], nums[k]);
                k--;
            }
            else i++;
        }
    }
};
```

### 76.最小字符字串[****](https://leetcode.cn/problems/minimum-window-substring/)
```c++
class Solution {
public:
    string minWindow(string s, string t) {
        //双指针，滑动窗口找包含t最小字串
        string res;
        //使用两个哈希表，ht记录字符串t中各字符出现的次数，wd记录窗口内字符串个字符出现次数
        unordered_map<char,int> ht, wd;
        for(auto c: t) ht[c]++;
        int cnt = 0;
        for(int i = 0, j = 0; i < s.size(); i++) {
            //队尾添加
            wd[s[i]]++;
            //判别队尾字符是否能引起t的字符总数
            if(wd[s[i]] <= ht[s[i]]) cnt++;
            //队首删除
            while(wd[s[j]] > ht[s[j]]) wd[s[j++]]--;
            //此时，窗口内包含字符串t的所有字符
            if(cnt == t.size()) {
                if(res.empty() || i - j + 1 < res.size()) 
                    res = s.substr(j, i - j + 1);
            }
        }
        return res;

    }
};
```
### 77.组合[***](https://leetcode.cn/problems/combinations/description/)
```c++
class Solution {
public: 
    vector<vector<int>> res;
    vector<int> path;
    //定义start，规定一个顺序不能选重复的，只能往后面选
    void dfs(int n, int k, int start) {
        if(!k) res.push_back(path);
        for(int i = start; i < n; i++) {
            path.push_back(i + 1);
            dfs(n, k - 1, i + 1);
            path.pop_back();
        }
    }
    vector<vector<int>> combine(int n, int k) {
        dfs(n, k, 0);
        return res;
    }
};
```
### 78.子集[***](https://leetcode.cn/problems/subsets/description/)
```c++
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void dfs(vector<int>& nums, int u) {
        if(u == nums.size()) {
            res.push_back(path);
            return;
        }
        //不选nums[u]
        dfs(nums, u + 1);
        //选nums[u]
        path.push_back(nums[u]);
        dfs(nums, u + 1);
        path.pop_back();
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(nums, 0);
        return res;
    }
};
```
### 79.单词搜索[***](https://leetcode.cn/problems/word-search/description/)
```c++
class Solution {
public:
    int n, m;
    int dr[4][2] = {{1,0}, {-1,0}, {0,1},{0,-1}};
    vector<vector<bool>> st;
    bool dfs(vector<vector<char>>& board, int i, int j, int u, string word) {
        // if(u == word.size())  return true;
        st[i][j] = 1;
        // 用于第一次判断
        if(word[u] != board[i][j]) return false;
        if(u == word.size() - 1) return true;
        for(int k = 0; k < 4; k++) {
            int x = i + dr[k][0], y = j + dr[k][1];
            // 在这里判断 board[x][y] != word[u + 1], 是防止u无序增长
            if(x < 0 || x >= n || y < 0 || y >= m || st[x][y] ||  board[x][y] != word[u + 1]) continue;
            if(dfs(board, x, y, u + 1, word)) return true;
        }
        // 要恢复现场
        st[i][j] = 0;
        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        if(board.empty() || board[0].empty()) return false;
        n = board.size(), m = board[0].size();
           
        for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++){
            st = vector<vector<bool>>(n + 1, vector<bool>(m + 1, 0));
            if(dfs(board, i, j, 0, word)) return true;
        }
        return false;
    }
};
```

### 80.删除有序数组中的重复项II[*](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int k = 0;
        for(int i = 0; i < nums.size(); i++) {
            int j = i + 1;
            while(j < nums.size() && nums[j] == nums[i]) j++;
            nums[k++] = nums[i];
            if(j - i > 1) nums[k++] = nums[i];
            i = j - 1; 
        }
        nums.resize(k);
        return k;
    }
};
```
### 81.搜索旋转排序数组II[***](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)
```cpp
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() -1;
        //去掉队尾与队首相同的元素
        while(r >= 0 && nums[r] == nums[0]) r--;
        int R = r;
        //所有的元素都相同
        if(r < 0) return nums[0] == target;
        //找到左递增序列与右序列的中介点
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(nums[mid] >= nums[0]) l = mid;
            else r = mid - 1;
        }
        //target在左序列中
        if(target >= nums[0]) r = l, l = 0;
        //target在右序列中
        else  
            l++, r = R;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        //不用nums[l] == target，因为上面的l++,可能会让l越界
        return nums[r] == target;  
    }
};
```
### 82.删除排序链表中的重复元素II[***](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)
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
    ListNode* deleteDuplicates(ListNode* head) {   
        ListNode *pre = new ListNode();
        pre->next = head;
        ListNode *p = pre, *r;
        while(p->next) {
            ListNode *q = p->next;
            //找到跳过与p->val相等的节点
            while(q->next && q->next->val == p->next->val) q = q->next;
            //有重复的节点
            if(q != p->next) p->next = q->next;
            //没有重复的节点
            else p = p->next;
        }
        return pre->next;
    }
};
```
### 83.删除排序链表中的重复元素[***](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)
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
    ListNode* deleteDuplicates(ListNode* head) { 
        ListNode *pre = new ListNode();
        pre->next = head;
        ListNode *p = pre, *r;
        while(p->next) {
            ListNode *q = p->next;
            while(q->next && q->next->val == p->next->val) q = q->next;
            if(q != p->next) p->next = q;
            p = p->next;
        }
        return pre->next;
    }
};
```
### 84.柱状图中最大的矩形[*****](https://leetcode.cn/problems/largest-rectangle-in-histogram/description/)
```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& h) {
        int n = h.size();
        vector<int> left(n), right(n);
        stack<int> stk;
        //单调栈，找到左边第一个比h[i]小的位置
        for(int i = 0; i < n; i++) {
            //构建单调栈，把栈中>=h[i]的元素删除
            while(stk.size() && h[stk.top()] >= h[i]) stk.pop();
            if(stk.empty()) left[i] = -1;
            else left[i] = stk.top();
            stk.push(i);
        }
        stk = stack<int>();
        //单调栈，找到右边第一个比h[i]小的位置
        for(int i = n - 1; i >= 0; i--) {
            while(stk.size() && h[stk.top()] >= h[i]) stk.pop();
            if(stk.empty()) right[i] = n;
            else right[i] = stk.top();
            stk.push(i);
        }
        int res = 0;
        for(int i = 0; i < n; i++)  
            res = max(h[i]*(right[i] - left[i] - 1), res); 
        return res;
    }
};
```
### 85.最大矩形[*****](https://leetcode.cn/problems/maximal-rectangle/)
```cpp
class Solution {
public:
     int largestRectangleArea(vector<int>& h) {
        int n = h.size();
        vector<int> left(n), right(n);
        stack<int> stk;
        //单调栈，找到左边第一个比h[i]小的位置
        for(int i = 0; i < n; i++) {
            //构建单调栈，把栈中>=h[i]的元素删除
            while(stk.size() && h[stk.top()] >= h[i]) stk.pop();
            if(stk.empty()) left[i] = -1;
            else left[i] = stk.top();
            stk.push(i);
        }
        stk = stack<int>();
        //单调栈，找到右边第一个比h[i]小的位置
        for(int i = n - 1; i >= 0; i--) {
            while(stk.size() && h[stk.top()] >= h[i]) stk.pop();
            if(stk.empty()) right[i] = n;
            else right[i] = stk.top();
            stk.push(i);
        }
        int res = 0;
        for(int i = 0; i < n; i++)  
            res = max(h[i]*(right[i] - left[i] - 1), res); 
        return res;
    }
    int maximalRectangle(vector<vector<char>>& matrix) {
        //可以把矩阵分成n行，求各行到第1行的“1”的个数最大的矩形
        if(matrix.empty() || matrix[0].empty()) return 0;
        int n = matrix.size(), m = matrix[0].size();
        vector<vector<int>> h(n, vector<int>(m, 0));
        for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++)
            if(matrix[i][j] == '1') {
                if(i) h[i][j] = 1 + h[i - 1][j];
                else h[i][j] = 1;
            }
        int res = 0;
        for(int i = 0; i < n; i++) 
            res = max(res, largestRectangleArea(h[i]));
        return res;
    }
};
```
### 86.分隔链表[***](https://leetcode.cn/problems/partition-list/description/)
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
    ListNode* partition(ListNode* head, int x) { 
        ListNode *l1 = new ListNode(), *l2 = new ListNode();
        ListNode *u = l1, *v = l2;
        for(ListNode *p = head; p; p = p->next)  
            if(p->val < x) {
                u->next = p;
                u = u->next;
            }
            else {
                v->next = p;
                v = v->next;
            }
        u->next = l2->next;
        v->next = nullptr;
        return l1->next; 
    }
};
```
### 87.扰乱字符串
```cpp
class Solution {
public:
    bool isScramble(string s1, string s2) {
        int n = s1.size();
        vector<vector<vector<bool>>> f(n, vector<vector<bool>>(n, vector<bool>(n + 1)));
        //f[i][j][k]表示s1的i开始的k个字符与s2的j开始的k个字符是否匹配
        for(int k = 1; k <= n; k++)
            for(int i = 0; i + k - 1 < n; i++)
                for(int j = 0; j + k - 1< n; j++) 
                    //匹配长度为1，判断两字符是否相等
                    if(k == 1)  f[i][j][k] = s1[i] == s2[j];
                    else {
                        //k>1，对集合进行划分，匹配分两种情况，1.交换位置 2.没有交换位置
                        for(int u = 1; u < k; u++) 
                            //i和j开始的u个字符匹配且i+u和j+u开始的k-u个字符匹配 || i和j+k-u开始的u个字符匹配且i+u和j开始的k-u个字符匹配
                            if(f[i][j][u] && f[i + u][j + u][k - u] || f[i][j + k - u][u] && f[i + u][j][k - u]) {
                                f[i][j][k] = true; 
                                break;
                            }
                    }
        return f[0][0][n];
    }
};
```
### 88.合并两个有序数组[**](https://leetcode.cn/problems/merge-sorted-array/)
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        //从后向前插入
        int i = m - 1, j = n - 1, k = n + m - 1;
        while(i >= 0 && j >= 0) {
            if(nums1[i] >= nums2[j])  
                nums1[k--] = nums1[i--];
            else 
                nums1[k--] = nums2[j--];
        } 
        while(i >= 0) nums1[k--] = nums1[i--];
        while(j >= 0) nums1[k--] = nums2[j--];
        
    }
};
```

### 89.各类编码[**](https://leetcode.cn/problems/gray-code/)
```cpp
class Solution {
public:
    vector<int> grayCode(int n) { 
        vector<int> res(1, 0);
        while(n--) {
            //新的格雷码是原来轴对称的左移1位，再加1
            for(int i = res.size() - 1; i >= 0; i--) {
                res[i] *= 2;
                res.push_back(res[i] + 1);
            }
        }
        return res;
    }
};
```

### 90.子集II[****](https://leetcode.cn/problems/subsets-ii/description/)
```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void dfs(vector<int>& nums, int u) {
        if(u == nums.size()) {
            res.push_back(path);
            return;
        } 
        int k = u + 1;
        while(k < nums.size() && nums[k] == nums[u]) k++;
        //总共k-u个nums[u]
        //i=0时，没有选择nums[u], 选择i个nums[u]加入集合
        for(int i = 0; i <= k - u; i++) {
            dfs(nums, k);
            path.push_back(nums[u]);
        }
        //恢复现场
        for(int i = 0; i <= k - u; i++)
            path.pop_back();
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return res;
    }
};
```
### 91.解码方法[***](https://leetcode.cn/problems/decode-ways/description/)
```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        s = " " + s;
        vector<int>f(n + 1);
        f[0] = 1;
        //f[i]分为两类，f[i-1]和f[i-2]
        for(int i = 1; i <= n; i++) {
            if(s[i] >= '1' && s[i] <= '9')  f[i] += f[i - 1];
            if(i > 1) {
                int t = (s[i - 1] - '0') * 10 + s[i] - '0';
                if(t >= 10 && t<= 26)
                    f[i] += f[i - 2];
            }
        }
        return f[n];
    }
};
```
### 92.反转链表II[***](https://leetcode.cn/problems/reverse-linked-list-ii/description/)
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
    ListNode* reverseBetween(ListNode* head, int left, int right) {  
        ListNode *pre = new ListNode();
        pre->next = head;
        ListNode *p = pre;
        for(int i = 0; i < left - 1; i++) p = p->next;
        ListNode *r = p->next, *q = p->next;
        //将left - 1后的链表断开
        p->next = NULL;
        //头插法进行旋转
        for(int i = 0; i < right - left + 1; i++) {
            ListNode *u = q->next;
            q->next = p->next;
            p->next = q;
            q = u;
        }
        //将right后链表缝补上
        r->next = q;
        return pre->next;
    }
};
```
### 93.复原IP地址[***](https://leetcode.cn/problems/restore-ip-addresses/description/)
```cpp
class Solution {
public:
    vector<string> res;
    void dfs(string &s, int u, int k, string path) {
        if(u == s.size()) {
            if(k == 4) {
                //删除最后一个'.'
                path.pop_back();
                res.push_back(path);
            }
            return;
        }
        //剪枝处理
        if(k == 4) return;
        for(int i = u, t = 0; i < s.size(); i++) {
            //不能有前导0
            if(i > u && s[u] == '0') break;
            t = 10 * t + s[i] - '0';
            //<=255时，递归继续找
            if(t <= 255) dfs(s, i + 1, k + 1, path + to_string(t) + ".");
            else break;
        }
    }
    vector<string> restoreIpAddresses(string s) {
        string path;
        //递归找到这4个数
        dfs(s, 0, 0, path);
        return res;
    }
};
```
### 95.不同的二叉搜索树II[****](https://leetcode.cn/problems/unique-binary-search-trees-ii/)
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
    vector<TreeNode*> dfs(int l, int r) {
        if(l > r) return {NULL};
        vector<TreeNode*> res;
        //从l到r中选一个节点作为根节点，[l, i-1]分配左子树，[i+1,r]分配给右子树
        for(int i = l; i <= r; i++) { 
            //左子树子集
            auto left = dfs(l, i - 1);
            //右子树子集
            auto right = dfs(i + 1, r);
            for(auto l : left)
            for(auto r : right) {
                //挑选一个左子树和右子树构成树，总共有left*right种情况
                TreeNode *root = new TreeNode(i);
                root->left = l;
                root->right = r;
                res.push_back(root);
            }
        }
        return res;
    }
    vector<TreeNode*> generateTrees(int n) { 
        if(!n) return {NULL};
        //1 ~ n构成二叉排序树
        return dfs(1, n);
    }
};
```
### 96.不同的二叉搜索树[**](https://leetcode.cn/problems/unique-binary-search-trees/)
```cpp
class Solution {
public:
    int numTrees(int n) { 
        vector<int>f(n + 1);
        f[0] = 1;
        for(int i = 1; i <= n; i++) 
            for(int j = 1; j <= i; j++)
                f[i] += f[j - 1] * f[i - j];
        return f[n];
    }
};
```

### 97.交错字符串[***](https://leetcode.cn/problems/interleaving-string/)
```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int n = s1.size(), m = s2.size();
        if(s3.size() != n + m) return false;
        s1 = " " + s1, s2 = " " + s2, s3 = " " + s3;
        //f[i][j]=true表示s1前i个和s2前j个是交错字符串，如果f[i-1][j] && s1[i]==s3[i+j] || f[i][j-1] && s2[j]==s3[i+j]
        vector<vector<bool>> f(n + 1, vector<bool>(m + 1, 0));
        for(int i = 0; i <= n; i++)
            for(int j = 0; j <= m; j++) {
                if(!i && !j) f[i][j] = 1;
                else {
                    if(i && s1[i] == s3[i + j]) 
                        f[i][j] = f[i - 1][j];
                    if(j && s2[j] == s3[i + j])
                        f[i][j] = f[i][j] || f[i][j - 1];
                } 
            }
        return f[n][m];
    }
};
```
### 98.验证二叉搜索树[***](https://leetcode.cn/problems/validate-binary-search-tree/)
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
    bool dfs(TreeNode* root, long minv, long maxv) {
        if(!root) return true;
        if(root->val <= minv || root->val >= maxv) return false;
        return dfs(root->left, minv, root->val) && dfs(root->right, root->val, maxv);
    }
    bool isValidBST(TreeNode* root) {  
        return dfs(root, LONG_MIN, LONG_MAX);
    }
};
```
### 99.恢复二叉搜索树[****](https://leetcode.cn/problems/recover-binary-search-tree/description/)
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
    //为了保证空间复杂度0（1），使用了Moriis方法
    //交换第一个逆序对的前一个 与 第二个逆序对的后一个，即可恢复二叉搜索树
    void recoverTree(TreeNode* root) { 
        TreeNode *first = NULL, *second = NULL, *last = NULL;
        while(root) {
            //没有左孩子
            if(!root->left) {
                //上一节点的值大于当前节点的值，出现逆序对
                if(last && last->val > root->val) {
                    //是第一个逆序对
                    if(!first) first = last, second = root;
                    //第二个逆序对
                    else second = root;
                }
                last = root;
                root = root->right;
            }
            else {
                auto p = root->left;
                //找到根的左子树最后一个节点
                while(p->right && p->right != root) p = p->right;
                //将左子树最后一个节点的后继设为root，然后遍历root的左子树
                if(!p->right) p->right = root, root = root->left;
                else {
                    //已经遍历完左子树，再将后继线索断开
                    p->right = NULL;
                    if(last && last->val > root->val) {
                    if(!first) first = last, second = root;
                    else second = root;
                }
                last = root;
                root = root->right;
                }
            }
        }
        swap(first->val, second->val);
    } 
};
```