# K个一组翻转链表

题目来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/reverse-nodes-in-k-group

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

**示例 :**

给定这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5

**说明 :**

你的算法只能使用常数的额外空间。
**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**题解**
> 1.链表需要翻转的每k个长度的子链表看做是一个整体，就是一个[反转链表](反转链表.md)的问题。
> 2.剩下需要关心的就是将翻转后的子链表 连接到总链表上，所以需要找出 需要反转的子链表的 前面的节点，后面的节点，和需要反转的链表的开始的节点和结束的节点。
> 3. 反转动作结束后 ，将反转链表的前面的节点连接到反转后的链表的开始位置，将反转后的结束位置节点 连接 到本组需要反转链表后面的节点，这样本次反转就完成了。依次类推直至需要反转的链表的长度小于k，完成了k个一组反转链表的操作了
>  时间复杂度为 **O(n)**, 空间复杂度为 **O(1)**

__代码如下:__
```python
# -*- coding: utf-8 -*-
# @Author   : xaohuihui
# @Time     : 19-12-13
# @File     : reverse_nodes_k_group.py
# Software  : study

"""
K 个一组翻转链表
"""


class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None


def reverse_list(head: ListNode) -> ListNode:
    new_node = None
    while head:
        p = head.next          # 暂存下一个节点
        head.next = new_node   # head指向上一个节点 上一个节点是从None开始
        new_node = head        # new_node 向后移动到当前head位置
        head = p               # head 向后移动一个节点
    if not new_node:
        return head
    else:
        return new_node


def reverse_k_group(head: ListNode, k: int) -> ListNode:
    if head and head.next:
        pre = ListNode(0)
        pre.next = head                         # 申请一个新的节点，记录最开始的位置，为了最后返回翻转后的第一个节点
        tmp = pre
        while tmp and tmp.next:
            i = 1
            start = end = tmp.next              # 申请开始和结束指针，初始化本组需要翻转链表的头节点位置
            while i < k and end.next:           # 本循环的作用是将end指针指向 本组需要翻转链表的最后一个位置
                end = end.next
                i += 1
            if i == k:                          # 若本组需要翻转链表的长度符合k，就进行下去
                last = end.next                 # last 记录本组需要翻转链表的后面的头节点位置
                end.next = None                 # 将本组需要翻转你链表的最后一个节点的next置为None，方便翻转
                tmp.next = reverse_list(start)  # 返回交换后链表的头节点，使 本组需要翻转链表 的前面链表的最后节点的next指向翻转后的头节点
                start.next = last               # 将翻转后的最后一个节点的next指针指向 本组翻转链表后面的节点 ，  这样翻转后的链表就和原来的链表连接上了

                tmp = start                     # 将tmp指针指向下一组需要翻转链表 的前面的节点
            else:                               # 若本组链表长度不符合k，即不进行交换，说明已经全部交换完成，返回交换后头节点
                return pre.next
        return pre.next
    else:
        return head


if __name__ == '__main__':
    node1 = ListNode(1)
    node2 = ListNode(2)
    node3 = ListNode(3)
    node4 = ListNode(4)
    node5 = ListNode(5)

    node1.next = node2
    node2.next = node3
    node3.next = node4
    node4.next = node5

    k = 2

    res = reverse_k_group(node1, k)
    while res:
        print(res.val)
        res = res.next
```
__输出结果：__
```bash
2
1
4
3
5
```
