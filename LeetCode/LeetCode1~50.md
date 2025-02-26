---
    title: LeetCode 1-50
    date: 2023-01-08 20:21:00
    tags: LeetCode
---

### 1.两数之和[*](https://leetcode.cn/problems/two-sum/)
    哈希表插入和查询操作复杂度都是0（1）
``` c++
class Solution {
public:
    unordered_map<int,int>p;
    vector<int> twoSum(vector<int>& nums, int target) {
        for(int i = 0; i < nums.size(); i++) { 
            int r = target - nums[i];
            if(p.count(r)) return {p[r], i};
            p[nums[i]] = i;
        } 
        return {};
    }
};
```
### 2.两数相加[**](https://leetcode.cn/problems/add-two-numbers/)
    两个链表按逆序相加
``` c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
      ListNode *p = l1, *q = l2;
      ListNode *pre = new ListNode(), *r = pre;
      int t = 0, a , b;
      while(p || q || t) 
      {
          a = p ? p->val : 0;
          b = q ? q->val : 0;
          if(p) p = p->next;
          if(q) q = q->next;
          t += a + b;
          r->next = new ListNode(t % 10);
          t /= 10;
          r = r->next;
      }
      return pre->next;
    }
};
```
### 3.无重复字符的最长字串[***](https://leetcode.cn/problems/longest-substring-without-repeating-characters/) 
    
    双指针扫描O(n), 定义两个指针i,j(i<=j), [i, j]
    1. 指针j向后移一位，同时将哈希表s[j]的计数加1
    2. 假设j移动前的区间[i,j]中没有重复字符，则j移动后，只有s[j]可能出现2次，因此我们不断向后移动i, 直至区间[i,j]中s[j]的个数为1
   **注意字串和子序列的区别：字串一定是连续的一段， 子序列不一定是连续的一段，但下标是递增的**

``` c++
 int lengthOfLongestSubstring(string s) { 
        unordered_map<char, int> p;
        int res = 0; 
        for(int i = 0, j = 0; i < s.size(); i++) {
            p[s[i]] ++;         // j可以向前移动p[s[i]] - 1步
            while(p[s[i]] > 1) p[s[j++]] --;    //遇到连续重复的序列，j要右移   
            res = max(res, i - j + 1);
        }
        return res;
    }
```

### 4.寻找两个正序数组的中位数[*****](https://leetcode.cn/problems/median-of-two-sorted-arrays/description/)
``` c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) { 
        int tot = nums1.size() + nums2.size();
        if(tot % 2 == 0) {      //序列长度为偶数
            int left = find(nums1, 0, nums2, 0, tot / 2);
            int right = find(nums1, 0, nums2, 0, tot / 2 + 1);
            return (left + right) / 2.0;
        }
        else 
            return find(nums1, 0, nums2, 0, tot / 2 + 1);
    } 
    //找到nums1从i开始, nums2从j开始，第k大的元素
    int find(vector<int> &nums1, int i, vector<int> &nums2, int j, int k) {
        //nums1可用的序列长度大于nums2可用的序列长度
        if(nums1.size() - i > nums2.size() - j) return find(nums2, j, nums1, i, k);
        if(k == 1) {        //找到第一大的元素
            if(nums1.size() == i) return nums2[j];
            else    return min(nums1[i], nums2[j]);
        }
        //nums1可用序列为空
        if(nums1.size() == i) return nums2[j + k - 1];
        //si为nums1的可能到达的新位置，向右移动k/2
        int si = min((int)nums1.size(), i + k / 2), sj = j + k - k / 2;
        if(nums1[si - 1] < nums2[sj - 1])   //nums1中前k/2个元素都小于num2的第k/2位置的值，
            return find(nums1, si, nums2, j, k - (si - i));     
            //在nums1的[si,nums1.size() - 1], nums2的[j, nums2.size() - 1]的范围内找第k - (si - i)个元素
            //即等价于在nums1[i, nums1.size() - 1], nums2在[j, nums2.size() - 1]内找第K个元素
        else    
            return find(nums1, i, nums2, sj, k - (sj - j));
    }
};
```
### 5.最长回文子串[***](https://leetcode.cn/problems/longest-palindromic-substring/description/)
``` c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        for(int i = 0; i < s.size(); i++) {
            //回文串为偶数长度
            int l = i - 1, r = i + 1;
            while(l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;     
            if(res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);    //从 l+1位开始，长度为r-l-1的字串
            //回文串为奇数长度
            l = i, r = i + 1;
            while(l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;
            if(res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```
### 6.Z字形变换[****](https://leetcode.cn/problems/zigzag-conversion/description/)
   1. 找规律：第一行和最后一行是公差为2*n - 2的等差数列以i为起始点 ，第二行到倒数第二行也一样，但斜线上是2*n - 2 -i为起始点公差为2*n - 2的等差数列
``` c++ 
class Solution {
public:
    string convert(string s, int n) {
        if(n == 1) return s;
        string res;
        for(int i = 0; i < n; i++) {
            if(i == 0 || i == n - 1) {
                for(int j = i; j < s.size(); j += 2 * n - 2) 
                    res += s[j];
            }
            else {
                for(int j = i, k = 2 * n - 2 - i; j < s.size() || k <s.size(); j += 2 * n - 2, k += 2*n -2) {
                    if(j < s.size()) res += s[j];
                    if(k < s.size()) res += s[k];
                }
            }
        }
        return res;
    }
};
```
    2. 模拟Z路径: i = 0 或 i = n - 1时，变换路径方向
