# Binary Trees

**Binary Tree:** Hierarchical data structure; each node has at most 2 children (left, right).
**Node:** Contains data and pointers to left/right children.
**Root Node:** Topmost node; entry point to the tree.
**Children:** Nodes directly connected to a parent node.
**Leaf Node:** Node with no children (terminal node).
**Sibling:** Nodes having the same parent.
**Ancestor:** Any node on the path from a node to the root.

## Types of Binary Trees

**Full Binary Tree:** Every node has 0 or 2 children (no node with only 1 child).
**Complete Binary Tree:** All levels filled except possibly last, which is filled left to right.
**Perfect Binary Tree:** All internal nodes have 2 children, all leaves at same level, all levels fully filled.
**Balanced Binary Tree:** Heights of left/right subtrees of any node differ by at most 1; height ≈ log₂N.
**Degenerate Tree:** Each parent has only one child; tree becomes a linear chain (like a linked list).

## Key Properties

- Maximum number of nodes at level L (root at level 0) = 2^L.
- Maximum number of nodes in a binary tree of height H (height measured in edges) = 2^(H+1) - 1.
- Minimum height of a tree with N nodes is approximately log2(N).
- A completely skewed/degenerate tree can have height N-1.

## Binary Tree in Cpp

```cpp
#include <iostream>

// Node Constructor
struct Node {
    int data;        
    Node* left;   // Reference pointer to the left child node
    Node* right;  // Reference pointer to the right child node

    Node(int val) {
        data = val;   
        left = right = NULL;  
    }
};

int main() {
    // Creating a new node for the root of the binary tree using dynamic allocation
    Node* root = new Node(1);
    root->left = new Node(2);
    root->right = new Node(3);
    root->left->right = new Node(5);
}
```

## Binary Tree Traversals

### Traversal Types

- **DFS (Depth-First Search):**
  - Explores as deep as possible before backtracking.
  - Types:
    - **Inorder (L, Root, R):** Left subtree → Node → Right subtree
    - **Preorder (Root, L, R):** Node → Left subtree → Right subtree
    - **Postorder (L, R, Root):** Left subtree → Right subtree → Node
  - DFS uses recursion or a stack to traverse deeper levels of the tree before visiting nodes at the same level.

- **BFS (Breadth-First Search):**
  - Visits nodes level by level (Level Order Traversal).Nodes at a level are visited from left to right.
  - Uses a queue to process nodes at each level

### Traversing Binary tree from given root to array

### 1. Inorder Traversal (O(N))

> Recursive

```cpp
struct Node {
    int val;
    Node *left;
    Node *right;
    Node() : val(0), left(nullptr), right(nullptr) {}
    Node(int x) : val(x), left(nullptr), right(nullptr) {}
};

void inorder(Node* node, vector<int>& arr){
    if (node == nullptr) return;  // Base case: empty subtree
    inorder(node->left, arr);     // Traverse left subtree first
    arr.push_back(node->val);     // Visit current node (in-order)
    inorder(node->right, arr);    // Then traverse right subtree
}

vector<int> inorderTraversal(Node* root) {
    vector<int> arr;
    inorder(root, arr);
    return arr;
}
```

> Iterative using Stack

```cpp
vector<int> inorderTraversal(TreeNode* root) {
    stack<TreeNode*> st;  // Stack to simulate recursion
    TreeNode* node = root;
    vector<int> inorder;

    while (true) {
        if (node != NULL) {
            // Keep pushing left children onto stack
            st.push(node);
            node = node->left;
        } else {
            // No more left children, process node and go right
            if (st.empty() == true) break;  // Done traversing
            
            node = st.top();  // Get last node we saw
            st.pop();
            
            inorder.push_back(node->val);  // Process current node
            
            node = node->right;  // Move to right subtree
        }
    }
    return inorder;
};
```

### 2. Preorder (O(N))

> Recursive

```cpp
void preorder(Node* node, vector<int>& arr){
    if (node == nullptr) return;  // Base case: empty subtree
    arr.push_back(node->val);     // Visit current node first (pre-order)
    preorder(node->left, arr);    // Then traverse left subtree
    preorder(node->right, arr);   // Finally traverse right subtree
}

vector<int> preorderTraversal(Node* root) {
    vector<int> arr;
    preorder(root, arr);
    return arr;
}
```

