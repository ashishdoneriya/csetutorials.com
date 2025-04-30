---
title: Redirect Non www to www using UrlRewriteFilter in Web Servers
date: 2016-10-13T09:37:22+05:30
description: Redirect non-www Urls to www in web servers like Apache Tomcat, JBoss, Jetty with the help of UrlRewriteFilter
slug: redirect-non-www-using-urlrewritefilter-web-servers
categories:
  - Web Development
tags:
  - JAVA
---
On apache servers usually we redirect by modifying .htaccess file. But it doesn't work on web apps that are hosted on servers like tomcat, jboss etc. So in this case we'll use a Java Web Filter called [UrlRewriteFilter](http://tuckey.org/urlrewrite/). It allows us to change url before they hit the apis. It is compatible with all web servers (Apache Tomcat, JBoss, Jetty etc.). Its licence is BSD-3-Clause

If you want the latest jar then you can take the latest pull from its [github repository](https://github.com/paultuckey/urlrewritefilter) and compile it using command `mvn clean install`. 

So lets start ::

**1.** If you webapp is a maven webapp then add the dependency to your `pom.xml`

```xml
<dependency>
  <groupId>org.tuckey</groupId>
  <artifactId>urlrewritefilter</artifactId>
  <version>4.0.4</version>
</dependency>
```

Otherwise [download](http://central.maven.org/maven2/org/tuckey/urlrewritefilter/4.0.4/urlrewritefilter-4.0.4.jar) the jar file directly from maven repository and add in your webapp.

**2.** Now add this code in your WEB-INF/web.xml

```xml
<filter>
    <filter-name>UrlRewriteFilter</filter-name>
    <filter-class>org.tuckey.web.filters.urlrewrite.UrlRewriteFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>UrlRewriteFilter</filter-name>
  <url-pattern>/*</url-pattern>
  <dispatcher>REQUEST</dispatcher>
  <dispatcher>FORWARD</dispatcher>
</filter-mapping>
```

**3.** Create `urlrewrite.xml` in `WEB-INF` directory (same place as `web.xml`). Paste the following code in it.

```xml
<urlrewrite>
  <rule>
    <name>seo redirect</name>
    <condition name="host" operator="notequal">^www.csetutorials.com</condition>
    <from>^/(.*)</from>
    <to type="permanent-redirect" last="true">/$1</to>
  </rule>
</urlrewrite>
```

Don't forget to change the website name. That's it.