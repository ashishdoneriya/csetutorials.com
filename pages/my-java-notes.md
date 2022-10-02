---
title: My Java Notes
created: 2022-10-02T14:00:00+05:30
updated: 2022-10-02T14:00:00+05:30
author: ashishdoneriya
permalink: /my-java-notes
---

## Java 17

1. To execute Java 8 jar via Java 17, add these parameters -
```bash
/path/to/java17home/bin/java -Dsun.misc.URLClassPath.disableJarChecking=true  --add-opens jdk.naming.rmi/com.sun.jndi.rmi.registry=ALL-UNNAMED --add-opens java.base/java.lang=ALL-UNNAMED  --add-opens java.base/sun.security.action=ALL-UNNAMED --add-opens java.base/sun.net=ALL-UNNAMED
```

2. Add `?useSSL=false` to mysql 5 jdbc jar.