``` c++
class Solution {
public:
    string convert(string s, int n) { 
        if(n == 1) return s;
        vector<string> v;
        v.resize(n);
        int flag = -1;
        int i = 0;
        for(auto x : s) {
            if(i == 0 || i == n - 1) 
                flag = - flag;
            v[i] += x;
            i += flag;
        }
        for(int i = 1; i < n; i++) 
            v[0] += v[i];
        return v[0];
    }
};
```
### 7.整数反转[**](https://leetcode.cn/problems/reverse-integer/description/)
``` c++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while(x) { 
//     res * 10 + x % 10 > INT_MAX
            if(res > 0 && res > (INT_MAX - x % 10) / 10) return 0;
//     res * 10 + x % 10 < INT_MIN
            if(res < 0 && res < (INT_MIN - x % 10) / 10) return 0;
            res = 10 * res + x % 10;
            x /= 10;
        }
        return res;
    }
};
```
### 8.字符串转换整数(atoi)[****](https://leetcode.cn/problems/string-to-integer-atoi/description/)
``` c++
class Solution {
public:
    int myAtoi(string s) {
        int k = 0;
        while(k < s.size() && s[k] == ' ') k++;     //跳过前导空格
        int minus = 1;
        if(s[k] == '-') minus = -1, k++;       //负数
        else if(s[k] == '+') k++;               //正数
        int res = 0;
        while(k < s.size() && s[k] >= '0' && s[k] <= '9') {         //非数字字符或者末尾时，退出
            int x = s[k] - '0'; 
            if(minus > 0 && res > (INT_MAX - x) / 10)    return INT_MAX;    //正溢出
            if(minus < 0 && -res < (INT_MIN + x) / 10)   return INT_MIN;    //负溢出
            if(- 10 * res - x == INT_MIN)    return INT_MIN;        //res为正数时，存在等于最小负数整数的情况     
            res = 10 * res + x;
            k++;
        }
        return minus * res;     //加上符号
    }
};
``` 
### 9.回文数[*](https://leetcode.cn/problems/palindrome-number/description/)
``` c++
class Solution {
public:
    bool isPalindrome(int x) {
        if( x < 0) return 0;
        long long res = 0, n = x;
        while(x) {
            res = 10 * res + x % 10;
            x /= 10;
        }
        return res == n;
    }
};
```
### 10.正则表达式匹配[*****](https://leetcode.cn/problems/regular-expression-matching/description/)
``` c++
class Solution {
//非递归：
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = ' ' + s;
        p = ' ' + p;
        vector<vector<bool>> f(n + 1, vector<bool>(m + 1));
        f[0][0] = 1;
        for(int i = 0; i <= n; i++) 
            for(int j = 1; j <= m; j++) {
                if(j + 1 <= m && p[j + 1] == '*') continue;         
                //a*是组合在一起的被归纳在else if(p[j] =='*')
                else if(i && p[j] != '*')       //s不为空，p[j]是有效字符
                    f[i][j] = f[i - 1][j - 1] && (s[i] == p[j] || p[j] == '.');
                else if(p[j] == '*')        //p[j]与p[j - 1]组合使用，
                    f[i][j] = f[i][j - 2] || i && f[i - 1][j] && (s[i] == p[j - 1] || p[j - 1] == '.');
                    //f[i][j - 2]: *表示零个p[j - 1]的情况 或者 s[0 ~ i]可以匹配p[0 ~ j - 2]
                    //f[i - 1][j]表示s[0 ~ i - 1]与p[0 ~ j]所匹配，s[i]又与p[0 ~ j]所匹配 =》
                    //f[i - 1][j]： s[0 ~ i] 与 p[0 ~ j]匹配
            }
        return f[n][m];
    }
}; 
```

### 11.盛最多水的容器[***](https://leetcode.cn/problems/container-with-most-water/description/)
``` c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int i = 0, j = height.size() - 1;
        int res = 0;
        while(i < j) {
            res = max(res, (j - i) * min(height[i], height[j]));
            if(height[i] < height[j]) i++;        
            //容器左端高度小于右端高度，如果存在其他的最大值，那么左端右移
            else j--;
            //容器左端高度大于右端高度，如果存在其他的最大值，那么右端左移
        }
        return res;
    }
};
```

