### 151.翻转字符串里的单词[***](https://leetcode.cn/problems/reverse-words-in-a-string/)
```cpp
class Solution {
public:    
    string reverseWords(string s) {
        int k = 0;
        for(int i = 0; i < s.size(); i++) {
            if(s[i] == ' ') continue;
            int j = i, t = k;
            //非空格字符保留
            while(j < s.size() && s[j] != ' ') s[k++] = s[j++];
            //单词翻转
            reverse(s.begin() + t, s.begin() + k);
            s[k++] = ' ';
            i = j;
        }
        //减一是上面多加了1
        if(k) k--;
        //把后面的空格去掉
        s.erase(s.begin() + k, s.end());
        //整体再翻转就是翻转字符串中单词
        reverse(s.begin(), s.end());
        return s;
    }
};
```
### 152.乘积最大子数组[****](https://leetcode.cn/problems/maximum-product-subarray/description/)
```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int f = nums[0], g = nums[0], res = nums[0];
        for(int i = 1; i < nums.size(); i++) {
            int a = nums[i], fa = f * a, ga = g * a;  
            // a > 0当前最大值为fa， a<0当前最大值为ga， a=0，最大值为0
            //最小值同理
            f = max(a, max(fa, ga));
            g = min(a, min(fa, ga));
            res = max(res, f);
        }
        return res;
    }
};
```
### 153.寻找排序数组中的最小值[***](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/description/)
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + r >> 1;
            //比较的右端点，有序的情况不用特判
            //找到比nums[r]小的最小值
            if(nums[mid] < nums[r]) r = mid;
            else l = mid + 1;
        }
        return nums[r];
    }
};
```
### 154.寻找排序数组中的最小值II[*****](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/description/)
```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        //把末尾中（和开头相同的数）删掉，方便我们找到，在右侧的最小值
        while(l < r && nums[0] == nums[r])  r--;
        if(nums[l] <= nums[r]) return nums[l];
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] < nums[0]) r = mid;
            else l = mid + 1;
        }
        return nums[l];
    }
};
```
### 155.最小栈[****](https://leetcode.cn/problems/min-stack/)
```cpp
class MinStack {
public:
    stack<int> stk, f;
    MinStack() { 
    }
    //f维护栈顶每次取出的是最小值
    void push(int val) {
        stk.push(val);
        if(!f.size() || f.top() >= val) f.push(val);
    }
    
