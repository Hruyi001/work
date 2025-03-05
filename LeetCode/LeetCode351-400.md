### 394. 字符串编码[**](https://leetcode.cn/problems/decode-string/?envType=study-plan-v2&envId=top-100-liked)
```c++
class Solution {
public: 
    // u要加引用，遍历完s; []里面可能还存在[]，需要递归
    string dfs(string s, int &u) {
        string res;
        while(u < s.size() && s[u] != ']') {
            // u++;
            if(s[u] >= 'a' && s[u] <= 'z' || s[u] >= 'A' && s[u] <= 'Z') res += s[u++];
            else if(s[u] >= '0' && s[u] <= '9') {
                int k = u;
                while(k < s.size() && s[k] != '[')  k++;
                int cnt = stoi(s.substr(u, k - u));
                // u指向'['
                u = k + 1;
                string y = dfs(s, u);
                // u当前指向']'
                u++;
                while(cnt--) res += y;
            }
        }
        return res;
    }
    string decodeString(string s) {
        int u = 0;
        return dfs(s, u);
    }
};
```