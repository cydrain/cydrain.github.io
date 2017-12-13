---
title: AVL-Tree详解
date: 2017-12-13 20:41:42
tags:
categories: Tree
---

AVL-Tree 即平衡二叉搜索树，理解的难点就是如何对结点进行旋转以保持二叉树的平衡。

AVL-Tree 的结点定义如下：
```c
struct AVL_Node {
    int val;
    int height;
    AVL_Node *left;
    AVL_Node *right;
    AVL_Node(int x) : val(x), height(0), left(NULL), right(NULL) {}
};
```
`height`记录了该结点的高度，空结点高度为-1，叶子结点高度为0。

AVL-Tree 的旋转只有4种情况：左左、右右、左右、右左。
左左、右右的情况比较好理解，见下图及代码所示。
`node`为失衡的结点，`top`为旋转后的根结点。
```c
/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *                                                             *
 *          3                                                  *
 *         /       (LL)         2                              *
 *        2       ======>      / \                             *
 *       /                    1   3                            *
 *      1                                                      *
 *                                                             *
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */
AVL_Node* rotateLL(AVL_Node* node) {
    AVL_Node* top = node->left;
    node->left = top->right;
    top->right = node;
    node->height = max(getHeight(node->left), getHeight(node->right)) + 1;
    top->height = max(getHeight(top->left), getHeight(top->right)) + 1;
    cout << "Rotate LL for node " << node->val << endl;
    return top;
}

/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *                                                             *
 *      1                                                      *
 *       \          (RR)         2                             *
 *        2        ======>      / \                            *
 *         \                   1   3                           *
 *          3                                                  *
 *                                                             *
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */
AVL_Node* rotateRR(AVL_Node* node) {
    AVL_Node* top = node->right;
    node->right = top->left;
    top->left = node;
    node->height = max(getHeight(node->left), getHeight(node->right)) + 1;
    top->height = max(getHeight(top->left), getHeight(top->right)) + 1;
    cout << "Rotate RR for node " << node->val << endl;
    return top;
}
```

左右的情况可以通过一次右右旋转，转换为左左。
同样，右左的情况可以通过一次左左旋转，转换为右右。
```c
/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *                                                             *
 *        3                     3                              *
 *       /        (RR)         /        (LL)          2        *
 *      1        ======>      2        ======>       / \       *
 *       \                   /                      1   3      *
 *        2                 1                                  *
 *                                                             *
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */
AVL_Node* rotateLR(AVL_Node* node) {
    node->left = rotateRR(node->left);
    return rotateLL(node);
}

/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *                                                             *
 *      1                    1                                 *
 *       \         (LL)       \         (RR)          2        *
 *        3       ======>      2       ======>       / \       *
 *       /                      \                   1   3      *
 *      2                        3                             *
 *                                                             *
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */
AVL_Node* rotateRL(AVL_Node* node) {
    node->right = rotateLL(node->right);
    return rotateRR(node);
}
```

添加结点的时候，如果添加到左子树并发生失衡，那么一定是左子树高度比右子树高。
如果添加到右子树并发生失衡，那么一定是右子树高度比左子树高。
```c
AVL_Node* addNode(AVL_Node* root, int v) {
    if (root == NULL) {
        root = new AVL_Node(v);
        cout << "Add node " << root->val << endl;
    } else {
        if (v < root->val) {
            root->left = addNode(root->left, v);

            if (getHeight(root->left) - getHeight(root->right) == 2) {
                if (v < root->left->val) {
                    root = rotateLL(root);
                } else {
                    root = rotateLR(root);
                }
            }
        } else if (v > root->val) {
            root->right = addNode(root->right, v);

            if (getHeight(root->right) - getHeight(root->left) == 2) {
                if (v > root->right->val) {
                    root = rotateRR(root);
                } else {
                    root = rotateRL(root);
                }
            }
        } else {
            cout << "Node with value " << v << " already exist!" << endl;
        }
        root->height = max(getHeight(root->left), getHeight(root->right)) + 1;
    }
    return root;
}
```

删除结点的时候，如果从左子树删除并发生失衡，那么一定是右子树的高度高于左子树；
如果从右子树删除并发生失衡，那么一定是左子树的高度高于右子树。
```c
AVL_Node* delNode(AVL_Node* root, int v) {
    if (root == NULL) {
        cout << "Node with value " << v << " NOT exist!" << endl;
        return NULL;
    }
    if (v < root->val) {
        root->left = delNode(root->left, v);
        
        // height(right) > height(left)
        if (getHeight(root->right) - getHeight(root->left) == 2) {
            if (root->right->right) {
                root = rotateRR(root);
            } else {
                root = rotateRL(root);
            }
        }
    } else if (v > root->val) {
        root->right = delNode(root->right, v);
        
        // height(left) > height(right)
        if (getHeight(root->left) - getHeight(root->right) == 2) {
            if (root->left->left) {
                root = rotateLL(root);
            } else {
                root = rotateLR(root);
            }
        }
    } else {
        cout << "Del node " << v << endl;
        if (root->left && root->right) {
            root->val = findMin(root->right);
            root->right = delNode(root->right, root->val);
        } else {
            root = (root->left ? root->left : root->right);
            if (root == NULL) return NULL;
        }
    }
    root->height = max(getHeight(root->left), getHeight(root->right)) + 1;
    return root;
}
```

完整代码请参考: [https://github.com/cydrain/Coding_Practice/blob/master/myCode/AVL-Tree/AVL-Tree/main.cpp]

