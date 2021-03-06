# Tree & Binary Search

## 1. Tree

树是最简单的数据结构，主要是因为一般不会用迭代来处理树的内容，只有递归的话比较好想

### 94. Binary Tree Inorder Traversal

基础题，注意递归和迭代两种方法

递归解法：

```py
def inorder(self, ret, root):
    if root == None:
        return
    if root.left:
        self.inorder(ret, root.left)
    ret.append(root.val)
    if root.right:
        self.inorder(ret, root.right)

def inorderTraversal(self, root):
    """
    :type root: TreeNode
    :rtype: List[int]
    """
    ret = []
    self.inorder(ret, root)
    return ret
```

迭代解法：

```py
def inorderTraversal(self, root):
    """
    :type root: TreeNode
    :rtype: List[int]
    """
    ret, stk = [], []
    while root or stk:
        if root:
            stk.append(root)
            root = root.left
        else:
            root = stk.pop()
            ret.append(root.val)
            root = root.right
    return ret
```

### 226. Invert Binary Tree

基础题，要注意速度

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null)
        return root;
    TreeNode temp = invertTree(root.left);
    root.left = invertTree(root.right);
    root.right = temp;
    return root;
}
```

### 105 & 106. Construct Binary Tree from Preorder and Inorder Traversal (Inorder and Postorder Traversal)

这题目实际是在考察对前序和中序遍历的理解，前序遍历的话最左侧的项是root，以此去寻找中序遍历数组中的root，中序遍历数组里root的左边是左子树，右边是右子树，注意python里`pop(0)`是弹出最左侧，`pop()`是弹出最右侧

```py
def buildTree(self, preorder, inorder):
    if inorder:
        ind = inorder.index(preorder.pop(0))
        root = TreeNode(inorder[ind])
        root.left = self.buildTree(preorder, inorder[:ind])
        root.right = self.buildTree(preorder, inorder[ind+1:])
        return root
```

106的话和105类似，只不过改成了pop掉postorder的最后一项，而且需要注意，因为是从右向左pop，需要先构造右子树，再构造左子树，这个顺序不能搞错

```py
def buildTree(self, inorder, postorder):
    if not inorder or not postorder:
        return None
    root = TreeNode(postorder.pop())
    inorderIndex = inorder.index(root.val)
    root.right = self.buildTree(inorder[inorderIndex+1:], postorder)
    root.left = self.buildTree(inorder[:inorderIndex], postorder)
    return root
```

### 111. Minimum Depth of Binary Tree

Given a binary tree, find its minimum depth. The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

这个题目的陷阱在于如果一个节点只有一侧有儿子，你是不能返回min(left, right)的，因为此时有一侧返回值是0

```java
public int minDepth(TreeNode root) {
    if (root == null) return 0;
    // recursion
    int left = minDepth(root.left);
    int right = minDepth(root.right);
    if (left == 0 || right == 0)
        // one of them is 0
        return left + right + 1;
    else
        return Math.min(left, right) + 1;
}
```

### 114. Flatten Binary Tree to Linked List

### 297. Serialize and Deserialize Binary Tree

这题其实没说要你把二叉树弄成啥样……这个解法就是复杂版前序遍历而已

```py
def serialize(self, root):
    def doit(node):
        if node:
            vals.append(node.val)
            doit(node.left)
            doit(node.right)
        else:
            vals.append('#')
    vals = []
    doit(root)
    return vals

def deserialize(self, data):
    def doit():
        val = next(vals)
        if val == '#':
            return None
        else:
            node = TreeNode(val)
            node.left = doit()
            node.right = doit()
            return node
    vals = iter(data)
    return doit()