> Iterative using stack

```cpp
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> preorder;
     
    if (root == nullptr) return preorder;  // Empty tree case
     
    stack<TreeNode*> st;
    st.push(root);  // Start with root
    
    while(!st.empty()) {
        root = st.top();
        st.pop();
        preorder.push_back(root->val);  // Visit node immediately
        
        // Push right first so left is processed first (stack is LIFO)
        if (root->right != nullptr) st.push(root->right);
        if (root->left != nullptr) st.push(root->left);
    }
    
    return preorder;
}
```

### 3. Postorder (O(N))

> Recursive

```cpp
void postorder(Node* node, vector<int>& arr){
    if (node == nullptr) return;        // Base case: empty subtree
    postorder(node->left, arr);         // Traverse left subtree first
    postorder(node->right, arr);        // Then traverse right subtree
    arr.push_back(node->val);           // Visit current node last (post-order)
}

vector<int> postorderTraversal(Node* root) {
    vector<int> arr;
    postorder(root, arr);
    return arr;
}
```

> Iterative using 2 stacks

```cpp
vector<int> postOrder(Node* root) {
    vector<int> postorder;

    if (root == NULL) return postorder;  // Empty tree case
    
    // Two stacks approach: st1 for processing, st2 for correct order
    stack<Node*> st1, st2;
    st1.push(root);

    // First pass: push nodes in reverse postorder to st2
    while(!st1.empty()){
        root = st1.top();
        st1.pop();
        st2.push(root);  // Add to second stack
        
        // Push children to first stack (left first)
        // This ensures reverse order in second stack
        if (root->left != NULL) st1.push(root->left);
        if (root->right != NULL) st1.push(root->right);
    }

    // Second pass: pop from st2 to get postorder
    while(!st2.empty()){
        postorder.push_back(st2.top()->data);
        st2.pop();
    }
    return postorder;
}
```

### 4. Level order traversal (returns an array with levels array in order) eg: [ [3],[9,20],[15,7,9,12] ]

```cpp
vector<vector<int>> levelOrder(Node* root) {
    vector<vector<int>> ans;
    // Empty tree case
    if (root == nullptr) return ans;
    
    // Queue for BFS traversal
    queue<Node*> q;
    q.push(root);
    
    while (!q.empty()){
        int n = q.size();  // Number of nodes at current level
        vector<int> level;  // Store nodes at current level
        
        // Process all nodes at current level
        for (int i = 0; i < n; i++){
            Node* node = q.front();
            q.pop();
            level.push_back(node->val);  // Add node value to current level
            
            // Add children to queue for next level processing
            if (node->left != NULL) q.push(node->left);
            if (node->right != NULL) q.push(node->right);
        }
        // Add current level to result
        ans.push_back(level);
    }
    return ans;
}
```

### Preorder Inorder PostOrder Traversals in one traversal

```cpp
vector<vector<int>> preInPostTraversal(Node* root) {
    vector<int> pre, in, post;

    if (root == NULL) return {} ;

    // Stack to maintain nodes and their traversal state
    // State 1: Preorder - Process node, go left
    // State 2: Inorder - Process node, go right
    // State 3: Postorder - Process node, pop
    stack<pair<Node*, int>> st;
    // Start with the root node and state 1 (preorder)
    st.push({root, 1});

    while (!st.empty()) {
        auto it = st.top();
        st.pop();

        // State 1: Preorder - process current node, then go left
        if (it.second == 1) {
            pre.push_back(it.first->data);  // Add to preorder
            // Move to state 2 (inorder) for this node
            it.second = 2;
            // Push the updated state back onto the stack
            st.push(it); 

            // Explore left subtree (preorder state)
            if (it.first->left != NULL) st.push({it.first->left, 1});
        }

        // State 2: Inorder - process current node, then go right
        else if (it.second == 2) {
            in.push_back(it.first->data);  // Add to inorder
            // Move to state 3 (postorder) for this node
            it.second = 3;
            // Push the updated state back onto the stack
            st.push(it); 

            // Explore right subtree (preorder state)
            if (it.first->right != NULL) st.push({it.first->right, 1});
        }

        // State 3: Postorder - process current node, then we're done with this node
        else {
            post.push_back(it.first->data);  // Add to postorder
        }
    }

    vector<vector<int>> result;
    result.push_back(pre);
    result.push_back(in);
    result.push_back(post);
    return result;
}
```

