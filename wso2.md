download the last version of wso2am and wso2mi and upload it on your session

1️⃣ Install OpenJDK 11

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

2️⃣ Selecting Java 11 as the default Java :

```bash
sudo update-alternatives --config java
```

3️⃣ Setting JAVA_HOME for the current terminal :

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

Review:
```
echo $JAVA_HOME
java -version
```
java -version should show version 11.


4️⃣ Implementing WSO2AM :

```bash
bin/api-manager.sh
```

💡 Tip: If you want JAVA_HOME to always be on Java 11, you can add these two export lines to the end of the ~/.bashrc file and then restart the terminal:

```bash
echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64" >> ~/.bashrc
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> ~/.bashrc
source ~/.bashrc
```