### 12.整数转罗马数字[***](https://leetcode.cn/problems/integer-to-roman/description/)
``` c++
class Solution {
public:
    string intToRoman(int num) {
        int values[] = {
            1000,
            900, 500, 400, 100,
            90, 50, 40, 10,
            9, 5, 4, 1
        };
        string s[] = {
                "M",
                "CM", "D", "CD", "C",
                "XC", "L", "XL", "X",
                "IX", "V", "IV", "I"
        };
        string res;
        for(int i = 0; i < 13; i++) {
            while(num >= values[i]) {
                num -= values[i];
                res += s[i];
            }
        }
        return res;
    }
};
```
### 13.罗马数字转整数[**](https://leetcode.cn/problems/roman-to-integer/description/)
``` c++
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char,int> hash;
        hash['I'] = 1, hash['V'] = 5;
        hash['X'] = 10, hash['L'] = 50;
        hash['C'] = 100, hash['D'] = 500;
        hash['M'] = 1000;
        int res = 0;
        for(int i = 0; i < s.size(); i++) {
            if(i + 1 < s.size() && hash[s[i]] < hash[s[i + 1]]) {
                res -= hash[s[i]];
            }
            else res += hash[s[i]];
            cout<<res<<endl;
        }
        return res;
    }   
};
```
### 14.最长公共前缀[*](https://leetcode.cn/problems/longest-common-prefix/description/)
``` c++
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string s;
        int k = INT_MAX;
        for(int i = 0; i < strs.size(); i++)
            k = min(k, (int)strs[i].size());
        if(!k) return s;
        for(int i = 0; i < k; i++) {
            bool flag = 1;
            for(int j = 1; j < strs.size(); j++)
                if(strs[j][i] != strs[0][i]) {
                    flag = 0;
                    break;
                }
            if(flag) s += strs[0][i];
            else break;
        }
        return s;
    }
};
```
### 15.三数之和[****](https://leetcode.cn/problems/3sum/description/)
``` c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());     //对数组进行排序
        for(int i = 0; i < nums.size(); i++) {
            //题目要求不要重复的三元组
            if(i && nums[i] == nums[i -1]) continue;
            for(int j = i + 1, k = nums.size() - 1; j < k; j++) {
                //跳过重复的三元组
                if(j > i + 1 && nums[j] == nums[j - 1]) continue;
                //确定k的位置了，跳过不满足nums[i] + nums[j] + nums[k] > 0
                while(j < k - 1 && nums[i] + nums[j] + nums[k - 1] >= 0) k--;
                if(nums[i] + nums[j] + nums[k] == 0) {
                    res.push_back({nums[i], nums[j], nums[k]});
                }
            }
        }
        return res;
    }
};
```

### 16.最接近的三数之和[****](https://leetcode.cn/problems/3sum-closest/description/)
``` c++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        pair<int, int> res(INT_MAX, INT_MAX);
        for(int i = 0; i < nums.size(); i++) 
            for(int j = i + 1, k = nums.size() - 1; j < k; j++) {
                while(k - 1 > j && nums[i] + nums[j] + nums[k - 1] >= target) k--;
                int s = nums[i] + nums[j] + nums[k];
                res = min(res, make_pair(abs(s - target), s));
                if(k - 1 > j) {
                    s = nums[i] + nums[j] + nums[k - 1];
                    res = min(res, make_pair(target - s, s));
                }
            }
        return res.second;
    }
};
```

### 17.电话号码的字母组合[***](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/description/)
``` c++
class Solution {
public:
    string s[8] = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> v;
    void dfs(string &d, int u, string res) {
        if(u == d.size()) {
            v.push_back(res);
            return;
        }
        //对digits下标进行dfs索引
        for(auto x : s[d[u] - '0' - 2]) {
            dfs(d, u + 1, res + x);
        }
    }
    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return {};
        dfs(digits, 0, "");
        return v;
    }
};
```

### 18.四数之和[***](https://leetcode.cn/problems/4sum/)
``` c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> v;
        //从小到大排序
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size(); i++) {
            //去重，i,j,k
            if(i && nums[i] == nums[i - 1]) continue;
            for(int j  = i + 1; j < nums.size(); j++) {
                if(j > i + 1 && nums[j] == nums[j - 1]) continue;
                //双指针，从左右两端夹逼
                for(int k = j + 1, u = nums.size() - 1; k < u; k++) {
                    if(k > j + 1 && nums[k] == nums[k - 1]) continue; 
                    //四数之和>=target，减小第四个数
                    while(u > k + 1 && (long long )nums[i] + nums[j] + nums[k] + nums[u - 1] >= target) u--; 
                    //四数之和可能会溢出，用long long， 相等就存储
                    if((long long)nums[i] + nums[j] + nums[k] + nums[u] == target) 
                        v.push_back({nums[i], nums[j], nums[k], nums[u]});
                }
            } 
        }
        return v;
    }
};
```
### 19.删除链表的倒数第N个结点[***]()
``` c++
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
    ListNode* removeNthFromEnd(ListNode* head, int n) { 
        ListNode *pre = new ListNode(0, head);
        //同时指向头节点，q前进n步，然后p与q同时前进，直到q->next为空，p->next即为删除节点
         ListNode *p = pre, *q = pre;
        for(int i = 0; i < n; i++) { 
            if(!q) return nullptr;
            q = q -> next;
        }
        while(q->next) {
            p = p->next;
            q = q->next;
        }
        p->next = p->next->next;
        //返回头节点
        return pre->next;
    }
};
```

### 20.有效的括号[**](https://leetcode.cn/problems/valid-parentheses/)
``` c++
class Solution {
public:
    bool isValid(string s) { 
        stack<char> stk;
        for(auto x : s) {
            if(x == '(' || x == '[' || x == '{')    stk.push(x);
            else {
                if(stk.size() && abs(x - stk.top()) <= 2)  stk.pop();
                else return false;
            }
        }
        return stk.size() == 0;
    }
};
```

### 21.合并两个有序链表[*](https://leetcode.cn/problems/merge-two-sorted-lists/)
``` c++
class Solution{
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {  
        ListNode *pre = new ListNode();
        ListNode *p = list1, *q = list2, *r = pre;
        while(p && q) {
            if(p->val < q->val)  {
                r->next = p;
                p = p->next;
            } 
            else {
                r->next = q;
                q = q->next;
            }
            r = r->next;
        }
        if(p) r->next = p;
        else r->next = q;
        return pre->next;
    }
};
```

