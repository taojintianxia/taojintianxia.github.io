---
layout: post  
title: BigDecimal的equals  
categories: [Java, BigDecimal]  
description:  
keywords: Java, BigDecimal  
---

```
BigDecimal testA = new BigDecimal(79);
BigDecimal testB = new BigDecimal("79.00");
 
System.out.println(testA);
System.out.println(testB);
System.out.println(testA.equals(testB));
```

#### 执行上面的代码 , 结果是false...

#### 如果想比较BigDecimal , 请使用compareTo.

#### BigDecimal比较的时候会比较scale , 如果scale不同就立刻返回false

