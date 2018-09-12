---
layout: post
title: "Remove Linked List Elements"
date: 2018-09-10 11:15:06 
description: "链表"
tag: 链表

---


##LeetCode203. Remove Linked List Elements(删除链表中的元素(多个))(三种方法)

 - 不设置dummyHead
 - 设置dummyHead
 - 使用递归

***
###不设置dummyHead
删除一个结点的操作: 

 - 先找到对应结点node的pre结点，然后令pre.next = pre.next.next就让node结点脱离整个链即可；
 - <font color = red>注意要先判断一下，链表一开始相等的部分，要把前面所有相等的部分都先删除；<font>

```
	 // no dummyhead
    public ListNode removeElements(ListNode head,int val){
        if(head == null)return null;
        while(head != null && head.val == val) head = head.next; //删除一开始的
        if(head == null )return null;
        ListNode pre = head;
        while(pre.next != null){
            if(pre.next.val == val){
                pre.next = pre.next.next;
            }else {
                pre = pre.next;//注意这里必须写在else中，因为pre.next删除之后，还是要检查pre.next
            }
        }
        return head;
    }

```
***
###设置dummyHead
**这个就是设置一个虚拟的头结点，让我们不再需要特判链表的第一个元素。**
```
	// dummyhead
    public ListNode removeElements2(ListNode head,int val){
        if(head == null)return null;
        ListNode dummyHead = new ListNode(-1);
        dummyHead.next = head;
        ListNode pre = dummyHead;
        while(pre.next != null){
            if(pre.next.val == val){
                pre.next = pre.next.next;
            }else {
                pre = pre.next;
            }
        }
        return dummyHead.next;
    }
```
***
###递归
宏观的来看: 

 - **就是先去处理我的next，我的next链开始的链先给我删除那些val的，然后我再连接上；**
 - **我连接的时候，要看看自己要不要被删除，如果要，就直接返回我的next就可以啦，如果不要就先连接上，然后返回；**
 - 注意边界条件处理；

微观的来看: 
![这里写图片描述](https://img-blog.csdn.net/20180831103953660?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
    //recursive
    public ListNode removeElements(ListNode head,int val){
        if(head == null)return null;

        // ListNode res = removeElements(head.next,val);
        // if(head.val == val){
        //     return res;
        // }else {
        //     head.next = res;
        //     return head;
        // }
        
       head.next = removeElements(head.next,val);//我的next 等于 你后面的先处理完val我再连上你
       return head.val == val ? head.next : head; //我自己的话 就要判断一下
    }
```