### 22.括号生成[****](https://leetcode.cn/problems/generate-parentheses/description/)
```cpp
class Solution {
public:
    vector<string> v; 
    // 括号序列合法的充要条件
    // 1. 任意前缀中左括号的数量>=右括号数量
    // 2. 左括号数量等于右括号数量
    void dfs(int lc, int rc, int n, string res) {
        if(lc == n && rc == n) {
            v.push_back(res);
            return;
        }
        if(lc < n) dfs(lc + 1, rc, n, res + '(');
        // 有可能lc已经到达n了
        if(rc < n && lc > rc)   dfs(lc, rc + 1, n, res + ')');
    }
    vector<string> generateParenthesis(int n) {
        dfs(0, 0, n, "");
        return v;
    }
};
```
### 23.合并k个升序链表[****](https://leetcode.cn/problems/merge-k-sorted-lists/description/)
```cpp
1. 堆实现K个升序链表所有的节点排序
class Solution {
public: 
    struct Cmp {
        //重载括号，大的数在前面，在堆中则理解为大的数载下面，小的数载上面，就是小根堆
        bool operator() (ListNode *a, ListNode *b) {
            return a->val > b->val;
        }
    };
    ListNode *mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, Cmp> heap;
        ListNode *pre = new ListNode(), *r = pre;
        for(auto x : lists) 
            if(x) heap.push(x);   //所有非空的链表头节点加入堆中
            
        while(heap.size()) {
            auto t = heap.top();
            //取出的是待排链表中值最小的节点
            heap.pop();
            r->next = t;
            r = r->next;
            //如果该节点后面还有节点，则将其加入到堆中，从而实现将链表所有节点依次加入新合成链表中
            if(t->next) heap.push(t->next);
        }
        return pre->next;
    }
};

2. 归并排序：将K个链表归不断划分，再合并
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
    ListNode *mergeTwo(ListNode *l1, ListNode *l2) {
        ListNode *pre = new ListNode();
        ListNode *p = l1, *q = l2, *r = pre;
        while(p && q) {
            if(p->val < q->val) {
                r->next = p;
                p = p->next;
            }
            else {
                r->next = q;
                q = q->next;
            }
            r = r->next;
        }
        r->next = p ? p : q;
        return pre->next;
    }
    ListNode* merge(vector<ListNode*>& lists, int l, int r) {
        if(l > r) return nullptr;
        if(l == r) return lists[l];
        int mid = l + r >> 1;
        ListNode *left = merge(lists, l, mid);
        ListNode *right = merge(lists, mid + 1, r);
        return mergeTwo(left, right);
    }
    ListNode* mergeKLists(vector<ListNode*>& lists) { 
        return merge(lists, 0, lists.size() - 1);
    }
};
```

### 24.两两交换链表中的节点[***](https://leetcode.cn/problems/swap-nodes-in-pairs/)
``` c++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {   
        //链表为空或者只有一个节点则直接返回链表
        if(head == nullptr) return head;
        if(head->next == nullptr) return head;
        ListNode *p = head->next; 
        //将链表第3个节点为首的链表进行两两交换
        head->next = swapPairs(p->next);
        p->next = head;
        return p;
    }
};
```

### 25.K个一组反转链表
``` c++
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
    ListNode* reverseKGroup(ListNode* head, int k) {
        int n = 0;
        for(ListNode *p = head; p; p = p->next) n++;
        int a = n / k, b = n % k; 
        //a是需要反转的次数
        ListNode *pre = new ListNode(0, head), *r = pre;
        //在原数组上进行操作
        ListNode *p = head, *t;
        for(int i = 0; i < a; i++) {
            //t暂存该组旋转之前的第一个节点，也就是旋转后的最后一个节点
            t = p;
            for(int j = 0; j < k; j++) {  
                ListNode *q = p->next;
                p->next = r->next;
                r->next = p; 
                p = q;
            }   
            t->next = p;  //将旋转之后的节点与链表后方的节点衔起来 
            r = t;        //下次头插法的头节点就是当前组的最后一个节点
        }
        return pre->next;
    }
};
```

### 26.删除有序数组中的重复项[***](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/description/)
``` c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int i = 0, res = 0, k = 0;
        while(i < nums.size()) {
            int j = i;
            //存储非重复元素
            nums[k++] = nums[i]; 
            //跳过重复元素
            while(j < nums.size() && nums[j] == nums[i]) j++; 
            //只有一个元素，即没有重复
            if(j - i == 1) res++;
            //更新i为下一轮便利的元素
            i = j;
        }   
        return res, k;
    }
};
```
### 27 移除元素[**](https://leetcode.cn/problems/remove-element/)
``` c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int k = 0;
        for(int i = 0; i < nums.size(); i++) {
            if(nums[i] == val) continue;
            nums[k++] = nums[i]; 
        }
        return k;
    }
};
```

