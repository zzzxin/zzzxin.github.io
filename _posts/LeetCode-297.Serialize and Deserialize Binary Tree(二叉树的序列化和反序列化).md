###LeetCode-297.Serialize and Deserialize Binary Tree(二叉树的序列化和反序列化)
 - 题意及解题思路
 - 层序序列化和层序反序列化
 - 完整测试代码
***
###[题目链接](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)
###题意
![这里写图片描述](https://img-blog.csdn.net/20180831160900232?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当然上面这个是按层序列化，我们也可以按前序序列化，只要能通过序列化的结果还原二叉树即可。

###解析
 **前序序列化，就是将当前的树按照前序的方式生成一个字符串，如果结点不为空，就是结点的value，如果结点为null，就用”null“来代替。**

逻辑很简单，直接上前序序列化代码

```
	public String serialize(TreeNode root){
        StringBuilder res = new StringBuilder();
        serialByPre(root,res);
        res.deleteCharAt(res.length()-1);
        return res.toString();
    }
    public  void serialByPre(TreeNode root,StringBuilder sb){
        if(root == null){
            sb.append("null,");
            return;
        }
        sb.append(root.val + ",");
        serialByPre(root.left,sb);
        serialByPre(root.right,sb);
    }
```
然后就是前序反序列化，按照前序序列化的方式，重建二叉树

```
	private int index;
    //使用给定的字符串重建二叉树
    public TreeNode deserialize(String data){
        String[] arr = data.split(","); //得到字符串数组
        index = 0;
        return reconPre(arr);
    }

    //递归前序建立二叉树
    private TreeNode reconPre(String[] arr) {
        if(index >= arr.length)return null;
        String val = arr[index];
        if(val.equals("null")){
            return null;
        }
        TreeNode head = new TreeNode(Integer.valueOf(val));
        index++;
        head.left = reconPre(arr);
        index++;
        head.right = reconPre(arr);
        return head;
    }
```
***
###层序序列化和层序反序列化

> 层序序列化，其实和前序差不多，会层序遍历就差不多，一个逻辑。

直接上代码了

```
	//层序序列化二叉树
    public static String serialByLevel(Node head){
        if(head == null)return "#,";
        String res = head.value + ",";
        Queue<Node>queue = new LinkedList<>();
        queue.add(head);
        Node now = null;
        while(!queue.isEmpty()){
            now = queue.poll();
            if(now.left != null){
                queue.offer(now.left);
                res += (now.left.value + ",");
            } else res += "#,";

            if(now.right != null){
                queue.offer(now.right);
                res += (now.right.value + ",");
            }else res += "#,";
        }
        return res;
    }

	//根据序列化结果重构
    public static Node reconByLevelString(String levelStr){
        String[] values = levelStr.split(",");
        int index = 0;
        Node head = reconLevelNode(values[index++]);

        Queue<Node>queue = new LinkedList<>();
        if(head != null){
            queue.offer(head);
        }
        Node now = null;
        while(!queue.isEmpty()){
            now = queue.poll();
            now.left = reconLevelNode(values[index++]);
            now.right = reconLevelNode(values[index++]);
            if(null != now.left) queue.offer(now.left);
            if(null != now.right) queue.offer(now.right);
        }
        return head;
    }

    //按照str的值得到一个结点
    public static Node reconLevelNode(String str){
        return str.equals("#") ? null : new Node(Integer.valueOf(str));
    }
```
***
###完整测试代码

> 包括了前序序列化和反序列化，以及层序序列化和反序列化，以及[二叉树的直观打印方法](https://blog.csdn.net/zxzxzx0119/article/details/81096554)。

```
package Algorithm.Zuo.LessionPrimary;


import sun.reflect.generics.tree.Tree;

import java.util.LinkedList;
import java.util.Queue;

/**
 * 序列化和反序列化二叉树
 *
 */
public class SerializeAndReconstructTree {


    private static class TreeNode{
        public int val;
        public TreeNode left;
        public TreeNode right;

        public TreeNode(int value) {
            this.val = value;
        }
    }


    public static String serialize(TreeNode root){
        StringBuilder res = new StringBuilder();
        serialByPre(root,res);
        res.deleteCharAt(res.length()-1);
        return res.toString();
    }

    //前序序列化二叉树
    public static void serialByPre(TreeNode root,StringBuilder sb){
        if(root == null){
            sb.append("null,");
            return;
        }
        sb.append(root.val + ",");
        serialByPre(root.left,sb);
        serialByPre(root.right,sb);
    }

    private int index;
    //使用给定的字符串重建二叉树
    public TreeNode deserialize(String data){
        String[] arr = data.split(","); //得到字符串数组
        index = 0;
        return reconPre(arr);
    }

    //递归前序建立二叉树
    private TreeNode reconPre(String[] arr) {
        if(index >= arr.length)return null;
        String val = arr[index];
        if(val.equals("null")){
            return null;
        }
        TreeNode head = new TreeNode(Integer.valueOf(val));
        index++;
        head.left = reconPre(arr);
        index++;
        head.right = reconPre(arr);
        return head;
    }




    //层序序列化二叉树
    public static String serialByLevel(TreeNode head){
        if(head == null)return "null,";
        String res = head.val + ",";
        Queue<TreeNode>queue = new LinkedList<>();
        queue.add(head);
        TreeNode now = null;
        while(!queue.isEmpty()){
            now = queue.poll();
            if(now.left != null){
                queue.offer(now.left);
                res += (now.left.val + ",");
            } else res += "null,";

            if(now.right != null){
                queue.offer(now.right);
                res += (now.right.val + ",");
            }else res += "null,";
        }
        return res;
    }

    public static TreeNode reconByLevelString(String levelStr){
        String[] values = levelStr.split(",");
        int index = 0;
        TreeNode head = reconLevelNode(values[index++]);

        Queue<TreeNode>queue = new LinkedList<>();
        if(head != null){
            queue.offer(head);
        }
        TreeNode now = null;
        while(!queue.isEmpty()){
            now = queue.poll();
            now.left = reconLevelNode(values[index++]);
            now.right = reconLevelNode(values[index++]);
            if(null != now.left) queue.offer(now.left);
            if(null != now.right) queue.offer(now.right);
        }
        return head;
    }

    //按照str的值得到一个结点
    public static TreeNode reconLevelNode(String str){
        return str.equals("null") ? null : new TreeNode(Integer.valueOf(str));
    }




    /**
     * @param head  传入的节点
     * @param height　　层数(根节点为0)
     * @param to     表示的特定节点  H表示根节点   ^表示父亲节点在左上方　v表示父亲节点在左下方
     * @param len　　　指定每一个节点打印的宽度
     */
    public static void printTree(TreeNode head,int height,String to,int len){
        if(head == null){
            return;
        }
        printTree(head.right,height + 1,"v",len);

        String val = to + head.val + to;  //两边指示的字符
        int lenV = val.length();
        int lenL = (len - lenV)/2;  //左边的空格(分一半)
        int lenR = len - lenV - lenL; // 右边的空格
        System.out.println( getSpace(len * height) + getSpace(lenL) + val + getSpace(lenR));

        printTree(head.left,height + 1,"^",len);
    }
    //获取指定的空格
    public static String getSpace(int len){
        StringBuffer str = new StringBuffer();
        for(int i = 0; i < len; i++) str.append(" ");
        return str.toString();
    }


    public static void main(String[] args) {
        //建上图的树
        TreeNode head = new TreeNode(1);
        head.left = new TreeNode(2);
        head.right = new TreeNode(3);
        head.right.left = new TreeNode(4);
        head.right.right = new TreeNode(5);

        System.out.println("--------------pre test --------------");
        printTree(head,0,"H",7); //先打印一下
        String preStr = serialize(head);
        System.out.println("after pre serial's string is :  " + "\n" + preStr);
        TreeNode newHead = new SerializeAndReconstructTree().deserialize(preStr);
        System.out.println("the recontree is : ");
        printTree(newHead,0,"H",7);

        System.out.println("--------------level test--------------");
        String levelStr = serialByLevel(head);
        System.out.println("after level serial's string is :  " + "\n" + levelStr);
        newHead = reconByLevelString(levelStr);
        printTree(newHead,0,"H",7);

    }
}

```
上面例子的测试结果
![这里写图片描述](https://img-blog.csdn.net/20180727085322116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

