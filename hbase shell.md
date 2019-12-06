# 一,基本操作
1. 进入HBase客户端命令行
	```bash
	[root@node1 hbase]$ bin/hbase shell
	```

2. 查看帮助命令

   ~~~shell
   hbase(main):001:0> help
   ~~~

3. 查看当前数据库中有哪些表

   ~~~shell
   hbase(main):002:0> list
   ~~~

# 二,表的操作

1. 创建表

   ~~~shell
   hbase(main):002:0> create 'student','info'
   ~~~