    void pop() { 
        if(f.top() == stk.top()) f.pop();
        stk.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return f.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

### 160.相交链表[****](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/)
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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {  
        ListNode *p = headA, *q = headB;
        while(p != q) {
            p = p ?  p->next : headB;
            q = q ? q->next : headA;
        }
        return p;
    }
};
```
### 161.寻找峰值[****](https://leetcode.cn/problems/find-peak-element/)
```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + r >> 1;
            //因为左右边界都是负无穷，所以当nums[i] > nums[i + 1]时，则峰值一定在左区间，否则峰值在右区间
            if(nums[mid] > nums[mid + 1]) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```
### 164.最大间距[*****](https://leetcode.cn/problems/maximum-gap/description/)
```cpp
class Solution {
public:
    int maximumGap(vector<int>& nums) {
        struct Range{
            //该桶的最大值和最小值
            int min, max;
            //该桶是否有值
            bool used;
            Range():min(INT_MAX), max(INT_MIN), used(false){}
        };
        int n = nums.size();
        int Min = INT_MAX, Max = INT_MIN;
        for(auto num : nums) {
            Min = min(Min, num);
            Max = max(Max, num);
        }
        if(n < 2 || Min == Max) return 0;
        //最多有n-1个桶（区间）
        vector<Range>r(n - 1);
        //这是（Max - min - 1 )/ (n - 1)向上取整
        int len = (Max - Min + n - 2) / (n - 1);
        for(auto x : nums) {
            //区间是左开右闭
            if(x == Min)    continue;
            //找到是哪个桶
            int k = (x - Min - 1) / len;
            r[k].min = min(r[k].min, x);
            r[k].max = max(r[k].max, x);
            r[k].used = true;
        }
        int res = 0;
        //各数相邻的最大值一定是在桶间取得的（不可能在桶内取得相邻最大值，当前桶的最小值-上一个桶的最大值
        for(int i = 0, last = Min; i < n - 1; i++) 
            if(r[i].used) {
                res = max(res, r[i].min - last);
                last = r[i].max;
            }
        return res;
    }
};
```
### 165.比较版本号[****](https://leetcode.cn/problems/compare-version-numbers/)
```cpp
class Solution {
public:
    int compareVersion(string v1, string v2) {
        for(int i = 0, j = 0; i < v1.size() || j < v2.size(); ){
            int u = i, v = j;
            while(u < v1.size() && v1[u] != '.') u++;
            while(v < v2.size() && v2[v] != '.') v++;
            //截取版本号进行比较，直接通过stoi解决前导0，如果已经遍历完，则放置0
            int a = i < v1.size() ? stoi(v1.substr(i, u - i)) : 0;
            int b = j < v2.size() ? stoi(v2.substr(j, v - j)) : 0;
            if(a > b) return 1;
            if(a < b) return -1;
            i = u + 1;
            j = v + 1;
        }
        return 0;
    }
};
```
### 166.分数到小数[****](https://leetcode.cn/problems/fraction-to-recurring-decimal/description/)
```cpp
class Solution {
public:
    string fractionToDecimal(int numerator, int denominator) {
        typedef long long LL;
        unordered_map<int, int> hash;
        //相除可能会越界，用long long
        LL x = numerator, y = denominator;
        //x能被y整除
        if(x % y == 0) return to_string(x / y);
        string res;
        //相除结果为负号
        if((x < 0) ^ (y < 0)) res += '-';
        //转成整数运算
        x = abs(x), y = abs(y);
        res += to_string(x / y) + '.', x = x % y;
        //x是余数，当余数为0是结束
        while(x) {
            //记录余数为x时，字符串长度
            hash[x] = res.size();
            x *= 10;
            //添加商
            res += to_string(x / y);
            x %= y;
            //出现相同的余数，出现循环小数的情况
            if(hash.count(x)) {
                //0~hash[x]是循环小数之前的字符串，hash[x]后的是循环小数
                res = res.substr(0, hash[x]) + '(' + res.substr(hash[x]) + ')';
                break;
            }
        }
        return res;
    }
        
}; 
```
### 167.两数之和II - 输入有序数组[***](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/description/)
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        //双指针，时间复杂度O(n), i向右移动，j向左移动使得两值等于target
        for(int i = 0, j = nums.size() - 1; i < j; i++) {
            while(i < j && nums[i] + nums[j] >target) j--;
            if(nums[i] + nums[j] == target) return {i + 1, j + 1};
        } 
        return {1, 1};
    }
};
```
### 168.Excel表列名称[****](https://leetcode.cn/problems/excel-sheet-column-title/description/)
```cpp
class Solution {
public:
    string convertToTitle(int columnNumber) {
        int n = columnNumber;
        string res;  
        while(n) {
            //这一题相比于正常的数字转字符，就是加了1，所以在求各位字符时，可以先减一
            res += 'A' + (n - 1) % 26;
            //减了1之后，把低位给去掉，再转换下一个26
            n = (n - 1) / 26;
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```
### 169.多数元素[****](https://leetcode.cn/problems/majority-element/)
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        //r是当前的数，c是r的库存
        int r = 0, c = 0;
        //因为总有某数超过1半，那么最后输出的r一定是该数
        for(auto x : nums) {
            //库存为0，放入r，库存增1
            if(!c) r = x, c = 1;
            //和库存的数不相等，则库存减一
            else if(x != r) c--;
            else c++;
        }
        return r;
    }
};
```
### 171.Excel表列序号[***](https://leetcode.cn/problems/excel-sheet-column-number/description/)
```c++
class Solution {
public:
    int titleToNumber(string s) {
        int res = 0;
        for(auto x : s) {
            //这里不是+=
            res = res * 26 + (x - 'A' + 1);
        }
        return res;
    }
};
```
### 172.阶乘后的零[****](https://leetcode.cn/problems/factorial-trailing-zeroes/description/)
```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        int res = 0;
        //末尾0的个数，就是找10， 2和5的质因数最小值，5的质因数比2更小
        //n!的质因数p的个数为n/p, n/p^2, n/p^3, ... , n/p^k(p^k的倍数个数)
        //表示n/p有p, 2*p, 3*p, ... , n/p*p(p的倍数的个数) 
        while(n) {
            res += n / 5;
            n /= 5;
        }
        return res;
    }
};

