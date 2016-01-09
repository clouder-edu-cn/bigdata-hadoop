##安装hadoop (以2.6为例)
-------
###安装方法

####需要软件：
- Java 支持的Java版本:
[java-version](hadoop-install/images/java-support-version.png)

- SSH 

####下载软件：
[apache Download Mirrors](http://www.apache.org/dyn/closer.cgi/hadoop/common/)

####安装软件
- 解压hadoop安装包
- 创建hadoope用户
```
$ sudo addgroup cloud
$ sudo adduser --ingroup cloud hduser
```
- 在etc/hadoop/hadoop-env.sh中定义以下参数：
  ```
  # set to the root of your Java installation
  export JAVA_HOME=/usr/java/latest

  # Assuming your installation directory is /usr/local/hadoop
  export HADOOP_PREFIX=/usr/local/hadoop
  ```

- 执行hadoop命令，确认安装成功
  ```
  $bin/hadoop
  ```

#### 配置伪分布式方式：

#####配置文件：
- 配置etc/hadoop/core-site.xml:
```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
- 配置etc/hadoop/hdfs-site.xml:
```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#####配置免密码登录
```
  $ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
  $ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
```

#####执行
- 格式化文件系统：
 `$ bin/hdfs namenode -format`

- 开始hdfs文件系统
 `  $ sbin/start-dfs.sh`

- 查看运行状态
 `http://hadoop.cloud:50070/`

- 简单操作：
```
# Make the HDFS directories required to execute MapReduce jobs:
  $ bin/hdfs dfs -mkdir /user
  $ bin/hdfs dfs -mkdir /user/<username>
# Copy the input files into the distributed filesystem:
  $ bin/hdfs dfs -put etc/hadoop input
# Run some of the examples provided:
  $ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.3.jar grep input output 'dfs[a-z.]+'
# Examine the output files:
# Copy the output files from the distributed filesystem to the local filesystem and examine them:

  $ bin/hdfs dfs -get output output
  $ cat output/*

# View the output files on the distributed filesystem:

  $ bin/hdfs dfs -cat output/*
```

- 停止文件系统：
 `sbin/stop-dfs.sh`

#### 单节点支持yarn:

- 配置参数：


etc/hadoop/mapred-site.xml:
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
etc/hadoop/yarn-site.xml:
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

- 开始yarn
Start ResourceManager daemon and NodeManager daemon:
  $ sbin/start-yarn.sh
Browse the web interface for the ResourceManager; by default it is available at:

- 通过浏览器查看：
ResourceManager - http://localhost:8088/

-停止停止yarn
When you're done, stop the daemons with:
  $ sbin/stop-yarn.sh
```