### MaxDepth/height of Binary tree O(N)

- we can use BFS(level order) but recursion suits as it can give ~ O(H) as stack space and O(N) time complexity.

```cpp
int maxDepth(TreeNode* root) {
    if (root == nullptr) return 0;

    // Recursively find height of left and right subtrees
    int lh = maxDepth(root->left);
    int rh = maxDepth(root->right);

    // Height = 1 (current node) + max height of subtrees
    return 1 + max(lh, rh);
    }
```

### Check for a Balanced Binary tree O(N)

```cpp
bool isBalanced(TreeNode* root) {
    return dfsHeight(root) != -1;
}

int dfsHeight(TreeNode* root){
    // Base case
    if (root == nullptr) return 0;

    // Check left subtree
    int lh = dfsHeight(root->left);
    if (lh == -1) return -1;
    
    // Check right subtree
    int rh = dfsHeight(root->right);
    if (rh == -1) return -1;

    // If height difference > 1, tree is unbalanced
    if (abs(lh - rh) > 1) return -1;

    return 1 + max(lh, rh);
}
```

### Diameter of Binary Tree O(N)

- The Diameter of a Binary Tree is the longest distance between any two nodes of that tree. This path may or may not pass through the root.

```cpp
int diameterOfBinaryTree(TreeNode* root) {
    int diameter = 0;
    height(root, diameter);
    return diameter;
}

int height(TreeNode* root, int& diameter){
    if (root == nullptr) return 0;
    
    int lh = height(root->left, diameter);
    int rh = height(root->right, diameter);

    // Update diameter if path through current node is longer
    // lh + rh = longest path passing through current node
    diameter = max(diameter, lh + rh);

    return 1 + max(lh, rh);
}
```

### Maximum sum along any path within the tree O(N)

- A path in a binary tree is defined as a sequence of nodes where each pair of adjacent nodes is connected by an edge. Nodes can only appear once in the sequence, and the path is not required to start from the root.

```cpp
int maxPathSum(TreeNode* root) {
    int maxsum = INT_MIN;
    findmaxsum(root, maxsum);
    return maxsum;
}

int findmaxsum(TreeNode* root, int& maxsum){
    if (root == nullptr) return 0;
    
    // Get max sum from left and right subtree (ignore negative sums)
    int leftsum = max(0, findmaxsum(root->left, maxsum));
    int rightsum = max(0, findmaxsum(root->right, maxsum));

    // leftsum + rightsum + root->val = path that passes through current node
    maxsum = max(maxsum, leftsum + rightsum + root->val);

    // Return max single path (can't use both left and right for parent calls)
    return root->val + max(leftsum, rightsum);
}
```

### Check whether two trees are identical

```cpp
bool isIdenticalTree(TreeNode* p, TreeNode* q) {
    // both trees empty, they are identical
    if (p == nullptr && q == nullptr)
        return true;
    
    // one tree empty and other not, they differ
    if (p == nullptr || q == nullptr)
        return false;

    // Trees are identical if: current values match AND left subtrees match AND right subtrees match
    return (p->val == q->val) && isIdenticalTree(p->left, q->left) &&
           isIdenticalTree(p->right, q->right);
}
```

### Zig Zag Traversal of a Binary Tree

```cpp
vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    vector<vector<int>> ans;
    if (root == nullptr) return ans;
    
    queue<TreeNode*> q;
    q.push(root);
    bool lefttoright = true;  // Toggle direction for zigzag pattern

    while(!q.empty()){
        int size = q.size();
        vector<int> level(size);
        
        // Process all nodes at current level
        for (int i = 0; i < size; i++){
            TreeNode* node = q.front();
            q.pop();
            
            // If left-to-right: use normal index, if right-to-left: reverse index
            int index = lefttoright ? i : size - 1 - i;
            level[index] = node->val;

            // Add children for next level (always left to right in queue)
            if (node->left != NULL) q.push(node->left);
            if (node->right != NULL) q.push(node->right);
        }
        
        // Toggle direction for next level
        lefttoright = !lefttoright;
        ans.push_back(level);
    }
    return ans;
}
```

