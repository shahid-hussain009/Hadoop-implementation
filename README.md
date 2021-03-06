# 1- Hadoop-implementation
## Basic Setup
`` sudo apt update ``
#### Install JDK
``sudo apt install openjdk-11-jdk``
#### Check the jdk Version
``java -version``
#### Make new user
``sudo adduser hadoopuser``
#### Switch new User
``su - hadoopuser``
#### Generating private and public key pairs
``ssh-keygen -t rsa``
#### Add these key pairs to the ssh authorized_keys
``cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys``
#### Read and Write permissions
``chmod 777 ~/.ssh/authorized_keys``
#### authenticate the localhost
``ssh localhost``
## Download the hadoop
``wget https://downloads.apache.org/hadoop/common/hadoop-3.3.3/hadoop-3.3.3.tar.gz``
or
download it from the link directly

[Hadoop Link](https://downloads.apache.org/hadoop/common/hadoop-3.3.3/hadoop-3.3.3.tar.gz)
#### Extract the downloaded hadoop (Hadoop may be different version in you case)

if you downloaded the hadoop from the link then move it to hadoopuser as we have already created hadoop user then extract

``tar -xvzf hadoop-3.3.3.tar.gz``

#### Move hadoop
``mv hadoop-3.3.0 hadoop``

### Configure Java environment variable
``dirname $(dirname $(readlink -f $(which java)))``
### Open the “~/.bashrc” file in your “nano” text editor
`` nano ~/.bashrc``

and add following code 

```html export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

export HADOOP_HOME=/home/hadoopuser/hadoop

export HADOOP_INSTALL=$HADOOP_HOME

export HADOOP_MAPRED_HOME=$HADOOP_HOME

export HADOOP_COMMON_HOME=$HADOOP_HOME

export HADOOP_HDFS_HOME=$HADOOP_HOME

export HADOOP_YARN_HOME=$HADOOP_HOME

export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native

export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```

Then  press ``CTRL+O`` + ``CTRL+M`` + ``CTRL+X`` to save the changes

### Activate the “JAVA_HOME”
``source ~/.bashrc``
### Open up the environment variable file of Hadoop in nono text editor
``nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh``

then add

``export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64``

Then  press ``CTRL+O`` + ``CTRL+M`` + ``CTRL+X`` to save the changes
### Create two directories: datanode and namenode, inside the home directory of Hadoop
``mkdir -p ~/hadoopdata/hdfs/namenode``

``mkdir -p ~/hadoopdata/hdfs/datanode``
### Check you hostname
``hostname``
### Now, open up the ``core-site.xml`` file in your “nano” editor:
``nano $HADOOP_HOME/etc/hadoop/core-site.xml``

then add

```html
<property>
     <name>fs.defaultFS</name>
     
     <value>hdfs://hadoop.linuxhint-VBox.com:9000</value>
 </property>
```

Replace ``linuxhint-VBox`` with your hostname

Then  press ``CTRL+O`` + ``CTRL+M`` + ``CTRL+X`` to save the changes
### Change the directory path of “datanode” and “namenode” in hdfs-site.xml

``nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml``

then add 

```html
<property>
     <name>dfs.replication</name>
     
     <value>1</value>
</property>
 

<property>
      <name>dfs.name.dir</name>
       
       <value>file:///home/hadoopuser/hadoopdata/hdfs/namenode</value>
</property>
 

<property>
      <name>dfs.data.dir</name>
      
      <value>file:///home/hadoopuser/hadoopdata/hdfs/datanode</value>
</property>
```
   ### Open up the ``mapred-site.xml``
   ``nano $HADOOP_HOME/etc/hadoop/mapred-site.xml``
   
   then add
   
   ```html
  <property>
   <name>mapreduce.framework.name</name>
   <value>yarn</value>
</property>

<property>
   <name>yarn.app.mapreduce.am.env</name>
   <value>HADOOP_MAPRED_HOME=/home/hadoopuser/hadoop</value>
</property>

<property>
   <name>mapreduce.map.env</name>
   <value>HADOOP_MAPRED_HOME=/home/hadoopuser/hadoop</value>
</property>

<property>
   <name>mapreduce.reduce.env</name>
   <value>HADOOP_MAPRED_HOME=/home/hadoopuser/hadoop</value>
</property>
   ```
  ### Open yarn-site.xml
   ``nano $HADOOP_HOME/etc/hadoop/yarn-site.xml``
   
   then add
   
```html
<property>
  <name>yarn.nodemanager.aux-services</name>
  
  <value>mapreduce_shuffle</value>
</property>
```
   
   then formet the node 
   
   ``hdfs namenode -format``
   
   Then start 
   
   ``start-dfs.sh``
   
   If you get the “Could resolve hostname error” then do below setup
   ``sudo nano /etc/hosts`` and replace the hostname with yours hostname
   
   then again start
   
   ``start-dfs.sh`` and ``start-yarn.sh`` then check the status by typing ``jps``
   
   ### Hadoop listens at the port 8088 and 9870, so you are required to permit these ports through the firewall
   Now switch to your main user then do the following setup
   
   ``firewall-cmd --permanent --add-port=9870/tcp``
   
   ``firewall-cmd --permanent --add-port=8088/tcp``
   
   ``firewall-cmd --reload``
   
   Hadoop installation ha done and open the browser and type ``localhost:9870`` and ``localhost:8088``
   
   for stoping hadoop type ``stop-dfs.sh`` and ``stop-yarn.sh``
   
# 2- Steps to run WordCount Program on Hadoop
create a folder name Lab ``or whatever you want`` on Desktop and inside Lab create two more folder ``Input`` and ``tutorial_classes``

create a file ``WordCount.java`` inside Lab folder

then create ``input.txt`` inside Lab

```js cd Desktop
mkdir Lab
mkdir Lab/Input
mkdir Lab/tutorial_classes
```
### Type the following command to export the hadoop classpath into bash
``export HADOOP_CLASSPATH=$(hadoop classpath)``

and make sure it is exported

``echo $HADOOP_CLASSPATH``

### Create these directories on HDFS 
```js
hadoop fs -mkdir /WordCountTutorial 
hadoop fs -mkdir /WordCountTutorial/Input
hadoop fs -put Lab/Input/input.txt /WordCountTutorial/Input
```
### Go to localhost:9870 from the browser, Open “Utilities → Browse File System”
### Back to local machine where we will compile the WordCount.java file
### Make sure we are in Desktop directory Lab
``javac -classpath $HADOOP_CLASSPATH -d tutorial_classes WordCount.java``

### Put the output files in one jar file (do not forget to copy dot as well)

``jar -cvf WordCount.jar -C tutorial_classes . ``

### Run the jar file on Hadoop

`` hadoop jar WordCount.jar WordCount /WordCountTutorial/Input /WordCountTutorial/Output ``

### Output the result

`` hdfs dfs -cat /WordCountTutorial/Output/* ``


## References
[Hadoop Installation setup](https://linuxhint.com/install-apache-hadoop-ubuntu/)


[Hadoop Installation with word count example](https://docs.google.com/document/d/1-BKY9iBpkm2dSbO7OKc33JBa4CZymOCiwl1EWaFqeBQ/edit)