### 28.找出字符串中第一个匹配项的下标[*****](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/description/)
``` c++
class Solution {
public:
    int strStr(string s, string p) {
        //s或p为空时
        if(s.empty() || p.empty()) return -1;
        int n = s.size(), m = p.size();
        s = " " + s;
        p = " " + p;
        vector<int> next(m + 1);
        //构建next数组，next[1] = 0， next[i]表示从1 ~ i的字符串的最长公共前后缀匹配的长度，从而可以判断出模板串s下次匹配的位置也就是next[i] + 1
        for(int i = 2, j = 0; i <=m; i++)   {
            while(j && p[i] != p[j + 1]) j = next[j];
            if(p[i] == p[j + 1]) j++;
            next[i] = j;
        }
        for(int i = 1, j = 0; i <= n; i++) {
            //如果s中第i个与p中第j+1个不匹配，则找到1 ~ j中最长公共前后缀,从这里开始匹配
            while(j && s[i] != p[j + 1]) j = next[j];
            if(s[i] == p[j + 1]) j++;
            if(j == m) return i - m;
        }
        return -1;
    }
};
```
### 29.两数相除[*****](https://leetcode.cn/problems/divide-two-integers/description/)
``` c++
class Solution {
public:
    int divide(int a, int b) {
        typedef long long LL;
        int minus = 1;
        if(a > 0 && b <0 || a < 0 && b > 0) minus = -1;
        //变成整数统一运算
        //a的负数可能位-2^31，转为绝对值时，会从Int变为long，所以需要加LL
        LL x = abs((LL)a), y = abs((LL)b); 
        LL res = 0;
        vector<LL> exp;
        //把除数的绝对值的1，2，4，8...,2^k倍数添加到exp中，exp[i]的i代表结果的二进制的第i位的情况
        for(LL i = y; i <= x; i = i + i) 
            exp.push_back(i); 
        //从exp的高位开始相减，从低位相减的话，有的没有减完的情况
        for(int i = exp.size() - 1; i >= 0 ; i--)
            if(x >= exp[i]) {
                x -= exp[i];
                //可能超过31位的情况，需要加LL
                res += 1LL << i;
        }
        if(minus == -1) res = -res;
        if(res < INT_MIN || res > INT_MAX) return INT_MAX;
        return res;
    }
};
```
### 30.串联所有单词的字串[***](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)
``` c++
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) { 
        vector<int> res;   
        if(words.empty()) return res;
        //统计words中单词个数
        unordered_map<string, int> tot;
        for(auto &word : words)     tot[word]++;
        //单词是等长的，找到单词长度
        int n = s.size(), m = words.size(), w = words[0].size();
        //cnt用来统计满足当前m*w长度中word的个数
        int cnt = 0;
        //把0~n划分成多个w，每个w从0到w-1，每个w分为w组
        for(int i = 0; i < w; i++) {
            unordered_map<string, int> wd;
            //统计窗口为m*w的单词出现个数
            for(int j = i; j + w <= n; j += w) {
                if(j - i >= m * w) {
                    //窗口超过m*w，words组合的总长度，把范围外的wd给去掉
                    string word = s.substr(j - m * w, w);
                    wd[word]--;
                    if(wd[word] < tot[word]) cnt--;
                }
                string word = s.substr(j, w);
                wd[word]++;
                //m*w中word个数不超过需求量时，cnt+1
                if(wd[word] <= tot[word]) cnt++
                if(cnt == m) 
                    res.push_back(j - (m - 1) * w);
            }
        }
    }
};
```

