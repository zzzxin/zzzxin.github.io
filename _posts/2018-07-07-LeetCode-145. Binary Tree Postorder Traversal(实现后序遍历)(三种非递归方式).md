##LeetCode-145. Binary Tree Postorder Traversal(实现后序遍历)(三种非递归方式)

 - 递归
 - 双栈法
 - 设置pre结点法
 - morris遍历

***
###[题目链接](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)
###题目
![这里写图片描述](https://img-blog.csdn.net/20180831163458125?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
###递归
这个不多说。
```
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer>res = new ArrayList<>();
        pos(root,res);
        return res;
    }

    public static void pos(TreeNode root,List<Integer>res){
        if(root == null)return;
        pos(root.left,res);
        pos(root.right,res);
        res.add(root.val);
    }
```
###双栈法

 - 我们前序遍历是：中->左->右，所以压栈的顺序是 右->左；
 - 我们可以实现遍历: 中->右->左，所以压栈的顺序是  左->右；
 - 利用上面这个 ，我们遍历的时候是中->右->左，但是我们不遍历，将**这个压入另一个栈，或者逆序回来**，**就能得到左->右->中；**

![这里写图片描述](https://img-blog.csdn.net/20180816162939265?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180816162827179?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
```
     /**
     * 非递归
     * 前序非递归: 中左右  入栈:  右左
     * 我们可以实现 : 中右左 入栈:  左右(1)
     * 后续非递归: 左右中
     * 上一个(1)位置该打印的时候不打印，压入到另一栈就是后续遍历
     */
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        if(root == null)return res;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        TreeNode cur = null;
        while(!stack.isEmpty()){
            cur = stack.pop();
            res.addFirst(cur.val);//代替了另一个栈
            if(cur.left != null)stack.push(cur.left);
            if(cur.right != null)stack.push(cur.right);
        }
        return res;
    }
```
***
###设置pre结点

> 设置pre结点 : 
> 
 - 对于任一结点p，先将其入栈。若p不存在左孩子和右孩子，则可以直接访问它。或者p存在左孩子或者右孩子，但是左孩子和右孩子都已经被访问过了，则可以直接访问该结点。 
 - 若非上述两种情况，则将右孩子和左孩子依次入栈。这样可以保证每次取栈顶元素时，左孩子在右孩子前面被访问，根结点在左孩子和右孩子访问之后被访问。

```
  public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer>res = new ArrayList<>();
        if(root == null)return res;
        Stack<TreeNode>stack = new Stack<>();
        stack.push(root);
        TreeNode cur = null,pre = null;
        while(!stack.isEmpty()){
            cur = stack.peek();  //不能直接pop
            if((cur.left == null && cur.right == null) || ((pre != null)&&(pre == cur.left || pre == cur.right))){
                res.add(cur.val);
                pre = cur;
                stack.pop();
            }else {  //否则不能访问
                if(cur.right != null)stack.push(cur.right);
                if(cur.left != null)stack.push(cur.left);
            }
        }
        return res;
    }
```
***
###morris遍历
morris遍历看[这篇博客](https://blog.csdn.net/zxzxzx0119/article/details/81669909)
```
     //morris后续
      public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer>res = new ArrayList<>();
        if(root == null)return res ;
        TreeNode cur = root;
        TreeNode mostRight = null;

        while(cur != null){
            mostRight = cur.left;
            if(mostRight != null){

                while(mostRight.right != null && mostRight.right != cur){
                    mostRight = mostRight.right;
                }

                if(mostRight.right == null){
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                }else { //第二次来的时候
                    mostRight.right = null;
                    printEdge(cur.left,res); //打印左子树的右边界
                }
            }
            cur = cur.right;
        }
        printEdge(root,res); //最后打印整棵树的右边界
        return res;
      }

    //打印边界
    private static void printEdge(TreeNode head,List<Integer>res) {
        //先逆序边界
        TreeNode tail = reverseEdge(head);
        //打印
        TreeNode cur = tail;
        while(cur != null){
            res.add(cur.val);
            cur = cur.right;
        }
        //再逆序回来
        reverseEdge(tail);
    }

    //有点类似链表的逆序
    private static TreeNode reverseEdge(TreeNode cur) {
        TreeNode pre = null;
        TreeNode next = null;
        while(cur != null){
            next = cur.right;//先保存下一个
            cur.right = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }

```