---
layout:     post
title:      java做算法题总结（持续更新）
subtitle:   java技巧
date:       2019-07-25
author:     showface
header-img: 
catalog: true
tags:
    - java
---
>手势在用户交互中有着举足轻重的作用，这篇文字简单的介绍了iOS中的手势，并通过手势对控件进行变形处理。


#各种输入
输入有多组数据，没有说明输入几组数据，也没有截止符，每组数据占一行：
Scanner s=new Scanner(System.in);
while(s.hasnext()){//判断是否数据结束
int a=s.nextInt();
}

通吃方法：

3
1 4 6 8
4 5 7
8 3


```java

import java.util.Scanner;
public static void main(String[] args) {
Scanner in = new Scanner(System.in);
List<Integer> list=new ArrayList<Integer>();
while (in.hasNext()) {
String s=in.nextLine();

String[] str=s.split(" ");  
int[] a=new int[str.length];
for(int i=0;i<str.length;i++){
a[i]=Integer.parseInt(str[i]);
...
}
}

```

直接把所有输入变成可以直接指定位置的
存在list里


排序

Arrays.sort(int[] a)

这种形式是对一个数组的所有元素进行排序，并且是按从小到大的顺序。
import java.util.Arrays;

public class Main {
public static void main(String[] args) {
   
int[] a = {9, 8, 7, 2, 3, 4, 1, 0, 6, 5};
Arrays.sort(a);
for(int i = 0; i < a.length; i ++) {
System.out.print(a[i] + " ");
}
}

}


Arrays.sort(int[] a, int fromIndex, int toIndex)

这种形式是对数组部分排序，也就是对数组a的下标从fromIndex到toIndex-1的元素排序，注意：下标为toIndex的元素不参与排序哦！