### 173.二叉搜索树迭代器[****](https://leetcode.cn/problems/binary-search-tree-iterator/description/)
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
class BSTIterator {
public:
    stack<TreeNode*> stk;
    //使用中序非递归算法实现迭代
    BSTIterator(TreeNode* root) { 
        while(root) {
            stk.push(root);
            root = root->left;
        }
    }
    
    int next() { 
        auto p = stk.top();
        stk.pop();
        int val = p->val;
        p = p->right;
        while(p) {
            stk.push(p);
            p = p->left;
        }
        return val;
    }
    
    bool hasNext() {
        return stk.size();
    }
};

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator* obj = new BSTIterator(root);
 * int param_1 = obj->next();
 * bool param_2 = obj->hasNext();
 */
```
### 174.地下城游戏[*****](https://leetcode.cn/problems/dungeon-game/description/)
```c++
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& w) {
        int n = w.size(), m = w[0].size();
        vector<vector<int>> f(n,vector<int>(m, 1e9));
        //状态集合，f[i][j]i宝石从(i,j)到（n,m)的需要最小健康点数，若f[i][j]<0，则不需要健康点数置为1
        //状态转移方程，f[i][j] = min(f[i + 1][j] - w[i][j], f[i][j + 1][j] - w[i][j]);
        //f[i][j] - w[i][j] >= f[i + 1][j] 和 f[i][j + 1]
        for(int i = n - 1; i >= 0; i--)
        for(int j = m - 1; j >= 0; j--) {
            if(i == n - 1 && j == m - 1) f[i][j] = max(1, 1 - w[i][j]);     //最后的健康点数最小为1
            else {
                if(i < n - 1) f[i][j] = f[i + 1][j] - w[i][j];
                if(j < m - 1) f[i][j] = min(f[i][j], f[i][j + 1] - w[i][j]);
                cout << f[i][j]<<endl;
                f[i][j] = max(1, f[i][j]);
            }
        }
        return f[0][0];
    }
};
```
### 175.组合两个表[***](https://leetcode.cn/problems/combine-two-tables/description/)
```sql
# Write your MySQL query statement below
select FirstName, LastName, City, State 
from Person left join Address 
on Person.PersonId = Address.PersonId
# 左连接:left join，联接结果保留左表的全部数据
# 右连接：right join, 联结结果保留右表的全部数据
# 内联结：inner join， 取两表的公共数据
```

### 176.第二高的薪水[***](https://leetcode.cn/problems/second-highest-salary/description/)
```sql
# Write your MySQL query statement below
select 
(select distinct salary 
from Employee 
order by salary desc 
limit 1 , 1) as SecondHighestSalary;
# limit M, N 返回第M个位置开始的N个语句
# 再加一个select作为临时表，避免只有一个薪水的情况
```
### 177.第N高的薪水[*****](https://leetcode.cn/problems/nth-highest-salary/)
```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT;
    set M = N - 1;
  RETURN (
      # Write your MySQL query statement below.
   select distinct salary from Employee
    order by salary desc 
    # 从第M个位置返回第一行
    limit M, 1
    ); 
END
```
### 178.分数排名[***](https://leetcode.cn/problems/rank-scores/description/)
```sql
# Write your MySQL query statement below
select score,
 dense_rank() over(order by score desc ) as 'rank' from Scores;

 # 窗口函数dense_rank()符合题目的分数相等，排名相同
