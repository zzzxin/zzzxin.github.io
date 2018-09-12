##LeetCode-102. Binary Tree Level Order Traversal(层次遍历保存)

 - 非递归
 - 递归
***
###[题目链接](https://leetcode.com/problems/binary-tree-level-order-traversal/description/)
###题目
![这里写图片描述](https://img-blog.csdn.net/20180816143539539?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这个题目和 [LeetCode-637](https://blog.csdn.net/zxzxzx0119/article/details/81738741)基本一样，这个可以说更简单；
###非递归(BFS)

```
	 public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if(root == null )return res;
        Queue<TreeNode>queue = new LinkedList<>();
        queue.add(root);
        TreeNode top = null;
        while(!queue.isEmpty()){
            int n = queue.size();
            List<Integer>temp = new ArrayList<>();
            for(int i = 0; i < n; i++){
                top = queue.poll();
                temp.add(top.val);
                if(top.left != null)queue.add(top.left);
                if(top.right != null)queue.add(top.right);
            
            }
            res.add(temp);
        }
        return res;
    }
```

###递归(DFS)
先序: 

```
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        process(root, 0, res);
        return res;
    }
    
    public static void process(TreeNode root,int level,List<List<Integer>>res){
        if(root == null)return;
        if(level >= res.size()){
            List<Integer>temp = new ArrayList<>();
            temp.add(root.val);
            res.add(temp);
        }
        else {
            res.get(level).add(root.val);
        }
        process(root.left,level+1,res);
        process(root.right,level+1,res);
    }
```

中序: 
```
	public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        process(root, 0, res);
        return res;
    }

    public static void process(TreeNode root,int level,List<List<Integer>>res){
        if(root == null)return;
        if(level >= res.size()){
            res.add(new ArrayList<>());
        }
        process(root.left,level+1,res);
        res.get(level).add(root.val);
        process(root.right,level+1,res);
    }
    
```
后序:

```
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        process(root, 0, res);
        return res;
    }

    public static void process(TreeNode root,int level,List<List<Integer>>res){
        if(root == null)return;
        if(level >= res.size()){
            res.add(new ArrayList<>());
        }
        process(root.left,level+1,res);
        process(root.right,level+1,res);
        res.get(level).add(root.val);
    }
    
```