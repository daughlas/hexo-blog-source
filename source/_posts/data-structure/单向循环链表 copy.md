---
title: 单向循环链表
date: 
categories:
- 数据结构
tags:
- 数据结构
- Python
---

```python
class Node(object):
    """节点"""

    def __init__(self, ele):
        self.ele = ele
        self.next = None


class SingleCycleLinkList(object):
    """单向循环链表"""

    def __init__(self, node=None):
        self.__head = None
        if node:
            node.next = node

    def is_empty(self):
        """链表是否为空"""
        return self.__head == None

    def length(self):
        """链表的长度"""
        if self.is_empty():
            return 0
        cur = self.__head
        count = 1
        while cur.next != self.__head:
            count += 1
            cur = cur.next
        return count

    def travel(self):
        """遍历"""
        if self.is_empty():
            return False
        cur = self.__head
        while cur.next != self.__head:
            print(cur.ele, end=" ")
            cur = cur.next
        # 退出循环，cur 指向尾节点，但是尾结点的元素未打印
        print(cur.ele, end=" ")
        print("")

    def add(self, item):
        """头部添加"""
        node = Node(item)
        if self.is_empty():
            self.__head = node
            node.next = node
        else:
            cur = self.__head
            while cur.next != self.__head:
                cur = cur.next
            cur.next = node
            node.next = self.__head
            self.__head = node

    def append(self, item):
        """尾部添加"""
        node = Node(item)
        if self.is_empty():
            self.__head = node
            node.next = node
            return
        else:
            cur = self.__head
            while cur.next != self.__head:
                cur = cur.next
            # 先不动原有的列表
            node.next = self.__head
            cur.next = node    

    def insert(self, pos, item):
        """插入"""
        if pos <= 0:
            self.add(item)
        elif pos > (self.length() - 1):
            self.append(item)
        else:
            index = 0
            pre = self.__head
            node = Node(item)
            while index < (pos - 1):
                pre = pre.next
                index += 1
            node.next = pre.next
            pre.next = node

    def remove(self, item):
        """删除元素"""
        if self.is_empty():
            return False
        cur = self.__head
        pre = None
        while cur.next != self.__head:
            if cur.ele == item:
                # 头结点就是要删除的元素
                if self.__head == cur:
                    # 头结点就是要找的
                    rear = self.__head
                    while rear.next != self.__head:
                        rear = rear.next
                    self.__head = cur.next
                    rear.next = self.__head
                elif cur.next == self.__head:
                    pre.next = self.__head
                else:
                    pre.next = cur.next
                return True
            else:
                pre = cur
                cur = cur.next
        # 退出循环， cur 指向尾结点
        if cur.ele == item:
            if cur == self.__head:
                # 链表只有一个节点
                self.__head = None
            else:
                pre.next = self.__head
            return True
        # 缺少最后一项
        return False

    def search(self, item):
        """查看元素是否存在"""
        cur = self.__head
        if self.is_empty():
            return False
        while cur.next != self.__head:
            if cur.ele == item:
                return True
                break
            else:
                cur = cur.next
        if cur.ele == item:
            return True
        return False


if __name__ == "__main__":
    ll = SingleCycleLinkList()
    ll.append(0)
    ll.append(1)
    ll.append(2)
    ll.append(3)
    ll.append(4)
    ll.append(5)
    ll.append(6)
    ll.insert(1, "11111")
    ll.insert(-1, "------")
    ll.insert(13, "00000")
    ll.travel()
    print(ll.search(9))
    ll.remove("11111")
    ll.travel()
    ll.remove("------")
    ll.travel()
    ll.remove("00000")
    ll.travel()

```





