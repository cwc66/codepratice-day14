# codepratice-day14
代码随想录第14次练习

二叉树的迭代，路径，构造二叉树
补

[找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)
给定一个二叉树，在树的最后一行找到最左边的值。

递归法
最后一行、最左边的值。
需要找深度最大的叶子节点。用前序遍历找最左的节点

递归三部曲
1.确定递归函数的参数和返回值
树的根节点，记录深度int类型，不需要返回值
全局变量记录最大深度maxdepth,result记录最大深度最左节点的数值。

2.确定终止条件
遇到叶子节点时，需要统计最大深度，更新。

3.确定单层递归的逻辑
递归深度，左右节点。


```CPP
class Solution {
public:
    int maxDepth = INT_MIN;
    int result;
    void traversal(TreeNode* root, int depth) {
        if (root->left == NULL && root->right == NULL) {
            if (depth > maxDepth) {
                maxDepth = depth;
                result = root->val;
            }
            return;
        }
        if (root->left) {
            depth++;
            traversal(root->left, depth);
            depth--; // 回溯
        }
        if (root->right) {
            depth++;
            traversal(root->right, depth);
            depth--; // 回溯
        }
        return;
    }
    int findBottomLeftValue(TreeNode* root) {
        traversal(root, 0);
        return result;
    }
};
```

精简版
```CPP
class Solution {
public:
    int maxDepth = INT_MIN;
    int result;
    void traversal(TreeNode* root, int depth) {
        if (root->left == NULL && root->right == NULL) {
            if (depth > maxDepth) {
                maxDepth = depth;
                result = root->val;
            }
            return;
        }
        if (root->left) {
            traversal(root->left, depth + 1); // 隐藏着回溯
        }
        if (root->right) {
            traversal(root->right, depth + 1); // 隐藏着回溯
        }
        return;
    }
    int findBottomLeftValue(TreeNode* root) {
        traversal(root, 0);
        return result;
    }
};
```

迭代法更好理解，只需要记录最后一行的第一个节点数值。
层序遍历模板修改
```CPP
class Solution
{
public:
    int findBottomLeftValue(TreeNode* root)
    {
        queue<TreeNode*> que;
        if(root!=nullptr) que.push(root);
        int result=0;
        while(!que.empty())
        {
            int size=que.size();
            for(int i=0;i<size;i++)
            {
                TreeNode* node=que.front();
                que.pop();
                if(i==0) result=node->val;// 记录最后一行第一个元素
                if(node->left) que.push(node->left);
                if(node->right) que.push(node->right);
            }
        }
        return result;
    }
}
```
求高度、回溯、层序遍历三个知识点。