### Boundary Traversal of a Binary Tree (Anticlockwise)

```cpp
class Solution {
public:
    // Function to check if a node is a leaf
    bool isLeaf(Node* root) {
        return !root->left && !root->right;
    }

    // Function to add the left boundary of the tree
    void addLeftBoundary(Node* root, vector<int>& res) {
        Node* curr = root->left;
        while (curr) {
            if (!isLeaf(curr)) res.push_back(curr->data);

            // Move to the left child if it exists otherwise move to the right child
            if (curr->left) curr = curr->left;
            else curr = curr->right;
        }
    }

    // Function to add the right boundary of the tree
    void addRightBoundary(Node* root, vector<int>& res) {
        Node* curr = root->right;
        vector<int> temp;
        while (curr) {
            if (!isLeaf(curr)) temp.push_back(curr->data);

            // Move to the right child if it exists otherwise move to the left child
            if (curr->right) curr = curr->right;
            else curr = curr->left;
        }
        // Reverse and add to the result
        for (int i = temp.size() - 1; i >= 0; i--) {
            res.push_back(temp[i]);
        }
    }

    // Function to add the leaves of the tree
    void addLeaves(Node* root, vector<int>& res) {
        if (isLeaf(root)) {
            res.push_back(root->data);
            return;
        }
        // Recursively add leaves of the left and right subtrees (preorder fashion)
        if (root->left) addLeaves(root->left, res);
        if (root->right) addLeaves(root->right, res);
    }

    vector<int> printBoundary(Node* root) {
        vector<int> res;
        if (!root) return res;

        // If the root is not a leaf add its value to the result
        if (!isLeaf(root)) res.push_back(root->data);

        // Add the left boundary, leaves & right boundary in order
        addLeftBoundary(root, res);
        addLeaves(root, res);
        addRightBoundary(root, res);

        return res;
    }
};
```

### Vertical Order Traversal of Binary Tree

- Problem Statement: Given a Binary Tree, return the Vertical Order Traversal of it starting from the Leftmost to the Rightmost. If there are multiple nodes passing through a vertical line, then they should be printed as level wise sorted.

- Vertical Coordinates -> x (vertical column)
- Level Coordinates -> y

```cpp
class Solution {
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        vector<vector<int>> ans;
        if (!root) return ans;

        queue<pair<TreeNode*, pair<int, int>>> q;
        q.push({root, {0, 0}});  // {{node, {x, y}}}

        map<int, map<int, vector<int>>> mpp; // mpp[vertical(x)][level(y)] -> vector of nodes 

        while (!q.empty()) {
            auto i = q.front();
            q.pop();

            TreeNode* node = i.first;
            int x = i.second.first;
            int y = i.second.second;

            mpp[x][y].push_back(node->val);

            if (node->left) q.push({node->left, {x - 1, y + 1}});
            if (node->right) q.push({node->right, {x + 1, y + 1}});
        }

        for (auto& x : mpp) {
            vector<int> col;
            for (auto& y : x.second) {
                sort(y.second.begin(), y.second.end());
                for (int val : y.second) {
                    col.push_back(val);
                }
            }
            ans.push_back(col);
        }
        return ans;
    }
};
```

#### Top View of Binary tree (optimal)

- A brute approach can be simple a vertical order traversal and getting the first entry of every vertical column

- Optimal can be using just a single map storing just first value at a level.

```cpp
class Solution {
public:
    vector<int> topView(Node* root) {
        vector<int> ans;
        if (root == NULL) return ans;

        map<int, int> mpp; // vertical level -> node value (only first encountered)

        queue<pair<Node*, int>> q; // {node, vertical_level}
        q.push({root, 0});

        while (!q.empty()) {
            auto it = q.front();
            q.pop();
            Node* node = it.first;
            int line = it.second;

            // If this vertical position is being visited for the first time, store it
            if (mpp.find(line) == mpp.end()) {
                mpp[line] = node->data;
            }

            if (node->left != NULL) q.push({node->left, line - 1});
            if (node->right != NULL) q.push({node->right, line + 1});
        }

        for (auto it : mpp) {
            ans.push_back(it.second);
        }
        return ans;
    }
};
```