### 31.下一个排列[***](https://leetcode.cn/problems/next-permutation/description/)
``` c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int k = nums.size() - 1;
        //从右往左找到降序的第一个数nums[k - 1]
        while(k > 0 && nums[k - 1] >= nums[k]) k--;
        if(k <= 0) {
            reverse(nums.begin(), nums.end());
        }
        else {
            int t = k;
            //从k~nums.size()之间找到第一个比nums[k - 1]大的数，两者交换，k-1右边再进行排序，就是字典序下一个数
            while(t < nums.size() && nums[t] > nums[k - 1]) t++;
            swap(nums[k - 1], nums[t - 1]);
            reverse(nums.begin() + k , nums.end());
        }
    }
};
```
### 32.最长有效括号[*****](https://leetcode.cn/problems/longest-valid-parentheses/description/)
``` c++
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> stk;
        int res = 0;
        //把s分为各段，每段满足总的左括号的数量比右括号数量小1，但是段内(除去最后一个右括号)左括号的数量>=右括号，有效括号一定在段内，不可能跨段
        for(int i = 0, start = -1; i < s.size(); i++) {
            //start作为段的前一个位置  ，     左括号入栈
            if(s[i] == '(') stk.push(i);
            else {
                //有匹配的左括号，必定拥有有效的括号序列
                if(stk.size()) {
                    stk.pop();
                    //退掉匹配的左括号后，栈中仍然有左括号，那么栈顶可作为匹配序列最左端的前一个位置
                    if(stk.size()) {
                        res = max(res, i - stk.top());
                    }
                    //栈中没有元素，那么整段都是有效的括号序列
                    else 
                        res = max(res, i - start);
                }
                //没有与之匹配的左括号，说明到达段的末尾，可以作为下一个段的左端前一个位置start
                else 
                    start = i;
            }
        }
        return res;
    }
};
```
### 33.搜索旋转排序数组[****](https://leetcode.cn/problems/search-in-rotated-sorted-array/description/)
``` c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        //两次二分，第一次二分找到前半段和后半段的中介点， 第二次二分找到target的位置（可能在升序，可能在降序）
        if(nums.empty()) return -1;
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            //l = mid时，要+1再除以2
            int mid = l + r + 1 >> 1;
            //前半段中所有数都>nums[0]，递增的， 符合>=nums[0]条件的区间为[mid, r]，
            if(nums[mid] >= nums[0]) l = mid;
            else r = mid - 1;
        }
        //判断target在前半段还是后半段，再查找
        if(target >= nums[0]) l = 0;
        else l = l + 1, r = nums.size() -1;
        while(l < r){
            int mid = l + r >> 1; 
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        } 
        if(nums[r] == target) return l;
        //以上使用了两个二分模板
        /*
        int bsearch_1(int l, int r)
        {
            while (l < r)
            {
                int mid = l + r >> 1;
                if (check(mid)) r = mid;
                else l = mid + 1;
            }
            return l;
        }
    int bsearch_2(int l, int r)
    {
        while (l < r)
        {
            int mid = l + r + 1 >> 1;
            if (check(mid)) l = mid;
            else r = mid - 1;
        }
        return l;
    }
    用'-'表示不满足check条件，'+'满足check条件，v表示我们的目标
    模板一：----v++++  找到满足条件的下边界
    模板二：----++++v   找到满足条件的上边界
    */
        return -1;
    }
};
```
### 34.在排序数组中查找元素的第一个和最后一个位置[****](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)
``` c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.empty()) return {-1, -1};
        int l = 0, r = nums.size() - 1;
        //满足条件的左边界
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if(nums[r] != target) return {-1, -1};
        int L = l;
        l = 0, r = nums.size() - 1;
        //满足条件的右边界
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(nums[mid] <= target) l = mid;
            else r = mid - 1;
        }
        return {L, r};
    }
};
```
### 35.搜索插入位置[***](https://leetcode.cn/problems/search-insert-position/)
``` c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if(nums[r] < target) return r + 1;
        return r;
    }
};
```
### 36.有效的数独[****](https://leetcode.cn/problems/valid-sudoku/description/)
``` c++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        bool st[9];
        //检验行
        for(int i = 0; i < 9; i++) {  
            memset(st, 0, sizeof st);
            for(int j = 0; j < 9; j++) {
                if(board[i][j] != '.') {
                    int t = board[i][j] - '1';
                    if(st[t]) return false;
                    st[t] = true;
                }
            }
        } 
        //检验列
        for(int i = 0; i < 9; i++) {  
            memset(st, 0, sizeof st);
            for(int j = 0; j < 9; j++) {
                if(board[j][i] != '.') {
                    //j和i要互换，从而实现检查某一列
                    int t = board[j][i] - '1';
                    if(st[t]) return false;
                    st[t] = true;
                }
            }
        }
        //检验3*3格子
        for(int i = 0; i < 9; i += 3) 
            for(int j = 0; j < 9; j += 3) {
                memset(st, 0, sizeof st);
                for(int x = 0; x < 3; x++)
                for(int y = 0; y < 3; y++) {
                    if(board[i + x][j + y] != '.') {
                        int t = board[i + x][j + y] - '1';
                        if(st[t]) return false;
                        st[t] = true;
                    }
                }
            } 
        return true;
    }
};
```
### 37.解数独[*****](https://leetcode.cn/problems/sudoku-solver/description/)
``` c++
class Solution {
public:
    bool row[9][9], col[9][9], cell[3][3][9];
    bool dfs(vector<vector<char>>& board, int x, int y) {
        if(y == 9) x++, y = 0;
        if(x == 9) return true;
        if(board[x][y] != '.') return dfs(board, x, y + 1);
        for(int i = 0; i < 9; i++) 
            if(!row[x][i] && !col[y][i] && !cell[x/3][y/3][i]) {
                row[x][i] = col[y][i] = cell[x/3][y/3][i] = 1;
                board[x][y] = '1' + i;
                if(dfs(board, x, y + 1)) return true;
                row[x][i] = col[y][i] = cell[x/3][y/3][i] = 0;
                board[x][y] = '.';
            }
        return false;
    }
    void solveSudoku(vector<vector<char>>& board) {
        memset(row, 0, sizeof row);
        memset(col, 0, sizeof col);
        memset(cell, 0, sizeof cell);
        for(int i = 0; i < 9; i++)
            for(int j = 0; j < 9; j++) 
                if(board[i][j] != '.') {
                    int t = board[i][j] - '1';
                    row[i][t] = col[j][t] = cell[i/3][j/3][t] = 1;
                }
        dfs(board, 0, 0);
    }
};
```
### 37.解数独[*****](https://leetcode.cn/problems/sudoku-solver/)
``` c++
class Solution {
public:
    bool row[9][9], col[9][9], cell[3][3][9];
    //判断每个点(x, y)的情况
    bool dfs(vector<vector<char>>& board, int x, int y) {
        //列满，进入下一行, 要放前面
        if(y == 9) x++, y = 0;
        //行满，排列完成，找到数独
        if(x == 9) return true;
        //不能安插数
        if(board[x][y] != '.') return dfs(board, x, y + 1);
        //深搜，逐个判断是否能在（x,y）插入i
        for(int i = 0; i < 9; i++) 
            if(!row[x][i] && !col[y][i] && !cell[x/3][y/3][i]) {
                row[x][i] = col[y][i] = cell[x/3][y/3][i] = 1;
                board[x][y] = '1' + i;
                //如果从(x, y)以后的位置都满足数独，则解数独成功
                if(dfs(board, x, y + 1)) return true;
                //恢复现场
                row[x][i] = col[y][i] = cell[x/3][y/3][i] = 0;
                board[x][y] = '.';
            }
        return false;
    }
    // row[u][i]表示能否再第u行插入i
    void solveSudoku(vector<vector<char>>& board) {
        memset(row, 0, sizeof row);
        memset(col, 0, sizeof col);
        memset(cell, 0, sizeof cell);
        //初始化row, col ,cell
        for(int i = 0; i < 9; i++)
            for(int j = 0; j < 9; j++) 
                if(board[i][j] != '.') {
                    int t = board[i][j] - '1';
                    row[i][t] = col[j][t] = cell[i/3][j/3][t] = 1;
                }
        dfs(board, 0, 0);
    }
};
```
### 38.外观数列[**](https://leetcode.cn/problems/count-and-say/description/)
``` c++
class Solution {
public:
    string countAndSay(int n) {
        string s = "1";
        //双指针，找到有多少个相等的数，然后构成字符串
        for(int i = 0; i < n - 1; i++) {
            string t;
            for(int j = 0; j < s.size(); j++) {
                int k = j;
                while(k < s.size() && s[k] == s[j]) k++;
                t += to_string(k - j) + s[j];
                j = k - 1;
            }
            s = t;
        }
        return s;
    }
};
```

