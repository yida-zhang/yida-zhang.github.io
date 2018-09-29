---
title: MyBatis分页插件PageHelper的使用
layout: post
categories: PageHelper
tags: MyBatis 
exccerpt: MyBatis分页插件PageHelper的使用
---
![使用原理]({{site.baseurl}}/assets/res/clipboard.png)
pageHelper会使用ThreadLocal获取到同一线程中的变量信息，各个线程之间的Threadlocal不会相互干扰，也就是Thread1中的ThreadLocal1之后获取到Tread1中的变量的信息，不会获取到Thread2中的信息
所以在多线程环境下，各个Threadlocal之间相互隔离，可以实现，不同thread使用不同的数据源或不同的Thread中执行不同的SQL语句
所以，PageHelper利用这一点通过拦截器获取到同一线程中的预编译好的SQL语句之后将SQL语句包装成具有分页功能的SQL语句，并将其再次赋值给下一步操作，所以实际执行的SQL语句就是有了分页功能的SQL语句 

1、添加maven依赖 ： 
<properties>
	<pagehelper.version>5.0.1</pagehelper.version>
</properties>
<dependency>
         <groupId>com.github.pagehelper</groupId>
         <artifactId>pagehelper</artifactId>
         <version>${pagehelper.version}</version>
</dependency>

2、mybatis-config配置分页插件
<!-- 配置分页插件 -->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
 <!-- 设置数据库类型 Oracle,Mysql,MariaDB,SQLite,Hsqldb,PostgreSQL六种数据库-->        
        </plugin>
    </plugins>

3、代码层次开启分页及分页查询
PageHelper.startPage(pageNum, pageSize, true);
//调用mapper.xml方法时不需要 传入 pageNum，pageSize参数
但是返回的参数为mybatis的分页对象 如：
Page<LifeContentVo> page=this.lifeDao.getLifeContentList(categoryId);

再通过 com.github.pagehelper.PageInfo 类可以获取到分页的一系列字段 如总页数，数据集合
PageInfo<LifeContentVo> p=new PageInfo<LifeContentVo>(page);
通过PageInfo  getList(); 方法获取数据集合
