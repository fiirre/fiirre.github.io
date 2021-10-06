---
title: 认识line-height
---

```text
line-height: auto;
默认chrome下是16px  ff是18px

line-height 属性设置行间的距离（行高）。 不允许使用负值

行间距  =  (line-height - font-size) / 2

默认值
normal

Depends on the user agent.
Desktop browsers (including Firefox) use a default value of roughly 1.2,
depending on the element's font-family.


数字
line-height:2   等同于 line-height: 当前font-size*2

最好用数字代替固定的行间距
why it is better to use <number> values instead of <length> values


固定的行间距
line-height:32px
line-height: 2em

以em单位给出的值可能会产生意外结果
Values given in em units may produce unexpected results


百分数
line-height:80%  等同于 line-height: 当前font-size*80%

inherit
从父元素继承 line-height 属性的值
```

经典面试题

```html
<div class="p1">
  <div class="s1">1</div>
  <div class="s2">1</div>
</div>
<div class="p2">
  <div class="s5">1</div>
  <div class="s6">1</div>
</div>
<style>
  .p1 {
    font-size: 16px;
    line-height: 32px;
  }
  .s1 {
    font-size: 2em;
    /**
      字体单位为em ，相对于父元素字体大小 ，  font-size:  32px
      本身没有line-height 继承父级 line-height: 32px;
    */
  }
  .s2 {
    font-size: 2em;
    /**
      font-size:  32px
    */
    line-height: 2em;
    /**
     行高单位为em，相对于自身字体大小
      line-height: 64px (当前font-size*2 )    
    */
  }

  .p2 {
    font-size: 16px;
    line-height: 2;
    /**
      line-height: 32px  (当前font-size*2 )    
    */
  }
  .s5 {
    font-size: 2em;
    /**
      font-size: 32px  (父级ifont-size*2 )   
      line-height: 64px (继承父级 line-height: 2，当前字体大小32px ) 
    */
  }
  .s6 {
    font-size: 2em;
    line-height: 2em;
    /**
      font-size: 32px 
       line-height: 64px;
    */
  }
</style>
```

参考来源： https://developer.mozilla.org/en-US/docs/Web/CSS/line-height，https://www.w3school.com.cn/cssref/pr_dim_line-height.asp