### 39.组合总和[****](https://leetcode.cn/problems/combination-sum/description/)
``` c++
class Solution {
public:
    vector<vector<int>> v;
    void dfs(vector<int>& c, int target, vector<int> &res, int u) {
        if(target < 0) return;
        if(target == 0) {
            v.push_back(res);
            return;
        }
        if(u == c.size()) return;
        for(int i = 0; i * c[u] <= target; i++) {
            dfs(c, target - i * c[u], res, u + 1);
            //可以理解为将前i个c[u]弹出，再加前i+1个c[u]加入，从而等价为每次push一个c[u[]
            res.push_back(c[u]);
        }
        //这是把所有的c[u]都回收
        for(int i = 0; i * c[u] <= target; i++) 
            res.pop_back();
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) { 
        vector<int> res;
        dfs(candidates, target, res, 0);
        return v;
    }
};
```
### 40.组合综合II[*****](https://leetcode.cn/problems/combination-sum-ii/)
``` c++
class Solution {
public:
    vector<vector<int>> v;
    void dfs(vector<int>& c, int target, int u, vector<int> &res) {
        if(target < 0) return;
        if(target == 0) {
            v.push_back(res); 
            return;
        }
        if(u == c.size()) return;
        int k = u;
        while(k < c.size() && c[k] == c[u]) k++;
        //找到c[u]相同值的个数
        int cnt = k - u; 
        //c[u]的个数受到制约
        for(int i = 0; i * c[u] <= target && i <= cnt; i++) {
            dfs(c, target - i * c[u], k, res);
            res.push_back(c[u]);
        }
        for(int i = 0; i * c[u] <= target && i <= cnt; i++)
            res.pop_back();
    }
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<int> res;
        dfs(candidates, target, 0, res);
        return v;
    }
};
```
### 41.缺失的第一个正数[*****](https://leetcode.cn/problems/first-missing-positive/)
``` c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) { 
        int n = nums.size();
        if(!n) return 1;
        //减1,方便映射到x的位置上
        // 这里，要加一个引用
        for(auto &x : nums) 
            if(x != INT_MIN) 
                x--;
        //x的范围为int最小值时，不能减一
        //最多有n个元素不在自己的位置，需要交换n次，让其回位
        for(int i = 0; i < n; i++) {
            //把nums[i]交换到nums[i]的位置，直到所有相关的都被交换好，遇到非法的nums[i]退出
            while(nums[i] >= 0 && nums[i] < n && nums[i] != nums[nums[i]]) 
                swap(nums[i], nums[nums[i]]);
        }  
        for(int i = 0; i < n; i++) 
            if(nums[i] != i) 
                return i + 1;
        //1~n
        return n + 1;
    }
};
```
### 42.接雨水[*****](https://leetcode.cn/problems/trapping-rain-water/description/)
``` c++
//单调栈
class Solution {
public:
    int trap(vector<int>& height) {
        stack<int> stk;
        int res = 0;
        for(int i = 0; i < height.size(); i++) {
            int last = 0;
            //height[i]，last, height[stk.top()]三者之间形成凹槽，凹槽次低点为height[stk.top()]
            //利用单调栈的特点，横向划分，height[stk.top()] - last为水槽高度
            while(stk.size() && height[stk.top()] <= height[i]) {
                res += (height[stk.top()] - last) * (i - stk.top() - 1);
                last = height[stk.top()];
                stk.pop();
            }
            //凹槽次低点为height[i]
            if(stk.size()) res += (height[i] - last) * (i - stk.top() - 1);
            stk.push(i);
        }
        return res;
    }
};

//动态规划
class Solution {
public:
    int trap(vector<int>& height) { 
        int n = height.size();
        vector<int> left(n), right(n);
        //找到每个位置左侧的最大高度和右侧的最大高度，较低的高度-height[i]就是第i个位置的容积
        for(int i = 1; i < n; i++) 
            left[i] = max(left[i - 1], height[i - 1]);
        for(int i = n - 2; i >= 0; i--) 
            right[i] = max(right[i + 1], height[i + 1]);
        int res = 0;
        for(int i = 0; i < n; i++) {
            int h = min(left[i], right[i]);
            res += max(0, h - height[i]);
        }
        return res;
    }
};

//双指针
class Solution {
public:
    int trap(vector<int>& height) {  
        int n = height.size();
        int l = 0, r = 0;
        for(int i = 0, j = n - 1; i < j; ) {
            int l = max(l, height[i]), r = max(r, height[i]);
            //凹槽左端为次低点
            if(l <= r) {
                res += max(0, l - height[i]);
                i++;
            }
            //凹槽右端为次低点
            else {
                res += max(0, r - height[i]);
                j--;
            }
        }
        return res;
    }
};
```

