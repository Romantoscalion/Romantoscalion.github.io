---
title: C# Enumerable Aggregate
date: 2025-1-22 16:17:29
categories:
- Unity
tags:
- 短篇
- C#
toc: true
---

之前都没注意到有这么好用的东西，这个东西的大体思路是对于一个Enumerable的对象，以列表为例，传一个任意类型的初始的随意什么东西进去，然后遍历列表中的每一对象，对这个初始的东西做累计的任意操作，遍历完后还可以进行一次转换，返回遍历完的这个初始对象。

这么做可以减少一些for循环，增不增加可读性不知道，但是代码看着会简洁不少。

下面列一些微软给的示例代码：

```c#
string[] fruits = { "apple", "mango", "orange", "passionfruit", "grape" };

string longestName =
    fruits.Aggregate("banana", //  输入一个初始的字符串类型的对象
                    (longest, next) => // 遍历用的匿名函数，这里前面的参数是遍历中流传的对象，后面的参数是列表中的对象
                        next.Length > longest.Length ? next : longest,
                    fruit => fruit.ToUpper()); // 指定输出时的转换函数，把上面流传完的longest对象作为这个函数的输入fruit

Console.WriteLine(
    "The fruit with the longest name is {0}.",
    longestName);
// The fruit with the longest name is PASSIONFRUIT.
```

有两个Aggregate的参数少一些的重载，比如下面这个是省去了输出时的转换方法：

```c#
int[] ints = { 4, 8, 8, 3, 9, 0, 7, 8, 2 };


int numEven = ints.Aggregate(0,  // 传入初始值Int型对象，0
                             (total, next) => // 遍历列表对象，列表对象为偶数则给初始对象的值加1
                                    next % 2 == 0 ? total + 1 : total);

Console.WriteLine("The number of even integers is: {0}", numEven);
// The number of even integers is: 6
```

初始值也可以省掉，不过需要注意，如果省掉初始值，会以列表中的第0项作为初始值开始遍历，而且遍历会跳过第0项。

```c#
string sentence = "the quick brown fox jumps over the lazy dog";
string[] words = sentence.Split(' ');

string reversed = words.Aggregate(
    (workingSentence, next) => // 指定遍历函数，在输入初始对象的前面遍历地加上列表对象
  		next + " " + workingSentence);

Console.WriteLine(reversed);
// dog lazy the over jumps fox brown quick the
```

