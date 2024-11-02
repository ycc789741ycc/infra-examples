在單節點上安裝 Hadoop 是學習 Hadoop 架構的好方式。以下是單節點安裝 Hadoop 的步驟。

### 前提條件
- **作業系統**：Linux（例如 Ubuntu）
- **Java**：Hadoop 需要 Java 環境

### 安裝步驟

#### 1. 安裝 Java
Hadoop 需要 Java 環境，首先檢查是否已安裝 Java，並安裝 OpenJDK：

```bash
java -version
```

如果未安裝，執行以下指令：

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

設定 `JAVA_HOME` 環境變數：

```bash
echo "export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:/bin/java::")" >> ~/.bashrc
source ~/.bashrc
```

#### 2. 下載並解壓 Hadoop

前往 [Hadoop 官方網站](https://hadoop.apache.org/releases.html) 下載最新版本或使用以下指令（假設是 Hadoop 3.3.6）：

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
sudo tar -xvzf hadoop-3.3.6.tar.gz -C /usr/local/
sudo mv /usr/local/hadoop-3.3.6 /usr/local/hadoop
```

#### 3. 設定 Hadoop 環境變數

編輯 `~/.bashrc` 文件，並添加以下內容：

```bash
# Hadoop environment variables
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
```

載入環境變數：

```bash
source ~/.bashrc
```

#### 4. 配置 Hadoop

編輯 Hadoop 的配置文件，進行基本配置。

- **core-site.xml**：設定 Hadoop 文件系統的位置。
  
  打開 `core-site.xml` 文件：

  ```bash
  nano $HADOOP_HOME/etc/hadoop/core-site.xml
  ```

  在 `<configuration>` 標籤內添加：

  ```xml
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
  ```

- **hdfs-site.xml**：設定 HDFS（Hadoop 分布式檔案系統）配置。

  打開 `hdfs-site.xml` 文件：

  ```bash
  nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
  ```

  添加以下內容以設定 Namenode 和 Datanode 的資料目錄：

  ```xml
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///usr/local/hadoop/hadoop_data/hdfs/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///usr/local/hadoop/hadoop_data/hdfs/datanode</value>
  </property>
  ```

- **mapred-site.xml**：設定 MapReduce 配置。

  先複製範本文件：

  ```bash
  cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
  nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
  ```

  添加以下內容：

  ```xml
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  ```

- **yarn-site.xml**：設定 YARN 配置。

  打開 `yarn-site.xml` 文件：

  ```bash
  nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
  ```

  添加以下內容：

  ```xml
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  ```

#### 5. 格式化 HDFS

在安裝 Hadoop 的第一次啟動之前，需要格式化 HDFS 文件系統：

```bash
hdfs namenode -format
```

#### 6. 啟動 Hadoop

在單節點模式下，依次啟動各個服務：

```bash
# 啟動 HDFS
start-dfs.sh

# 啟動 YARN
start-yarn.sh
```

#### 7. 驗證安裝

檢查 Hadoop 的運行狀態：

- 可以使用 `jps` 命令檢查服務是否正常運行：

  ```bash
  jps
  ```

  預期應看到以下服務：`NameNode`、`DataNode`、`ResourceManager`、`NodeManager` 等。

- 驗證 Web 界面：
  - **HDFS**：`http://localhost:9870`
  - **YARN ResourceManager**：`http://localhost:8088`

這樣就完成了單節點 Hadoop 的基本安裝與配置！