### 43.字符串相乘[****](https://leetcode.cn/problems/multiply-strings/)
``` c++
class Solution {
public:
    //两个字符串相乘
    string multiply(string num1, string num2) {
        int n = num1.size(), m = num2.size();
        vector<int> A, B;
        //字符串数组取出逆置，从低位到高位
        for(int i = n - 1; i >=0; i--)  A.push_back(num1[i] - '0');
        for(int i = m - 1; i >= 0; i--) B.push_back(num2[i] - '0');
        vector<int> C(n + m);
        
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                C[i + j] += A[i] * B[j]; 
        //每位进行进制运算
        for(int  i = 0, t = 0; i < C.size(); i++) {
            t += C[i];
            C[i] = t % 10;
            cout << C[i];
            t /= 10;
        }
        //把高位多余的0删掉
        while(C.size() > 1 && C.back() == 0) C.pop_back();
        string res;
        //换成字符串
        for(int i = C.size() - 1; i >= 0; i--)
            res += C[i] + '0';
        return res;
    }
};
```
### 44.通配符匹配[*****](https://leetcode.cn/problems/wildcard-matching/description/)
```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = " " + s;
        p = " " + p;
        vector<vector<bool>> f(n + 1, vector<bool>(m + 1));
        //0个与0个匹配成功
        f[0][0] = true;
    
        for(int i = 0; i <= n; i++)
            for(int j = 1; j <= m; j++)
                //f[i][j] = f[i][j - 1] || f[i-1][j -1] || f[i-2][j-1] ....|| f[0][j-1];
                //f[i - 1][j] = f[i-1][j-1] || f[i-2][j-1]... || f[0][j-1]
                //f[i][j] = f[i][j-1] || f[i-1][j];
                if(p[j] == '*') 
                    f[i][j] = f[i][j - 1] || i && f[i - 1][j];
                else 
                    f[i][j] = i && f[i - 1][j - 1] && (s[i] == p[j] || p[j] == '?');
        return f[n][m];
    }
};
```
### 45.跳跃游戏II[****](https://leetcode.cn/problems/jump-game-ii/)
``` c++
class Solution {
public:
//动态规划 + 贪心
    int jump(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n);
        int last = 0;
        f[0] = 0;
        //last -> i,  last前进的范围(i的范围)是[last, last + f[last]]
        //f[i] = f[last] + 1表示的最小跳跃次数
        for(int i = 1; i < n; i++) {
            while(i > last + nums[last]) last++;
            f[i] = f[last] + 1;
        }
        return f[n - 1];
    }
};
```
### 46.全排列[***](https://leetcode.cn/problems/permutations/description/)
``` c++
class Solution {
public:  
    vector<vector<int>> v; 
    void dfs(vector<int>& nums, vector<int> &res, int state) {
        if(res.size() == nums.size()) {
            v.push_back(res);
            return;
        }
        //state的二进制表示第i个数有没有被存储
        //全排列用for循环
        for(int i = 0; i < nums.size(); i++) 
            if(!(state >> i & 1)) { 
                res.push_back(nums[i]);
                dfs(nums, res, state | 1 << i); 
                res.pop_back();
            }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> res;
        int n = nums.size(); 
        dfs(nums, res, 0);
        return v;
    }
};
```
### 47. 全排列II[***](https://leetcode.cn/problems/permutations-ii/description/)
``` c++
class Solution {
public: 
//算法时间复杂度：n * n!
    vector<vector<int>> v;
    vector<bool> st;
                //保证相同的数不重复，比如1，1，1
                //出现的情况有（1），（1，1），（1，1，1）这三种，可以通过找到第一次没有用过的1来不重复 
     void dfs(vector<int>& nums, vector<int> &res) {
         if(res.size() == nums.size()) {
             v.push_back(res);
             return;
         }
         for(int i = 0; i < nums.size(); i++) 
            if(!st[i]) {
                // 相同的1，如果前面的1没有被用过，不能跳过前面的1，如果前面的1被用过才可以用后面的1
                if(i && nums[i] == nums[i - 1] && !st[i - 1]) continue;
                st[i] = 1; 
                res.push_back(nums[i]);
                dfs(nums, res);
                st[i] = 0;
                res.pop_back();
            } 
     }
    vector<vector<int>> permuteUnique(vector<int>& nums) { 
        //可能是乱序
        sort(nums.begin(), nums.end());
        vector<int> res;
        int n = nums.size();
        st = vector<bool>(n, 0);
        dfs(nums, res);
        return v;
    }
};
```
### 48.旋转图像[***](https://leetcode.cn/problems/rotate-image/description/)
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        //先沿对角线翻转，再沿中线翻转
        for(int i = 0; i < matrix.size(); i++)
        for(int j = 0; j < i; j++) 
            swap(matrix[i][j], matrix[j][i]);
        for(int i = 0; i < matrix.size(); i++)
            for(int  j = 0, k = matrix[i].size() - 1; j < k; j++, k--)
                swap(matrix[i][j], matrix[i][k]);
        
    }
};
```
### 49.字母异位分组[***](https://leetcode.cn/problems/group-anagrams/description/)
``` c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> p;
        //将同一类字符串按最小的字典序映射
        for(auto s : strs) {
            string t = s;
            sort(s.begin(), s.end());
            p[s].push_back(t);
        }
        vector<vector<string>> res;
        for(auto item : p) 
            res.push_back(item.second);
        return res;
    }
};
```
### 50.Pow(x, n) [***](https://leetcode.cn/problems/powx-n/description/)
```cpp
class Solution {
public:
    double myPow(double x, int n) {
        //快速幂， n小于0时，需要取倒数，n可能超过int，用LL
        int iminus = 1;
        if(n < 0) iminus = -1;
        typedef long long LL;
        double res = 1;
        for(LL k = abs((LL)n); k; k >>= 1) {
            if(k & 1) res *=  x;
            x = x * x;
        }
        if(iminus == -1) res = 1 / res;
        return res;
    }
};
```

