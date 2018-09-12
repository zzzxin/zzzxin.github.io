##LeetCode-110. Balanced Binary Tree(判断一棵树是否是平衡二叉树)

 - O(n*logn)解法
 - O(n)解法
###[题目链接](https://leetcode.com/problems/balanced-binary-tree/description/)
####题目

> 给定一棵二叉树，判断该二叉树是否是平衡二叉树。

这个题目[剑指offer](https://blog.csdn.net/zxzxzx0119/article/details/80357393)中也写过。
####解析
###O(n*logn)解法
> 首先我们知道平衡二叉树**是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树**我们可以使用一个获取树的高度的函数。然后递归比较左右子树是不是平衡二叉树且左右子树的高度不超过1即可。

![这里写图片描述](https://img-blog.csdn.net/20180816145409500?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
 //简单的方式
    public boolean isBalanced(TreeNode root) {
        if(root == null)return true;
        return isBalanced(root.left) && isBalanced(root.right) && Math.abs(getHeight(root.left)-getHeight(root.right)) <= 1;
    }

    //获取二叉树的高度
    public static int getHeight(TreeNode root){
        if(root == null)return 0;
        return Math.max(getHeight(root.left),getHeight(root.right)) + 1;
    }
```
***
###O(n)解法
> 这个题目重点是理解使用递归函数的技巧和套路： 
> 所以假如我们要判断一个以x开头的结点是否为一颗平衡二叉树，则要满足以下几点 : 
> 
 - 它的左子树要给它一个左子树本身是不是平衡二叉树的信息；
 - 它的右子树要给它一个右子树本身是不是平衡二叉树的信息；
 - 它的左子树要给它左子树高度的信息；
 - 它的右子树要给它右子树高度的信息；

所以我们知道上面的几点之后，可以完全按照上面写出一个递归结构函数，因为子树返回的信息中既包含高度信息，又包含子树是不是也是一颗平衡二叉树的信息，所以这里把这个信息封装成一个结构。
**这里和O(n*logn方法不同的是，我们求的是一个结构体，递归函数同时返回了两个信息: 高度和子树是否平衡，如果不平衡，我们就不再判断直接false了)。**

```
	 //二叉树子树返回的类型
    public static class ReturnData{
        public int height;
        public boolean isB;

        public ReturnData(int height, boolean isB) {
            this.height = height;
            this.isB = isB;
        }
    }

    public boolean isBalanced(TreeNode root) {
        return process(root).isB;
    }
      
    //判断是否是一颗平衡二叉树的递归函数
    public static ReturnData process(TreeNode head){
        if(head == null)return new ReturnData(0,true); //空树是平衡的
        ReturnData leftRes = process(head.left);
        if(!leftRes.isB) return new ReturnData(0,false); //如果isB已经是false，高度就不用判断了
        ReturnData rightRes = process(head.right);
        if(!rightRes .isB) return new ReturnData(0,false);
        if(Math.abs(leftRes.height-rightRes.height) > 1) return new ReturnData(0,false);
        return new ReturnData(Math.max(leftRes.height,rightRes.height) + 1,true);
    }
```
如下面的树，其中的返回类型的变化，以及子树的遍历
![这里写图片描述](https://img-blog.csdn.net/20180719082957931?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***
上面的结构体也可以简化的写成下面的样子: 
```
	public boolean isBalanced(TreeNode root) {
        return getHeight(root) > -1;
    }
    public int getHeight(TreeNode root){
        if(root == null) return 0;
        int left = getHeight(root.left), right = getHeight(root.right);
        if(Math.abs(left - right) > 1 || left == -1 || right == -1) return -1;
        return Math.max(left + 1, right + 1);
    }
```

