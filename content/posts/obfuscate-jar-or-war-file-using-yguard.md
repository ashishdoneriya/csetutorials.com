---
title: How to obfuscate jar or war file using yguard
date: 2016-09-20T08:15:24+05:30
lastmod: 2016-09-20T12:12:38+05:30
description: Obfuscate java code, jar or war files using yguard ant maven project. code obfuscation tool
slug: obfuscate-jar-or-war-file-using-yguard
categories:
  - Java
  - Web Development
---
YGuard is a free Java bytecode obfuscator and shrinker. It is one of the best code obfuscation tools. It improves your software deployment by prohibiting undesirable access to your source code. It is free and protect code from reverse engineering. It can obfuscate Names and shrinks Code.

YGuard is an Ant Task. It means it can be run through ant. If you are using maven then you would need a plugin called `maven-antrun-plugin`. You can call ant script from pom.xml using this plugin. Here the code for calling ant script using maven.

```xml
<profile>
  <id>yguard</id>
  <build>
    <plugins>
      <plugin>
    <artifactId>maven-antrun-plugin</artifactId>
    <version>1.8</version>
    <executions>
      <execution>
        <phase>install</phase>
        <configuration>
      <target>
        <ant antfile="build.xml" />
      </target>
        </configuration>
        <goals>
      <goal>run</goal>
        </goals>
      </execution>
    </executions>
      </plugin>
    </plugins>
  </build>
</profile>
```
Now lets start.

First [download](https://www.yworks.com/downloads#yGuard) the yguard jar file from its website and put it in your project directory. If its a maven project then I suggest you to put it with pom.xml file.

Create a file `build.xml` in your project directory. In maven project, put it with pom.xml.

### Obfuscate a war file

There is an excellent [ant script](http://codeaweso.me/2009/02/obfuscating-a-webapp-war-file-with-yguard-and-ant/) written by Mike Christianson. What he did in that script is  
1. Created two directories unwar.dir and obfuscation.dir  
2. Extract the war file in unwar.dir  
3. Created a jar of webapp classes (/WEB-INF/classes) called webapp.jar and put it in obfuscation.dir  
4. Created jar of web.xml called web.xml.jar and put it in obfuscation.dir  
5. Moved all the jar inside lib directory from unwar.dir/WEB-INF/lib/ to obfuscation.dir  
6. Obfuscated all the jars inside obfuscation.dir  
7. Put back all the jars in their respective places.  
8. Again create the war file from unwar.dir.  
9. That's it.

In short, place all the jars and code together in one place, obfuscate it and then put back all in their respective places.

**NOTE :** There can be some third party jars ( like gson) which can't be obfuscated (due to some class name problem). So you have to exclude those jars from being obfuscated. ie. Its better not to obfuscate third party jars.

### Obfuscate a single jar file

For obfuscating a single jar file, copy the below ant script and paste it in build.xml file.

```xml
<project xmlns='antlib:org.apache.tools.ant'>

  <!-- Creating temporary directory -->
  <mkdir dir="tempDir" />

  <!-- Copy the jar file to that temporary directory-->
  <copyfile src="jar-file-path/test-1.0.jar" dest="tempDir/test-1.0.jar"/>

  <!-- creating file set of jars (that contains our jar file and all other jars that uses this jar)-->
  <fileset dir="tempDir" includes="*.jar" id="jars.to.be.obfuscated" />

  <taskdef name="yguard" classname="com.yworks.yguard.YGuardTask" classpath="yguard.jar" />
  <!-- start the obfuscation -->
  <yguard>
    <inoutpairs>
      <fileset refid="jars.to.be.obfuscated" />
    </inoutpairs>
    <rename>
      <keep>
        <!-- Preventing the below class and methods from obfuscation -->
        <class name="com.csetutorials.Test" />
        <method class="com.csetutorials.Test" name="void main(java.lang.String[])" />
        <class classes="private" methods="private" fields="private">
          <patternset>
            <include name="com.csetutorials.beans.**" />
            <include name="com.csetutorials.beans.*" />
          </patternset>
        </class>
      </keep>
    </rename>
  </yguard>
  <!-- Obfuscation completed -->

  <!-- Delete our main jar file -->
  <delete file="jar-file-path/test-1.0.jar" />

  <!-- Copy the obfuscated jar file-->
  <copy todir="jar-file-path/">
    <fileset dir="tempDir" />
    <globmapper from="*_obf.jar" to="*.jar" />
  </copy>

  <!-- Delete temporary directory -->
  <delete dir="tempDir" />
</project>
```

I think the script is self explanatory. You can do changes according to your requirements.