## 万能头文件
#include <bits/stdc++.h>
  
## c++输入输出
### 1.读取字符串
string s;
getline(cin, s): 读取整行

### 2.读取字符
char c = getchar()

### 1. 链表的输入输出
```c++
#include<iostream>
#include<vector>
using namespace std;
struct ListNode{
    int val;
    ListNode *next; 
    ListNode(int x):val(x),next(NULL){}
};
ListNode* createList(vector<int> &v, int pos) {
    ListNode *pre = new ListNode(-1), *r = pre, *q;
    int k = 0;
    for(int i = 0; i < v.size(); i++) {
        r->next = new ListNode(v[i]);
        r = r->next;
        if(k == pos) q = r;
        k++;
    }
    if(pos != -1) r->next = q;
    return pre->next;
}
bool hasCycle(ListNode *head) {
    ListNode *p = head, *q = head;
    while(q && q->next) {
        p = p->next;
        q = q->next->next;
        if(p == q) return true;
    }
    return false;
}
int main()
{
    vector<int> v;
    int n;
    cin >> n;
    while(n --){
        int x;
        cin >> x;
        v.push_back(x);
    }
    int pos;
    cin >> pos;
    ListNode *head = createList(v, pos);
    cout << hasCycle(head) << endl;
}
```

### 2. 树节点输入输出
```c++
#include<iostream>
#include<queue>
#include<vector>
using namespace std;
struct TreeNode{
    int val;
    TreeNode *left, *right;
    TreeNode(){}
    TreeNode(int x):val(x),left(NULL),right(NULL){}
    TreeNode(int x, TreeNode *l, TreeNode *r):val(x), left(l), right(r){}
};
TreeNode* createTree(vector<int> &v) {
    vector<TreeNode*> tree(v.size(), NULL);
    TreeNode *root = NULL;
    for(int i = 0; i < v.size(); i++) {
        if(v[i] != -1) tree[i] = new TreeNode(v[i]);  
        // if(i == v.size() - 1) cout << v[i];
    }
    root = tree[0];
    for(int i = 0; i < v.size(); i++) {
        if(v[i] == -1) continue;
        int j = 2 * i + 1;
        if(j < v.size()) tree[i]->left = tree[j];
        if(j + 1 < v.size()) tree[i]->right = tree[j + 1];
    }
    return root;
}
void printTree(TreeNode *root) {
    if(!root) return;
    queue<TreeNode*> q;
    q.push(root);
    while(q.size()) {
        int n = q.size();
        for(int i = 0; i < n; i++) {
            auto p = q.front();
            q.pop();
            cout << p->val;
            if(i < n - 1) cout << " ";
            else cout << endl;
            if(p->left) q.push(p->left);
            if(p->right) q.push(p->right);
        }
    }
}
int main()
{ 
    vector<int> v;  
    int x;
    while(cin >> x) { 
        // cout <<x << endl;
        char c = getchar();
        v.push_back(x); 
         if(c != ',') break;
    }
    TreeNode *root = createTree(v);
    printTree(root);
}
```