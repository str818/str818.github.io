---
layout: article
title: 左神 — 链表
tags: 左神算法

lang: zh-Hans
key: Zuo_Linked_List
pageview: true
toc: true
show_subscribe: false
---

## 1. 打印两个有序链表的公共部分

#### 题目

给定两个有序链表的头指针 head1 和 head2，打印两个链表的公共部分。

#### 思路

双指针，head1 小则 head1 往下移，head2 小则 head2 往下移，相等则打印。

```java
public void printCommonPart(Node head1, Node head2) {
    while (head1 != null && head2 != null) {
        if (head1.value < head2.value) {
            head1 = head1.next;
        } else if (head1.value > head2.value) {
            head2 = head2.next;
        } else {
            System.out.println(head.value);
            head1 = head1.next;
            head2 = head2.next;
        }
    }
}
```