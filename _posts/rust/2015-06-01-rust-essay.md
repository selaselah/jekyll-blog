---
layout: post
title: Rust 随笔
category: Rust
tags: Rust
---

这里记录了在学习 rust 时遇到的一些问题

## 你犯过 if (a = 0) 的错误么

{% highlight c %}
if (p = NULL) {
    printf("resourses init failed");
    return 0;
}
{% endhighlight %}

判断 p 指针是否为 NULL, 如果是的话，则报错。

然而这段代码是错误的。
因为 `==` 误写作了 `=`，无论 p 值为多少，都会被赋值为 NULL，不仅 p 被错误赋值，
if 判断也永远为 false。

这是 c/c++ 最常见的错误之一。
很多教程都有提过这个梗，并告诉我们一定要细心细心，不要犯这样的错误。

一个可以规避 const/mutable，具有 public/private 的强类型语言，
却不能处理这种错误，不觉得很奇怪么？

王垠[提到过](http://www.yinwang.org/blog-cn/2013/03/15/language-design-mistake1/)
，这个问题的实质是混淆了赋值语句和表达式值的界限：赋值语句本身不应该返回任何值
的。

而在 rust 中，表达式不再等同语句，而声明定义变量强制限制为语句形式，赋值表达式返
值为`()`，从而彻底规避了这种问题。请看代码示例：

{% highlight rust %}
fn main() {
    let x = 0;
    if x = 0 {
        println!("x is eqaul with 0");
    }
}
{% endhighlight %}

报错为:

{% highlight text %}
assign.rs:3:8: 3:13 error: mismatched types:
 expected `bool`,
    found `()`
(expected bool,
    found ()) [E0308]
assign.rs:3     if x = 0 {
                   ^~~~~
error: aborting due to previous error
{% endhighlight %}