```

### Ternary Expression to Binary Tree

这题目leetcode里没有，可能因为太简单？两个解法，一个是栈解法：

1. 一开始推进去一个树节点
2. 每次向右推两格
    1. 遇到问号呢，就把当前栈顶节点的左子树加上这个节点
    2. 遇到冒号的话，先往外弹一个，然后如果遇到右子树满着的节点，一路从栈往外弹，直到遇到右子树为空的节点
    3. 每次循环的最后把节点推进去

另一个是递归，问号后面的【整个字符串】是左子树，冒号后面的【整个字符串】是右子树呗

```java
public TreeNode convert(String expr) {
    char[] exp = expr.toCharArray();
    if (exp.length == 0)
        return null;
    TreeNode root = new TreeNode(exp[0]);
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    for (int i = 1; i < exp.length; i += 2) {
        TreeNode node = new TreeNode(exp[i + 1]);
        if (exp[i] == '?')
            stack.peek().left = node;
        if (exp[i] == ':') {
            stack.pop();
            while (stack.peek().right != null)
                stack.pop();
            stack.peek().right = node;
        }
        stack.push(node);
    }
    return root;
}

Node convertExpression(char[] expression, int i) {
    if (i >= expression.length)
        return null;
    Node root = new Node(expression[i]);
    ++i;
    if (i < expression.length && expression[i]=='?')
        root.left = convertExpression(expression, i+1);
    else if (i < expression.length)
        root.right = convertExpression(expression, i+1);
    return root;
}
```

### 572. Subtree of Another Tree

这题其实没说要你把二叉树弄成啥样……这个解法就是复杂版前序遍历而已

```py
def isSubtree(self, s, t):
    def helper(s, t, root):
        if not s and not t:
            return True
        if not s or not t:
            return False
        if s.val == t.val:
            return (helper(s.left, t.left, False) and helper(s.right, t.right, False)) or (helper(s.left, t, True) or helper(s.right, t, True))
        else:
            if root:
                return helper(s.left, t, True) or helper(s.right, t, True)
            else:
                return False
    return helper(s, t, True)
```

### 104 & 124. Maximum Depth of Binary Tree & Binary Tree Maximum Path Sum

这题名为难题实际上没多难……重点是体会递归关系

```py
def maxDepth(self, root):
    if root is None:
        return 0
    return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

```java
int maxValue;

public int maxPathSum(TreeNode root) {
    maxValue = Integer.MIN_VALUE;
    maxPathDown(root);
    return maxValue;
}

private int maxPathDown(TreeNode node) {
    if (node == null) return 0;
    int left = Math.max(0, maxPathDown(node.left));
    int right = Math.max(0, maxPathDown(node.right));
    maxValue = Math.max(maxValue, left + right + node.val);
    return Math.max(left, right) + node.val;
}
```

## 2. Binary Search

二分搜索的思想是很简单的，但一次性写对也不容易：

```py
def binarysearch(self, arr, k):
    m = len(arr) // 2
    if k == arr[m]:
        return arr[m]
    elif k > arr[m]:
        return self.binarysearch(arr[m:], k)
    else:
        return self.binarysearch(arr[0:m], k)
```

另外不要拘泥于python提供的简单写法：

```py
def binarysearch(self, arr, k, lo, hi):
    m = (lo + hi) // 2
    if k == arr[m]:
        return arr[m]
    elif k < arr[m]:
        return self.binarysearch(arr, k, lo, m)
    else:
        return self.binarysearch(arr, k, m + 1, hi)
```

### 33 \& 81 \& 153 \& 154. Find Minimum in Rotated Sorted Array II

这个题和153基本一样，简单的二分搜索，唯一的问题是如果两侧一样的话怎么办呢？153题不含重复值，含重复值的154题，去掉重复值不就跟153一样了吗

```py
def findMin(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    l = len(nums)
    if l < 1:                   # corner cases
        return 0
    elif l == 1:
        return nums[0]
    elif nums[0] < nums[l-1]:   # local minimum
        return nums[0]
    elif nums[0] > nums[l-1]:   # global minimum
        return min(self.findMin(nums[0:l//2]), self.findMin(nums[l//2:]))
    elif nums[0] == nums[l-1]:  # remove the repeat value
        return self.findMin(nums[1:])
```

### 162. Find Peak Element

这个题目就是分情况讨论的二分搜索，如果找到了peak那当然好，找不到的话，增长趋势就向右找，下跌趋势就向左找，如果搜索范围只有一个那只能返回他，如果有两个就返回那个较大值，一共这是五种情况