```
### 179.最大数[****](https://leetcode.cn/problems/largest-number/submissions/515923625/)
```c++
class Solution {
public:
    //字符串a+b>字符串b+a时，所有数字都按照此方法排序，最后结果时最大的
    //这个排序要满足全序关系，反对称性，传递性，完全性
    //sort的cmp函数得是静态的
    bool static cmp(int a, int b) {
        string u = to_string(a), v = to_string(b);
        return u + v > v + u;
    }
    string largestNumber(vector<int>& nums) {
        sort(nums.begin(), nums.end(), cmp);
        string res;
        for(auto x : nums)
            res += to_string(x);
        int k = 0;
        //去掉前导0，如果只有1个0则保留
        while(k + 1 < res.size() && res[k] == '0') k++;
        return res.substr(k);
    }
};
```
### 180.连续出现的数字[***](https://leetcode.cn/problems/consecutive-numbers/description/)
```sql
# Write your MySQL query statement below
select distinct a.num as ConsecutiveNums from Logs a, Logs b, Logs c
where a.id + 1 = b.id and b.id + 1 = c.id and a.num = b.num and b.num = c.num
### 3行的序号是连续的，3个值是相等的
```
### 181.超过经理收入的员工[****](https://leetcode.cn/problems/employees-earning-more-than-their-managers/description/)
```sql
# Write your MySQL query statement below
select a.name as Employee from Employee a, Employee b
where a.managerId = b.id
and a.salary > b.salary
# 员工比他所属部门经理的工资高
```
### 182.查找重复的电子邮箱[***](https://leetcode.cn/problems/duplicate-emails/submissions/515937251/)
```sql
# Write your MySQL query statement below
select email as Email from Person 
group by email having count(email) > 1
```

### 183.从不订购的客户[***](https://leetcode.cn/problems/customers-who-never-order/)
```sql
# Write your MySQL query statement below
select name as Customers from 
Customers a left join Orders b 
on a.Id = b.customerId 
where customerId is null
# 进行左连接，然后判断左连接后的customerId是否为空，为空，则不在右表中
```
### 184.部门工资最高的员工[****](https://leetcode.cn/problems/department-highest-salary/description/)
```sql
# Write your MySQL query statement below
select b.name as Department, a.name as Employee, Salary
from Employee a left join Department b
on a.departmentId = b.id
where (departmentId, salary) in
(select departmentId, max(salary) from Employee group by departmentId)

