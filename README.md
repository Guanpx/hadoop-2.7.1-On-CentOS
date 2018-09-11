### 使用虚拟机搭建hadoop分布式（CentOS + hadoop2.7.1）

1.配置文件均在 hadoop-2.7.x/etc/hadoop下

2.路径需要根据实质情况配置，ssh免密登录以及防火墙关闭默认准备完成

3.准备三台虚拟机，配置好一台后，直接复制安装目录即可

#### 设置环境变量

```shell
vi /etc/profile
# 末尾添加
export HADOOP_HOME=/home/hadoop-2.7.1
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

# source
source /etc/profile
```

#### 设置hadoop-env.sh配置文件

```shell
vi   hadoop-env.sh  #添加如下内容
....
# remote nodes.

# java 添加java_home路径
export JAVA_HOME=/app/java/jdk1.8.0_60/

# The java implementation to use.
export JAVA_HOME=${JAVA_HOME}
....
```

#### 设置core-site.xml配置文件

```shell
vi core-site.xml   #添加如下内容
# fs.defaultFS : value就是 namenode url
# hadoop.tmp.dir ： datanode临时存放的数据文件,需要自己新建
# mkdir xxx/hadoop-2.7.1/data  

<configuration>
      <property>
           <name>fs.defaultFS</name>
           <value>hdfs://hdp01:9000</value>
      </property>
      <property>
           <name>hadoop.tmp.dir</name>
           <value>/home/hadoop/hadoop-2.7.1/data</value>
           <description>Abase for other temporary directories.</description>
      </property>
</configuration>
```

#### 设置hdfs-site.xml配置文件

```shell
vi hdfs-site.xml  #添加如下内容
# dfs.replication ：数据复制数量

<configuration>
<property>
		<name>dfs.replication</name>
        <value>2</value>
</property>
</configuration>
```

#### 设置mapred-site.xml配置文件

```shell
mv  mapred-site.xml.template   mapred-site.xml  
#默认mapred-site.xml不存在，使用mapred-site.xml.template生成
vi  mapred-site.xml  #添加如下内容
# mapreduce.framework.name ：执行框架设置为 Hadoop YARN.

<configuration>
<property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
</property>
</configuration>
```

#### 设置yarn-site.xml配置文件

```shell
vi   yarn-site.xml   #添加如下内容
# yarn.resourcemanager.hostname : rm服务器的主机名
# yarn.nodemanager.aux-services ：Shuffle service 需要加以设置的Map Reduce的应用程序服务
<configuration>
<!-- Site specific YARN configuration properties -->
       <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>hdp01</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

#### 设置slaves配置文件

```shell
vi  slaves    #修改后显示如下内容
hdp02
hdp03
# 可以添加自己 hdp01
```

#### 两个常见小错误

1.datanode启动不了

hadoop datanode -format

2.datanode启动成功之后又启动失败

清空datanode的tmp文件夹里的内容
