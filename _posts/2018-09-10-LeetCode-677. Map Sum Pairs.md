---
layout: post
title: "Map Sum Pairs(键值映射)(字典树变形)"
date: 2018-09-10 11:15:06 
description: "字典树"
tag: 字典树

---



##LeetCode-677. Map Sum Pairs(键值映射)(字典树变形)
####[题目链接](https://leetcode-cn.com/problems/map-sum-pairs/description/)
####题目
![这里写图片描述](https://img-blog.csdn.net/20180908123313471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
####解析
<font color = red>做这题之前先学[字典树基础](https://blog.csdn.net/zxzxzx0119/article/details/81134479)。这个题目和普通字典树不同的是结点内部存放的是val，一个具体的值，不是path和end。
 
 - insert()也没什么好说的，注意找到末尾结点之后，维护val；
 - **注意求这里的sum，分为两部，第一步先找到对应字符串的结尾结点，然后使用递归来求解它所有孩子的结点的值的和；**

![这里写图片描述](https://img-blog.csdn.net/20180908124456505?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
	   private class Node{
            public int val;
            public Node[] next;//使用整数表示字符　c - 'a'

            public Node() {
                val = 0;
                next = new Node[26];
            }
        }

        private Node root;

        public MapSum() {
            root = new Node();
        }

        public void insert(String key, int val) {
            if(key == null)
                return;
            Node cur = root;
            int index = 0;
            for(int i = 0; i < key.length(); i++){
                index = key.charAt(i) - 'a';
                if(cur.next[index] == null)
                    cur.next[index] = new Node();
                cur = cur.next[index];
            }
            cur.val = val;
        }

        public int sum(String prefix) {
            if(prefix == null)
                return 0;
            Node cur = root;
            int index = 0;
            for(int i = 0; i < prefix.length(); i++){
                index = prefix.charAt(i) - 'a';
                if(cur.next[index] == null)
                    return 0;
                cur = cur.next[index];
            }
            
            //开始递归求解　所有孩子的值的和
            return process(cur);
        }

        public int process(Node node){
            if(node == null)
                return 0;
            int sum = node.val;
            for(Node cur : node.next){
                sum += process(cur);
            }
            return sum;
        }
```