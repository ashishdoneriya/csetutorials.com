---
title: My Java Notes
created: 2022-10-02T14:00:00+05:30
updated: 2022-10-02T14:00:00+05:30
author: ashishdoneriya
permalink: /my-java-notes
---

## Java 17

**1.** To execute Java 8 jar via Java 17, add these parameters -
```bash
/path/to/java17home/bin/java -Dsun.misc.URLClassPath.disableJarChecking=true  --add-opens jdk.naming.rmi/com.sun.jndi.rmi.registry=ALL-UNNAMED --add-opens java.base/java.lang=ALL-UNNAMED  --add-opens java.base/sun.security.action=ALL-UNNAMED --add-opens java.base/sun.net=ALL-UNNAMED
```

**2.** Add `?useSSL=false` to mysql 5 jdbc jar.

**3.** To install Oracle JDK (and not open-jdk) on Ubuntu, first download and install the .deb file and then execute the below command to add java in System - 
```bash
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk-19/bin/javac" 1
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk-19/bin/java" 1
```
Change the jdk path according to your version.
After then set the java 19's javac and java binaries version using the below command. Select the java 19 option. 
```bash
sudo update-alternatives --config javac
```
The output will be like
```bash
There are 2 choices for the alternative javac (providing /usr/bin/javac).

  Selection    Path                                         Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-8-openjdk-amd64/bin/javac   1081      auto mode
  1            /usr/lib/jvm/java-8-openjdk-amd64/bin/javac   1081      manual mode
  2            /usr/lib/jvm/jdk-19/bin/javac                 1         manual mode

Press <enter> to keep the current choice[*], or type selection number: 2
update-alternatives: using /usr/lib/jvm/jdk-19/bin/javac to provide /usr/bin/javac (javac) in manual mode
```
Similarly update `java`
```bash
sudo update-alternatives --config java
```

