---
title: C# 泛型方法
date: 2025-1-2 18:01:52
categories:
- Unity
tags:
- 短篇
- C#
toc: true
---



Unity里有很多内置的泛型方法，比如`GetComponent<>`、`GetObjectOfType<>`等。

有时候我们会希望定义自己的泛型方法，在我的情况中，我希望编写一个方法用于检查一个委托中是否已经订阅了某个方法。

如果不使用泛型方法，需要为每一种Action都重载一个方法的实现，非常麻烦。

如果像下面这样使用泛型方法的话，会非常方便：

```c#
static void Main(string[] args)
{   
    Action<string> action = (string str) => {};
    var del = new Action<string>((string str) => {});
    Console.WriteLine(ActionHasDelegate(action, del)); // Output: False
    action += del;
    Console.WriteLine(ActionHasDelegate(action, del)); // Output: True

    Action<int, string> action1 = (int i, string str) => {};
    var del1 = new Action<int, string>((int i, string str) => {});
    Console.WriteLine(ActionHasDelegate(action1, del1)); // Output: False
    action1 += del1;
    Console.WriteLine(ActionHasDelegate(action1, del1)); // Output: True
}

static bool ActionHasDelegate<T>(T action, Delegate del) where T : Delegate
{
    foreach (var d in action.GetInvocationList())
    {
        if (d == del)
        {
            return true;
        }
    }
    return false;
}
```

可以观察到泛型方法ActionHasDelegate对于不同类型的Action都能够顺利判断，可喜可贺，可喜可贺。