- Similarly ***for bottom view*** , we can use brute force (a vertical order traversal and getting the first entry of every vertical column) and a optimal same may with a map , this time we always update the map value with node position , so last map value points to bottom value. (mpp[line] = node->data)

### Symmetrical Binary Tree

- A binary tree is symmetric if its left and right sides are mirror images of each other.

```cpp
class Solution {
public:
    // Function to check if left & right subtrees are symmetric
    bool isMirror(TreeNode* root1, TreeNode* root2) {
        if (root1 == nullptr || root2 == nullptr) {
            return root1 == root2; // // If one subtree is NULL, the othe must also be NULL for symmetry
        }
        return (root1->val == root2->val) &&
               isMirror(root1->left, root2->right) &&
               isMirror(root1->right, root2->left);
    }
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        return isMirror(root->left, root->right);
    }
};
```

### Print Path from Root to given Node in a Binary Tree

- It is given that x exists in tree

```cpp
class Solution {
public:
    bool path(TreeNode* root, vector<int>& arr, int x) {
        if (!root) return false;

        arr.push_back(root->val); // Add current node's value to the path

        if (root->val == x) return true;

        // Recursively search in left or right subtree
        if (path(root->left, arr, x) || path(root->right, arr, x)) {
            return true;
        }
        // If not found, backtrack and remove current node
        arr.pop_back();
        return false;
    }
    // Function to get the path from root to node with value x
    vector<int> solve(TreeNode* root, int x) {
        vector<int> arr;
        if (root == NULL) return arr;
        
        path(root, arr, x);
        return arr;
    }
};
```

### Lowest Common Ancestor (LCA) for two given Nodes

- The lowest common ancestor (LCA) is defined between two nodes p and q as the lowest node in tree that has both p and q as descendants (where we allow a node to be a descendant of itself).

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // Base case
        if (root == NULL || root == p || root == q) {
            return root;
        }
        
        // Search in left and right subtrees
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        
        if (left == NULL) {
            return right;
        } else if (right == NULL) {
            return left;
        } else { // Both left and right are not null, we found our result
            return root;
        }
    }
};
```

### Smallest Subtree with all the Deepest Nodes

- Given the root of a binary tree, the depth of each node is the shortest distance to the root. Return the smallest subtree such that it contains all the deepest nodes in the original tree. A node is called the deepest if it has the largest depth possible among any node in the entire tree.

```cpp
class Solution {
public:
    pair<int, TreeNode*> dfs(TreeNode* root){
        if (!root) return {0, nullptr};
        auto [d1, r1] = dfs(root->left);
        auto [d2, r2] = dfs(root->right);
        if (d1 == d2) return {1+d1, root};
        else if (d1 > d2) return {1+d1, r1};
        else return {1+d2, r2};
    }
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        return dfs(root).second;
    }
};
```

### Maximum Width of a Binary Tree

- The *maximum width* of a Binary Tree is the maximum width among all its levels. The width of a level is the number of nodes between the leftmost and rightmost nodes in the level including the null nodes between the endnodes.

- We assign index to each node from left to right from 0, and moving left as `(2*i + 1)` and right as `(2*i + 2)` where i = parent root index. Also at every level, we subtract the minindex of the level to prevent overflow of size. The width at a level will be (last_index - first_index + 1)
  
```cpp
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if (!root) return 0;

        int maxwidth = 0;
        queue<pair<TreeNode*, int>> q;
        q.push({root, 0});

        while (!q.empty()) {
            int n = q.size();
            long long minid = q.front().second; // the index of the first node at this level
            int first = 0, last = 0;

            for (int i = 0; i < n; i++) {
                auto it = q.front();
                q.pop();
                TreeNode* node = it.first;
                long long index = it.second - minid; // to make the index starting from zero

                if (i == 0) first = index;
                if (i == n - 1) last = index;

                if (node->left) q.push({node->left, 2*index + 1});
                if (node->right) q.push({node->right, 2*index + 2});
            }
            maxwidth = max(maxwidth, last-first+1);
        }
        return maxwidth;
    }
};
```