```py
def findPeakElement(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    left, right = 0, len(nums) - 1
    while True:
        if left == right: return left  # edge case
        elif left + 1 == right:
            if nums[left] > nums[right]: return left
            else: return right
        m = (left + right) // 2
        if nums[m-1] < nums[m] and nums[m+1] < nums[m]:
            return m
        elif nums[m-1] > nums[m] > nums[m+1]:
            right = m - 1
        else:
            left = m + 1
```

#### 34. First/Last Occurrence of Binary Search

这个题目也是简单的二分搜索，唯一需要讨论的就是边界条件

```java
// low and high are ids, n is array length
int first(int arr[], int low, int high, int x, int n) {
    if(high >= low) {
        int mid = low + (high - low)/2;
        if (( mid == 0 || x > arr[mid - 1]) && arr[mid] == x)
            return mid;
        else if(x > arr[mid])
            return first(arr, (mid + 1), high, x, n);
        else
            return first(arr, low, (mid - 1), x, n);
    }
    return -1;
}

int last(int arr[], int low, int high, int x, int n) {
    if (high >= low) {
        int mid = low + (high - low)/2;
        if (( mid == n-1 || x < arr[mid + 1]) && arr[mid] == x)
            return mid;
        else if (x < arr[mid])
            return last(arr, low, (mid - 1), x, n);
        else
            return last(arr, (mid + 1), high, x, n);
    }
    return -1;
}
```

## 3. Binary Search Tree

### 95 & 96. Unique Binary Search Trees

Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1 ... n.

```md
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

这题目的题眼就是BST的性质：每个节点的左子树都比他小，右子树都比他大

```py
def generateTrees(self, n):
    if n == 0:
        return []
    def generate(first, last):
        trees = []
        for root in range(first, last+1):
            # B/c this is BST, left sub-tree < root < right sub-tree
            for left in generate(first, root-1):
                for right in generate(root+1, last):
                    node = TreeNode(root)
                    node.left = left
                    node.right = right
                    trees.append(node)
        return trees or [None]
    return generate(1, n)
```

注意96题因为有时间限制，单纯递归是不管用的，95因为要逐个生成每个树，所以不用递归也不可能

我们首先定义`G(n)`是长度n的序列能生成的独特BST树数量，`F(i,n)`是以i为root，从1到n的序列能生成的独特BST树数量，所以`G(n)=sum(F(i,n))`，而我们观察95题，得到`F(i,n) = G(i-1) * G(n-i)`

```java
public int numTrees(int n) {
    int [] G = new int[n+1];
    G[0] = G[1] = 1;
    for(int i=2; i<=n; ++i) {
        for(int j=1; j<=i; ++j) {
            G[i] += G[j-1] * G[i-j];
        }
    }
    return G[n];
}
```

### 98. Validate Binary Search Tree

这题用中序遍历简直弱智，所以最好别这么写

```py
def isValidBST(self, root):
    return self.validity(root, -2**32, 2**32)

def validity(self, root, left, right):
    if not root: return True
    if root.val >= right or root.val <= left: return False
    return self.validity(root.left, left, root.val) and self.validity(root.right, root.val, right)
```

### 99. Recover Binary Search Tree

Two elements of a binary search tree (BST) are swapped by mistake. Recover the tree without changing its structure.

```md
Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2
```

这题看着有点难，但实际上是利用的中序遍历的性质

1. BST的中序遍遍历必为顺序，那么每次遍历的时候记录prev跟cur
2. 第一次遇到`prev > cur`的话记录下来需要交换的`first = prev`
3. 第二次记录下来`second = cur`
4. 结尾处交换一下`first`和`second`即可

```java
TreeNode firstElement = null;
TreeNode secondElement = null;
TreeNode prevElement = new TreeNode(Integer.MIN_VALUE);
public void recoverTree(TreeNode root) {
    // In order traversal to find the two elements
    traverse(root);
    // Swap the values of the two nodes
    int temp = firstElement.val;
    firstElement.val = secondElement.val;
    secondElement.val = temp;
}
private void traverse(TreeNode root) {
    if (root == null)
        return;
    traverse(root.left);
    if (firstElement == null && prevElement.val >= root.val)
        firstElement = prevElement;
    if (firstElement != null && prevElement.val >= root.val)
        secondElement = root;
    prevElement = root;
    traverse(root.right);
}
```

### 108 & 109. Convert Sorted Array / List to Binary Search Tree

这俩题目一样的套路，分治法，找到中点为root，然后递归左侧构建左子树，递归右侧构建右子树

```java
public TreeNode sortedArrayToBST(int[] nums) {
    if (nums.length == 0)
        return null;
    else {
        int mid = nums[nums.length / 2];
        TreeNode temp = new TreeNode(mid);
        temp.left = sortedArrayToBST(Arrays.copyOfRange(nums, 0, nums.length / 2));
        temp.right = sortedArrayToBST(Arrays.copyOfRange(nums, nums.length / 2 + 1, nums.length));
        return temp;
    }
}
```

```py
def sortedListToBST(self, head):
    return self.listToBST(head, None)

