---
layout: post
title: JDBC ResultSet.getXxx如何区分返回null还是0
description: JDBC ResultSet.getXxx如何区分返回null还是0
category: java
---

### 问题
在java开发中，如果用到数据库，不可避免要使用jdbc，在ResultSet类的getInt，getLong，getFloat，getDouble方法中如果sql结果返回值是null，这些方法将会返回0，在实际业务中是需要区分null和0的，如何区分呢？

### 解决方案
> 方法1.使用getObject方法，如果为null，将会返回null，判断如果不是null，再转换为指定类型。  

> 方法2.在调用getInt，getLong，getFloat，getDouble方法之后，调用wasNull方法，判断刚才获取的是否为null，必须在调用getXxx之后，判断也是针对上一次调用getXxx方法。
