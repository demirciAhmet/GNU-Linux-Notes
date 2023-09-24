
Just install the latest jdk from oracle (for debian based distros install .deb): https://www.oracle.com/java/technologies/downloads/ then install it with installer or: `sudo dpkg -i jdk-20_linux-x64_bin.deb`

(Optional) To change the jdk to newer version (e.g. jdk21), change the alternatives:
```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk-21-oracle-x64/bin/java 1
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk-21-oracle-x64/bin/javac 1
```
(Optional) If you are not encountering any issues with your current setup, there may be no need to add JAVA_HOME to the /etc/environment file. You can continue to use Java as you have been without any problems. However, if you plan to use specific Java development tools or applications that require the JAVA_HOME variable to be set, you can add it with `sudo nano /etc/environment` and then add the line to the end: `JAVA_HOME="/usr/lib/jvm/jdk-21-oracle-x64"` and `source /usr/environment`. 