def listToBST(self, head, tail):
    if head == tail:
        return None
    else:
        slow = head
        fast = head
        while fast != tail and fast.next != tail:
            slow = slow.next
            fast = fast.next.next
        root = TreeNode(slow.val)
        root.left = self.listToBST(head, slow)
        root.right = self.listToBST(slow.next, tail)
        return root
```

### 173. Binary Search Tree Iterator

这题就是利用BST的中序遍历性质，先把最左边的都塞进栈里，然后就类似于中序遍历，每次只有从栈里弹出来的元素才把他的右子树塞进去

```py
def __init__(self, root):
    self.stack = []
    while root:
        self.stack.append(root)
        root = root.left

# @return a boolean, whether we have a next smallest number
def hasNext(self):
    return len(self.stack) > 0

# @return an integer, the next smallest number
def next(self):
    node = self.stack.pop()
    x = node.right
    while x:
        self.stack.append(x)
        x = x.left
    return node.val
```

### 285. Inorder Successor in BST

货真价实的简单题，BST中序遍历是单调递增的，所以实际就是在BST里寻找比p大的最小值咯

```py
def inorderSuccessor(self, root, p):
    ret = None
    while root:
        if p.val < root.val:
            ret = root
            root = root.left
        else:
            root = root.right
    return ret
```

### 235/6 Lowest Common Ancestor of BST / Binary Tree

BST的话要注意方向，如果不是BST的话要注意他是如何变换状态的

```py
def lowestCommonAncestor(self, root, p, q):
    if root.val < p.val and root.val < q.val:
        return self.lowestCommonAncestor(root.right, p, q)
    elif root.val > p.val and root.val > q.val:
        return self.lowestCommonAncestor(root.left, p, q)
    else:
        return root

def lowestCommonAncestor(self, root, p, q):
    if root is None or root is p or root is q:
        return root
    else:
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        elif left:
            return left
        elif right:
            return right
```

#### 543. Diameter of Binary Tree

这个做法似乎是唯一解？但反正在二叉树问题里时间复杂度是很不漂亮的一种了

```py
def diameterOfBinaryTree(self, root):
    self.best = 1
    def depth(root):
        if not root: return 0
        depL = depth(root.left)
        depR = depth(root.right)
        self.best = max(self.best, depL + depR + 1)
        return max(depL, depR) + 1
    depth(root)
    return self.best - 1
```

## 4. Graph

### 133. Clone graph

这个问题的题眼是，复制一个图，其中每个节点都只能创造一次，第二次调用它的时候你就得重复调用以前使用过的节点

其他的其实就是简单的BFS而已

```py
def cloneGraph(self, node):
    if not node:
        return
    nodeCopy = UndirectedGraphNode(node.label)
    dic = {node: nodeCopy}
    queue = collections.deque([node])
    while queue:
        node = queue.popleft()
        for neighbor in node.neighbors:
            if neighbor not in dic:
                # store copy
                neighborCopy = UndirectedGraphNode(neighbor.label)
                dic[neighbor] = neighborCopy
                dic[node].neighbors.append(neighborCopy)
                queue.append(neighbor)
            else:
                # we met this node before, no need to insert it to queue
                # but necessary to add that edge
                dic[node].neighbors.append(dic[neighbor])
    return nodeCopy
```

### PocketGem. All paths from node to node
