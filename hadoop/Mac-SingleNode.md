是的，可以在 MacBook 上設置 Hadoop 單節點（Single Node）環境來進行測試。這樣的設置適合用於學習和測試 Hadoop 的基本操作。在 Mac 上運行 Hadoop 需要完成以下步驟。

### 1. 安裝 Homebrew

如果尚未安裝 Homebrew，可以通過以下命令安裝：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. 安裝 Java

Hadoop 需要 Java 環境。通過 Homebrew 安裝 OpenJDK 8 或 OpenJDK 11：

```bash
brew install openjdk@8
```

安裝後設置 JAVA_HOME（假設使用 JDK 8）：

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)
```

將此行添加到 `~/.bash_profile` 或 `~/.zshrc` 文件中，然後執行 `source ~/.bash_profile` 或 `source ~/.zshrc` 使其生效。

### 3. 下載並安裝 Hadoop

從 [Apache Hadoop 官方網站](https://hadoop.apache.org/releases.html) 下載 Hadoop 二進制文件。下載完成後解壓文件並移動到指定目錄（例如 `/usr/local/hadoop`）：

```bash
# 下載 Hadoop
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz

# 解壓並移動到目錄
tar -xzf hadoop-3.3.1.tar.gz
sudo mv hadoop-3.3.1 /usr/local/hadoop
```

### 4. 配置 Hadoop 環境變量

編輯 `.bash_profile` 或 `.zshrc` 文件，添加 Hadoop 環境變量：

```bash
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)  # 根據使用的 JDK 版本設定
```

執行以下命令使配置生效：

```bash
source ~/.bash_profile
```

### 5. 配置 Hadoop 文件

進入 Hadoop 的配置目錄 `/usr/local/hadoop/etc/hadoop`，配置以下文件：

#### 編輯 `core-site.xml`

設置默認文件系統為 HDFS：

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

#### 編輯 `hdfs-site.xml`

指定存儲路徑並設置複製因子為 1（單節點模式）：

```xml
<configuration>
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
</configuration>
```

#### 編輯 `mapred-site.xml`

將 `mapred-site.xml.template` 複製並重命名為 `mapred-site.xml`，然後配置 MapReduce 使用 YARN：

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

#### 編輯 `yarn-site.xml`

啟用 YARN 的資源管理功能：

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

#### 配置 `hadoop-env.sh`

設置 Java 路徑：

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)  # 使用的 JDK 版本
```

### 6. 格式化 NameNode

在初次啟動 HDFS 之前，需要格式化 NameNode：

```bash
hdfs namenode -format
```

### 7. 啟動 Hadoop

依次啟動 HDFS 和 YARN 服務：

```bash
start-dfs.sh
start-yarn.sh
```

### 8. 驗證安裝

可以通過瀏覽器打開以下端口檢查 Hadoop 是否運行正常：

- **HDFS NameNode Web UI**：[http://localhost:9870](http://localhost:9870)
- **YARN ResourceManager Web UI**：[http://localhost:8088](http://localhost:8088)

### 9. 測試 HDFS

可以使用一些基本命令來驗證 HDFS 是否正常運行：

```bash
# 創建目錄
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/your_username

# 上傳文件
echo "Hello Hadoop" > hello.txt
hdfs dfs -put hello.txt /user/your_username/

# 查看文件
hdfs dfs -ls /user/your_username
```

### 10. 停止 Hadoop 服務

當測試結束後，可以停止 Hadoop 服務：

```bash
stop-dfs.sh
stop-yarn.sh
```

這樣，您就可以在 MacBook 上成功啟動 Hadoop 的單節點環境並進行測試。