###LeetCode-222. Count Complete Tree Nodes(统计完全二叉树的结点个数)
###[题目链接](https://leetcode.com/problems/count-complete-tree-nodes/description/)
###题目
> 给出一棵完全二叉树的根节点，求完全二叉树的结点个数，**要求时间复杂度小于O(n)**。
> 完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。**若最底层为第 h 层，则该层包含 1~ 2h 个节点。**
###解析
> 不知道完全二叉树概念看[这个博客](https://blog.csdn.net/zxzxzx0119/article/details/81112061)
> 遍历整个二叉树的方法是不行的，因为时间复杂度要求小于O(n)。
> 具体求法如下 : 
> 
 - 先求树的高度，也就是看树的最左结点能到哪一层，层数记为h。
 - 然后下面求解要分两种情况，都是递归过程，node表示当前结点，level表示当前结点所在的层数(根节点为第一层)，h是整棵树的层数。先看第一种情况 : 
![这里写图片描述](https://img-blog.csdn.net/20180719150214116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 再看第二种情况，这种情况是node的右子树没有来到最后一层 : 

![这里写图片描述](https://img-blog.csdn.net/20180719150739719?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
 
所以经过上面的分析 ，可以写出下面的代码 :

```
	  public int countNodes(TreeNode root){
        if(root == null)return 0;
        return ct(root,1,mostLeftLevel(root,1));
    }

    private static int ct(TreeNode node, int level, int h) {
        if(level == h){// 叶子结点,只有一个结点
            return 1;
        }
        if(mostLeftLevel(node.right,level + 1) == h){//如果右孩子的最左边界到了最后一层
            return (1 << (h-level)) + ct(node.right,level+1,h);
        }else{
            return (1 << (h-level-1)) + ct(node.left,level+1,h);
        }
    }

    private static int mostLeftLevel(TreeNode node, int level) {
        while(node != null){
            level++;
            node = node.left;
        }
        return --level;
    }


```
稍微改动一下，不用记录层数，用下面的写法意思是一样的 : 

```
	private static int mostLeftLevel2(TreeNode node){
        if(node == null)return 0;
        return mostLeftLevel2(node.left) + 1;
    }

    //方法二 不用记录层数
    public int countNodes2(TreeNode root){
        if(root == null){
            return 0;
        }
        int leftH = mostLeftLevel2(root.left); //求出左结点最左边高度
        int rightH = mostLeftLevel2(root.right);//求出右结点最左边高度
        if(leftH == rightH) return (1<<leftH) + countNodes2(root.right);
        else return (1<<rightH) + countNodes2(root.left); //不相等肯定是right = left-1
    }

```
　完整测试代码 : 

```
/**
 * 统计完全二叉树结点个数
 */
public class CompleteTreeNodeNumber {

    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;

        public TreeNode(int data) {
            this.val = data;
        }
    }


    public int countNodes(TreeNode root){
        if(root == null)return 0;
        return ct(root,1,mostLeftLevel(root,1));
    }

    private static int ct(TreeNode node, int level, int h) {
        if(level == h){// 叶子结点,只有一个结点
            return 1;
        }
        if(mostLeftLevel(node.right,level + 1) == h){//如果右孩子的最左边界到了最后一层
            return (1 << (h-level)) + ct(node.right,level+1,h);
        }else{
            return (1 << (h-level-1)) + ct(node.left,level+1,h);
        }
    }

    private static int mostLeftLevel(TreeNode node, int level) {
        while(node != null){
            level++;
            node = node.left;
        }
        return --level;
    }

    private static int mostLeftLevel2(TreeNode node){
        if(node == null)return 0;
        return mostLeftLevel2(node.left) + 1;
    }

    //方法二 不用记录层数
    public int countNodes2(TreeNode root){
        if(root == null){
            return 0;
        }
        int leftH = mostLeftLevel2(root.left); //求出左结点最左边高度
        int rightH = mostLeftLevel2(root.right);//求出右结点最左边高度
        if(leftH == rightH) return (1<<leftH) + countNodes2(root.right);
        else return (1<<rightH) + countNodes2(root.left); //不相等肯定是right = left-1
    }

    public static void main(String[] args) {
        TreeNode head = new TreeNode(1);
        head.left = new TreeNode(2);
        head.right = new TreeNode(3);
        head.left.left = new TreeNode(4);
        head.left.right = new TreeNode(5);
        head.right.left = new TreeNode(6);
        head.right.right = new TreeNode(7);
        head.left.left.left = new TreeNode(8);
        head.left.left.right = new TreeNode(9);
        head.left.right.left = new TreeNode(10);
        head.left.right.right = new TreeNode(11);
        head.right.left.left = new TreeNode(12);
        System.out.println(new CompleteTreeNodeNumber().countNodes(head));
        System.out.println(new CompleteTreeNodeNumber().countNodes2(head));
    }
}
```