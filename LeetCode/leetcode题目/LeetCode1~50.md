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
````
### 10.正则表达式匹配[*****](https://leetcode.cn/problems/regular-expression-matching/description/)
``` c++
class Solution {
非递归：
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

### 18. 