# 左连接输出
# where 条件 （部门号，薪水）与（部门号，部门最高薪水）匹配的
```
### 185.部门工资前三高的所有员工[*****](https://leetcode.cn/problems/department-top-three-salaries/submissions/515962252/)
```sql
# Write your MySQL query statement below
select b.name as Department, e1.name as Employee, salary 
from Employee e1 left join Department b
on e1.departmentId = b.id 
# 找到比e1该项薪水高的且只有两行符合的情况
where (
   select count(distinct e2.salary) from Employee e2
    where e1.salary < e2.salary and e1.departmentId = e2.departmentId
) < 3 order by e1.departmentId
```
### 187.重复的DNA序列[***](https://leetcode.cn/problems/repeated-dna-sequences/description/)
```c++
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        unordered_map<string, int> hash;
        for(int i = 0; i + 10 <= s.size(); i++) 
            hash[s.substr(i, 10)]++;
        vector<string> v;
        for(auto p : hash) 
            if(p.second > 1) 
                v.push_back(p.first);
        return v;
    }
};
```
### 188.买卖股票的最佳时机IV[*****](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/submissions/516014301/)
```c++
class Solution {
public:
    /*
    第k笔交易可以看作状态机模型(k < n / 2)
    f[i][j]表示已走i条边，且已经转了j圈的最大收益
    g[i][j]表示已走i条边，且正在转第j圈的最大收益
    */
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if(k >= n / 2) {
            //等价为可以进行无限次交易
            int res = 0;
            for(int i = 0; i + 1< prices.size(); i++)
                if(prices[i] < prices[i + 1])
                    res += prices[i + 1] - prices[i];
            return res;
        }
        vector<vector<int>> f(2, vector<int>(k + 1, -1e9));
        auto g = f;
        f[0][0] = 0;
        int res = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 0; j <= k;  j++) { 
                f[i & 1][j] = max(f[i - 1 & 1][j], g[i - 1 & 1][j] + prices[i - 1]);
                if(j == 0)  g[i & 1][j] = g[i - 1 & 1][j];
                else g[i & 1][j] = max(g[i - 1 & 1][j], f[i - 1 & 1][j - 1] - prices[i - 1]);
                res = max(res, f[i & 1][j]);
            }
        return res;
    }
};
```
### 189.轮转数组[***](https://leetcode.cn/problems/rotate-array/description/)
```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();    
        //数组循环右移，得先把K模N
        k %= n;
        reverse(nums.begin(), nums.begin() + n - k);
        reverse(nums.begin() + n - k, nums.end());
        reverse(nums.begin(), nums.end());
    }
};
```

### 190.颠倒二进制位[***](https://leetcode.cn/problems/reverse-bits/description/)
```c++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t res = 0;
        for(int i  = 0; i < 32; i++) {
            res = 2 * res + n % 2;
            n /= 2;
        }
        return res;
    }
};
```

### 191.位1的个数
```c++
class Solution {
public:
    int hammingWeight(int n) {
        int res = 0;
        while(n) {
            if(n % 2) res++;
            n /= 2;
        }
        return res;
    }
};
```

### 193.有效电话号码[****]()
```python
# Read from the file file.txt and output all valid phone numbers to stdout.
grep -P '^([0-9]{3}-|\([0-9]{3}\) )[0-9]{3}-[0-9]{4}$' file.txt 
```

### 196.删除重复的电子邮箱[***](https://leetcode.cn/problems/delete-duplicate-emails/description/)
```sql
# Write your MySQL query statement below
delete p1 from Person p1, Person p2
where p1.email = p2.email and p1.id > p2.id
```
### 197.上升的温度[****](https://leetcode.cn/problems/rising-temperature/)
```sql
# Write your MySQL query statement below
select w2.id from weather w1, weather w2
where datediff(w2.recorDate, w1.recorDate) = 1 and w2.Temperature > w1.Temperature
```

### 198.打家劫舍[****](https://leetcode.cn/problems/house-robber/description/)
```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n + 1), g(n + 1);
        //f[i]在1~i中必选第i个
        //g[i]在1~i中不选第i个
        for(int i = 1; i <= n; i++) {
            //不能取相邻的现金，所以不能选i-1位置的现金
            f[i] = g[i - 1] + nums[i - 1];
            g[i] = max(f[i - 1], g[i - 1]);
        }
        return max(f[n], g[n]);
    }
};
```
### 199.二叉树的右视图[****](https://leetcode.cn/problems/binary-tree-right-side-view/)
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
    vector<int> rightSideView(TreeNode* root) {
        vector<int> v;
        if(!root) return v;
        queue<TreeNode*> q;
        q.push(root);
        
        while(q.size()) {
            int n = q.size();
            for(int i = 0; i < n; i++) {
                auto p = q.front();
                if(i == n- 1) v.push_back(p->val);
                q.pop();
                if(p->left) q.push(p->left);
                if(p->right) q.push(p->right);
            }
        }
        return v;
    }
};
```
### 200.岛屿数量[****](https://leetcode.cn/problems/number-of-islands/)
```c++
class Solution {
public:  
    int n, m;
    int dr[4][2] = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    void dfs(vector<vector<char>>& grid, int x, int y) {
        grid[x][y] = '0';
        for(int i = 0; i < 4; i++) {
            int u = x + dr[i][0], v = y + dr[i][1];
            if(u < 0 || u >= n || v < 0 || v >= m || grid[u][v] == '0') continue;
            dfs(grid, u, v);
        }
    }
    int numIslands(vector<vector<char>>& grid) {
        int cnt = 0;
        n = grid.size();
        m = grid[0].size();
        for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++)
            if(grid[i][j] == '1') {
                dfs(grid, i, j);
                cnt++;
            }
        return cnt;                
    }
};
```
