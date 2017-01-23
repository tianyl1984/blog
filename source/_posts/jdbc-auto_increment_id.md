---
layout: post
title: jdbc获取auto_increment字段的值
description: jdbc获取auto_increment字段的值
category: java
---

##问题
在mysql数据库中可以指定某一列为auto_increment，如何获取此列的值呢？

##解决方案
方法1：使用 select max(id) from tab 获取。多线程情况下可能会有问题，基本不可用。  
方法2：创建PreparedStatement时，指定Statement.RETURN_GENERATED_KEYS。代码片段：  

	PreparedStatement ps = conn.prepareStatement("insert into tab1 (age,name) values(12,'123123')", Statement.RETURN_GENERATED_KEYS);
	ps.executeUpdate();
	ResultSet rs = ps.getGeneratedKeys();
	System.out.println("----------GeneratedKeys----------");
	while (rs.next()) {
		System.out.println(rs.getInt(1));//刚刚生成的id值
	}

方法3：使用select last_insert_id()获取。一次插入多条只能获取最后一条的id。

	PreparedStatement ps = conn.prepareStatement("insert into t1 (age,name) values(12,'123123')");
	ps.executeUpdate();
	ResultSet rs = conn.prepareStatement("select last_insert_id()").executeQuery();
	while (rs.next()) {
		System.out.println(rs.getInt(1));
	}
  
方法4：使用可更新的游标，ResultSet.CONCUR_UPDATABLE.

	Statement st = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_UPDATABLE);
	ResultSet rs = st.executeQuery("select id,age,name from t1");
	rs.moveToInsertRow();
	rs.updateString("name", "李四");
	rs.updateInt("age", 22);
	rs.insertRow();
	rs.last();
	int id = rs.getInt("id");
	System.out.println(id);