[路径总和](https://leetcode.cn/problems/path-sum/description/)
给你二叉树的根节点root和一个表示目标和的整数targetSum。判断该树种是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和targetSum。如果存在，返回true，否则返回false.

递归函数什么时候要有返回值，什么时候没有返回值

中节点没有处理逻辑，所以前中后序遍历都可以。
1.确定递归函数的参数和返回类型。
二叉树根节点，计数器，用来计算二叉树的一条边之和是否正好是目标和，计数器为int

递归什么时候需要和不需要返回值
(1)如果需要搜索整棵二叉树且不用处理递归返回值，递归函数就不需要返回值。
(2)如果需要搜索整棵二叉树且需要处理递归返回值，递归函数需要返回值
(3)如果要搜索其中一条符合条件的路径，那么递归一定需要返回值，因为遇到符合条件的路径时就要及时返回。

2.确定终止条件
累加判断目标和，需要传递当前路径和 和 目标和，增加复杂度。
递减的方法简单。只需要传递目标和，每次递归将目标和减去当前节点值传递。

count==0，同时找到叶子节点，就找到了目标和。

3.确定单层递归的逻辑
不让空节点进入递归，返回值true说明找到路径。
回溯隐藏在traversal(cur->left, count - cur->left->val)这里， 因为把count - cur->left->val 直接作为参数传进去，函数结束，count的数值没有改变。

```CPP
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
    bool traversal(TreeNode* cur,int count)
    {
        if(!cur->left && !cur->right && count==0) return true;
        //遇到叶子节点，并且计数为0
        if(!cur->left && !cur->right) return false;
        //遇到叶子节点，直接返回

        if(cur->left)
        {
            //左
            count-=cur->left->val;//递归，处理节点
            if(traversal(cur->left,count)) return true;
            count+=cur->left->val;
        }
        if(cur->right)
        {
            //右
            count-=cur->right->val;//递归处理节点，之后加回去 作为回溯
            if(traversal(cur->right,count)) return true;
            count+=cur->right->val;
        }
        return false;
    }
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root==nullptr) return false;
        return traversal(root,targetSum-root->val);
    }
};
```

精简代码
```CPP
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root) return false;
        if (!root->left && !root->right && sum == root->val) {
            return true;
        }
        return hasPathSum(root->left, sum - root->val) || hasPathSum(root->right, sum - root->val);
    }
};
```

迭代
用栈模拟递归，栈的一个元素要记录节点指针，还要记录从头节点到该节点的路径数值总和。
stack<pair<TreeNode*,int>> st;
```CPP
class solution {

public:
    bool haspathsum(TreeNode* root, int sum) {
        if (root == null) return false;
        // 此时栈里要放的是pair<节点指针，路径数值>
        stack<pair<TreeNode*, int>> st;
        st.push(pair<TreeNode*, int>(root, root->val));
        while (!st.empty()) {
            pair<TreeNode*, int> node = st.top();
            st.pop();
            // 如果该节点是叶子节点了，同时该节点的路径数值等于sum，那么就返回true
            if (!node.first->left && !node.first->right && sum == node.second) return true;

            // 右节点，压进去一个节点的时候，将该节点的路径数值也记录下来
            if (node.first->right) {
                st.push(pair<TreeNode*, int>(node.first->right, node.second + node.first->right->val));
            }

            // 左节点，压进去一个节点的时候，将该节点的路径数值也记录下来
            if (node.first->left) {
                st.push(pair<TreeNode*, int>(node.first->left, node.second + node.first->left->val));
            }
        }
        return false;
    }
};
```

[路径总和Ⅱ](https://leetcode.cn/problems/path-sum-ii/description/)
给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

要遍历整个树，找到所有路径所以递归函数不要返回值

```CPP
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
    vector<vector<int>> result;
    vector<int> path;
    //递归函数不需要返回值，因为我们要遍历整个树
    void traversal(TreeNode* cur,int count)
    {
        if(!cur->left && !cur->right && count==0)
        {
            //遇到了叶子节点且找到了和为sum的路径
            result.push_back(path);
            return;
        }

        //遇到叶子节点而没有找到合适的边，直接返回
        if(!cur->left && !cur->right) return ;
        
        if(cur->left)
        {
            path.push_back(cur->left->val);
            count-=cur->left->val;
            traversal(cur->left,count);//递归
            count+=cur->left->val;
            path.pop_back();//回溯
        }

        if(cur->right)
        {
            path.push_back(cur->right->val);
            count-=cur->right->val;
            traversal(cur->right,count);
            count+=cur->right->val;
            path.pop_back();
        }
        return ;
    }
public:
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        result.clear();
        path.clear();
        if(root==nullptr) return result;
        path.push_back(root->val);//把根节点放进路径
        traversal(root,targetSum-root->val);
        return result;
    }
};
```

[从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
根据一棵树的中序遍历与后序遍历构造二叉树。


理论知识：
以 后序数组的最后一个元素为切割点，先切中序数组，根据中序数组，反过来再切后序数组。一层一层切下去，每次后序数组最后一个元素就是节点元素。

画一个二叉树，去切分，自己就能判断出来。

递归步骤:
1.如果数组大小为0的话，说明是空节点了
2.如果不为空，那么取后序数组最后一个元素作为节点元素
3.找到后序数组最后一个元素在中序数组的位置，作为切割点
4.切割中序数组，切成中序左数组和中序右数组
5.切割后序数组，切成后序左数组和后序右数组
6.递归处理左区间和有区间

切割时，会产生四个区间。具体，切割点在后序数组的最后一个元素，用于切割中序数组。找到切割点在中序数组的位置，然后切割，以下代码是左闭右开的原则。

切割后序数组，没有明确切割元素，但是我们拿中序数组的左右区间大小来切割后序数组即可。因为中序数组大小一定是和后序数组的大小相同的

```CPP
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
    TreeNode* traversal(vector<int>& inorder,vector<int>& postorder)
    {
        if(postorder.size()==0) return nullptr;

        //后序遍历数组最后一个元素，就是当前的中间节点
        int rootValue=postorder[postorder.size()-1];
        TreeNode* root=new TreeNode(rootValue);

        //叶子节点
        if(postorder.size()==1) return root;

        //找到中序遍历的切割点
        int delimiterIndex;
        for(delimiterIndex=0;delimiterIndex<inorder.size();delimiterIndex++)
        {
            if(inorder[delimiterIndex]==rootValue) break;
        }

        //切割中序数组
        //左闭右开区间:[0,delimiterIndex)
        vector<int> leftInorder(inorder.begin(),inorder.begin()+delimiterIndex);
        //[delimiterIndex+1,end)
        vector<int> rightInorder(inorder.begin()+delimiterIndex+1,inorder.end());

        //postorder舍弃末尾元素
        postorder.resize(postorder.size()-1);

        //切割后序数组
        //依然是左闭右开区间，注意这里使用了左中序数组大小作为切割点
        //[0,leftInorder.size)
        vector<int> leftPostorder(postorder.begin(),postorder.begin()+leftInorder.size());
        //[leftInorder.size(),end)
        vector<int> rightPostorder(postorder.begin()+leftInorder.size(),postorder.end());


        // 以下为日志
        cout << "----------" << endl;

        cout << "leftInorder :";
        for (int i : leftInorder) {
            cout << i << " ";
        }
        cout << endl;

        cout << "rightInorder :";
        for (int i : rightInorder) {
            cout << i << " ";
        }
        cout << endl;

        cout << "leftPostorder :";
        for (int i : leftPostorder) {
            cout << i << " ";
        }
        cout << endl;
         cout << "rightPostorder :";
        for (int i : rightPostorder) {
            cout << i << " ";
        }
        cout << endl;

        root->left=traversal(leftInorder,leftPostorder);
        root->right=traversal(rightInorder,rightPostorder);


        return root;
    }
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        if(inorder.size()==0 || postorder.size()==0) return nullptr;
        return traversal(inorder,postorder);
    }
};
```
以上代码在每次递归定义了新的vector,会耗时间，但是好理解

以下是用下标索引的代码：
```CPP
class Solution {
private:
    TreeNode* traversal (vector<int>& inorder, int inorderBegin, int inorderEnd, vector<int>& postorder, int postorderBegin, int postorderEnd) {
        if (postorderBegin == postorderEnd) return NULL;

        int rootValue = postorder[postorderEnd - 1];
        TreeNode* root = new TreeNode(rootValue);

        if (postorderEnd - postorderBegin == 1) return root;

        int delimiterIndex;
        for (delimiterIndex = inorderBegin; delimiterIndex < inorderEnd; delimiterIndex++) {
            if (inorder[delimiterIndex] == rootValue) break;
        }
        // 切割中序数组
        // 左中序区间，左闭右开[leftInorderBegin, leftInorderEnd)
        int leftInorderBegin = inorderBegin;
        int leftInorderEnd = delimiterIndex;
        // 右中序区间，左闭右开[rightInorderBegin, rightInorderEnd)
        int rightInorderBegin = delimiterIndex + 1;
        int rightInorderEnd = inorderEnd;

        // 切割后序数组
        // 左后序区间，左闭右开[leftPostorderBegin, leftPostorderEnd)
        int leftPostorderBegin =  postorderBegin;
        int leftPostorderEnd = postorderBegin + delimiterIndex - inorderBegin; // 终止位置是 需要加上 中序区间的大小size
        // 右后序区间，左闭右开[rightPostorderBegin, rightPostorderEnd)
        int rightPostorderBegin = postorderBegin + (delimiterIndex - inorderBegin);
        int rightPostorderEnd = postorderEnd - 1; // 排除最后一个元素，已经作为节点了

        cout << "----------" << endl;
        cout << "leftInorder :";
        for (int i = leftInorderBegin; i < leftInorderEnd; i++) {
            cout << inorder[i] << " ";
        }
        cout << endl;

        cout << "rightInorder :";
        for (int i = rightInorderBegin; i < rightInorderEnd; i++) {
            cout << inorder[i] << " ";
        }
        cout << endl;

        cout << "leftpostorder :";
        for (int i = leftPostorderBegin; i < leftPostorderEnd; i++) {
            cout << postorder[i] << " ";
        }
        cout << endl;

        cout << "rightpostorder :";
        for (int i = rightPostorderBegin; i < rightPostorderEnd; i++) {
            cout << postorder[i] << " ";
        }
        cout << endl;

        root->left = traversal(inorder, leftInorderBegin, leftInorderEnd,  postorder, leftPostorderBegin, leftPostorderEnd);
        root->right = traversal(inorder, rightInorderBegin, rightInorderEnd, postorder, rightPostorderBegin, rightPostorderEnd);

        return root;
    }
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        if (inorder.size() == 0 || postorder.size() == 0) return NULL;
        return traversal(inorder, 0, inorder.size(), postorder, 0, postorder.size());
    }
};
```

[从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

这道题的不同点，仅仅在于用前序数组切割中序数组，道理一样的。
前序数组，第一个元素是中。注意切割的时候，中序数组的一个元素在切割时去掉，前序数组的第一个元素在切割时去掉。
```CPP
class Solution {
private:
        TreeNode* traversal (vector<int>& inorder, int inorderBegin, int inorderEnd, vector<int>& preorder, int preorderBegin, int preorderEnd) {
        if (preorderBegin == preorderEnd) return NULL;

        int rootValue = preorder[preorderBegin]; // 注意用preorderBegin 不要用0
        TreeNode* root = new TreeNode(rootValue);

        if (preorderEnd - preorderBegin == 1) return root;

        int delimiterIndex;
        for (delimiterIndex = inorderBegin; delimiterIndex < inorderEnd; delimiterIndex++) {
            if (inorder[delimiterIndex] == rootValue) break;
        }
        // 切割中序数组
        // 中序左区间，左闭右开[leftInorderBegin, leftInorderEnd)
        int leftInorderBegin = inorderBegin;
        int leftInorderEnd = delimiterIndex;
        // 中序右区间，左闭右开[rightInorderBegin, rightInorderEnd)
        int rightInorderBegin = delimiterIndex + 1;
        int rightInorderEnd = inorderEnd;

        // 切割前序数组
        // 前序左区间，左闭右开[leftPreorderBegin, leftPreorderEnd)
        int leftPreorderBegin =  preorderBegin + 1;
        int leftPreorderEnd = preorderBegin + 1 + delimiterIndex - inorderBegin; // 终止位置是起始位置加上中序左区间的大小size
        // 前序右区间, 左闭右开[rightPreorderBegin, rightPreorderEnd)
        int rightPreorderBegin = preorderBegin + 1 + (delimiterIndex - inorderBegin);
        int rightPreorderEnd = preorderEnd;

        cout << "----------" << endl;
        cout << "leftInorder :";
        for (int i = leftInorderBegin; i < leftInorderEnd; i++) {
            cout << inorder[i] << " ";
        }
        cout << endl;

        cout << "rightInorder :";
        for (int i = rightInorderBegin; i < rightInorderEnd; i++) {
            cout << inorder[i] << " ";
        }
        cout << endl;

        cout << "leftPreorder :";
        for (int i = leftPreorderBegin; i < leftPreorderEnd; i++) {
            cout << preorder[i] << " ";
        }
        cout << endl;

        cout << "rightPreorder :";
        for (int i = rightPreorderBegin; i < rightPreorderEnd; i++) {
            cout << preorder[i] << " ";
        }
        cout << endl;


        root->left = traversal(inorder, leftInorderBegin, leftInorderEnd,  preorder, leftPreorderBegin, leftPreorderEnd);
        root->right = traversal(inorder, rightInorderBegin, rightInorderEnd, preorder, rightPreorderBegin, rightPreorderEnd);

        return root;
    }

public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (inorder.size() == 0 || preorder.size() == 0) return NULL;
        return traversal(inorder, 0, inorder.size(), preorder, 0, preorder.size());

    }
}
```

在区间左闭右开部分，我回去看了一眼数组部分，对应螺旋矩阵Ⅱ，发现螺旋矩阵、螺旋遍历二维数组，都没写。
看了一下螺旋矩阵Ⅱ,看了看写法，我确实也记不住了。
重点也是，区间以及更新的部分。

第二道题，晚一点再写。
[螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/description/)
给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。
```CPP
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if(matrix.size()==0 || matrix[0].size()==0)
        {
            return {};
        }

        int rows=matrix.size(), columns=matrix[0].size();
        vector<int> order;
        int left=0,right=columns-1,top=0,bottom=rows-1;
        while(left<=right && top<=bottom)
        {
            for(int column=left;column<=right;column++)
            {
                order.push_back(matrix[top][column]);
            }
            for(int row=top+1;row<=bottom;row++)
            {
                order.push_back(matrix[row][right]);
            }
            if(left<right && top<bottom)
            {
                for(int column=right-1;column>left;column--)
                {
                    order.push_back(matrix[bottom][column]);
                }
                for(int row=bottom;row>top;row--)
                {
                    order.push_back(matrix[row][left]);
                }
            }
            left++;
            right--;
            top++;
            bottom--;
        }
        
        return order;
    }
};

```

