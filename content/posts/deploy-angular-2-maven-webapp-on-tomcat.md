---
title: Deploy Angular Maven WebApp on Tomcat
date: 2016-08-08T07:16:16+05:30
lastmod: 2020-03-23T23:43:00+05:30
description: Steps to configure maven project to auto compile and build angular app.
slug: deploy-angular-2-maven-webapp-on-tomcat
categories:
  - Web Development
tags:
  - ANGULAR
  - maven
---
In this post I'm going to show you how you could integrate Angular with Maven automatically. I request you to read this article carefully and not for just copy paste because it would be very helpful for you in various ways like debugging, tweaking or modifying steps etc.

## First of all here is the summary of the steps for integration

1. Setup nodejs and npm
2. Run command `npm install` in angular directory to install dependencies and compile typescript files.
3. Run command `npm run build` in angular directory to build Angular project.
4. Finally copied the contents from `your-angular-directory/dist/` in root of .war file (before finally making .war file).

## Explaination

We are going to do all these steps automatically means we want maven to do all these steps above for us.

If you donot understand the 4th step then let me explain. Once I analyzed the contents of a .war file and what I found is that the .html, javascript and css files are placed in the root of war file. From this I got an idea that to integrate angular with maven, I have to copy the contents of dist directory(build directory) from angular folder to the root of war with the help of maven.

If you want full automation ie. you want maven to setup nodejs and run commands npm install and npm run build for you then build time increases a lot.

But if you are ready to run commands npm install and npm run build manually then running command mvn clean install would be very fast. This is semi automatic way. I will show you both ways.

So lets start. This is my project structure for maven project.

```bash
.
├── angular
│   ├── angular-cli-build.js
│   ├── angular-cli.json
│   ├── config
│   ├── dist
│   │   ├── app
│   │   ├── bundle.js
│   │   ├── favicon.ico
│   │   ├── index.html
│   │   ├── main.js
│   │   ├── system-config.js
│   │   └── vendor
│   ├── e2e
│   ├── package.json
│   ├── public
│   ├── README.md
│   ├── src
│   ├── tmp
│   ├── tslint.json
│   ├── typings
│   └── typings.json
├── cat.txt
├── pom.xml
├── src
│   └── main
│       ├── java
│       ├── resources
│       └── webapp
│           └── WEB-INF
│               └── web.xml
├── target
└── temp
```


And Just for your knowledge this is the content of the .war file that I found when I was analyzing it.

```bash
.
├── app
├── cat.txt
├── favicon.ico
├── index.html
├── main.js
├── META-INF
├── system-config.js
├── vendor
└── WEB-INF
    ├── lib
    └── web.xml
```


## Fully automatic way

I am writing the code first that you have to add in pom.xml. I will explain after that.
```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>2.6</version>
    </plugin>

    <!-- Plugin to execute command  "npm install" and "npm run build" inside /angular directory -->
    <plugin>
      <groupId>com.github.eirslett</groupId>
      <artifactId>frontend-maven-plugin</artifactId>
      <version>1.0</version>
      <configuration>
        <workingDirectory>angular</workingDirectory>
        <installDirectory>temp</installDirectory>
      </configuration>
      <executions>
        <!-- It will install nodejs and npm -->
        <execution>
          <id>install node and npm</id>
          <goals>
            <goal>install-node-and-npm</goal>
          </goals>
          <configuration>
            <nodeVersion>v6.3.1</nodeVersion>
            <npmVersion>3.9.5</npmVersion>
          </configuration>
        </execution>

        <!-- It will execute command "npm install" inside "/angular" directory -->
        <execution>
          <id>npm install</id>
          <goals>
            <goal>npm</goal>
          </goals>
          <configuration>
            <arguments>install</arguments>
          </configuration>
        </execution>

        <!-- It will execute command "npm build" inside "/angular" directory to clean and create "/dist" directory-->
        <execution>
          <id>npm build</id>
          <goals>
            <goal>npm</goal>
          </goals>
          <configuration>
            <arguments>run build</arguments>
          </configuration>
        </execution>
      </executions>
    </plugin>

    <!-- Plugin to copy the content of /angular/dist/ directory to output directory (ie/ /target/transactionManager-1.0/) -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-resources-plugin</artifactId>
      <version>2.4.2</version>
      <executions>
        <execution>
          <id>default-copy-resources</id>
          <phase>process-resources</phase>
          <goals>
            <goal>copy-resources</goal>
          </goals>
          <configuration>
            <overwrite>true</overwrite>
            <outputDirectory>${project.build.directory}/${project.artifactId}-${project.version}/</outputDirectory>
            <resources>
              <resource>
                <directory>${project.basedir}/angular/dist</directory>
              </resource>
            </resources>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```


In the above build code, we used these two new plugins frontend-maven-plugin and maven-resources-plugin.  
1. **frontend-maven-plugin** - It will install nodejs and build the angular project.  
2. **maven-resources-plugin** - It will copy the contents of ‘dist' directory to root of .war file.

### How we used frontend-maven-plugin?

```xml
<configuration>
  <workingDirectory>angular</workingDirectory>
  <installDirectory>temp</installDirectory>
</configuration>
```


In the above code we are telling frontend-maven-plugin that our angular code is in `/angular` directory and the directory in which we want it to install nodejs is `/temp`.

```xml
<execution>
  <id>install node and npm</id>
  <goals>
    <goal>install-node-and-npm</goal>
  </goals>
  <configuration>
    <nodeVersion>v6.3.1</nodeVersion>
    <npmVersion>3.9.5</npmVersion>
  </configuration>
</execution>
```


In the above code we are specifying node and npm version to install it on our system automatically.

```xml
<execution>
  <id>npm install</id>
  <goals>
    <goal>npm</goal>
  </goals>
  <configuration>
    <arguments>install</arguments>
  </configuration>
</execution>
```


It will execute command `npm install` inside the `workingDirectory` (ie. `/angular/`)

```xml
<execution>
  <id>npm build</id>
  <goals>
    <goal>npm</goal>
  </goals>
  <configuration>
    <arguments>run build</arguments>
  </configuration>
</execution>
```


It will execute command `npm run build` inside the `workingDirectory` (ie. `/angular/`)

### How we used maven-resources-plugin?

```xml
<execution>
  <id>default-copy-resources</id>
  <phase>process-resources</phase>
  <goals>
    <goal>copy-resources</goal>
  </goals>
  <configuration>
    <overwrite>true</overwrite>
    <outputDirectory>${project.build.directory}/${project.artifactId}-${project.version}/</outputDirectory>
    <resources>
      <resource>
  <directory>${project.basedir}/angular/dist</directory>
      </resource>
    </resources>
  </configuration>
</execution>
```


It will copy the content of `/angular/dist/` directory to output directory (`${project.basedir}/angular/dist`).

Now you can execute command `mvn clean install` and your .war will be ready in a few minutes. But I suggest you to read the full article first.

**NOTE :**  
1. Add index.html in your welcome files list in `web.xml`.  
ie. Paste the below code in `web.xml`

```xml
<welcome-file-list>
  <welcome-file>index.html</welcome-file>
</welcome-file-list>
```


2. Add `"build": "ng build"` script in your `package.json`

```
{
  "name": "angular",
  "version": "0.0.0",
  "license": "MIT",
  "scripts": {
    "start": "ng serve",
    	.
    	.
    	.

    "build": "ng build --prod"
  },
  "dependencies": {
   	.
   	.
   	.
  },
  "devDependencies": {
   	.
   	.
  }
}
```


You can check my projects (have not updated for a long time but concept remain same)
* [BaceDevotees](https://github.com/ashishdoneriya/BaceDevotees)
* [TransactionManager](https://github.com/ashishdoneriya/TransactionManager)

## Semi Automatic way

This way would be very helpful if the above method won't work or you want to build your maven project separately from angular project.  
If you are the only person in your team who changes angular project then you could also use this method. In my team one person commit angular code with angular/dist directory so that others could easily build their maven project.

The code is similar to the above method except we remove the frontend-maven-plugin code.

```xml

<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>2.6</version>
    </plugin>

    <!-- Plugin to copy the content of /angular/dist/ directory to output directory (ie/ /target/transactionManager-1.0/) -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-resources-plugin</artifactId>
      <version>2.4.2</version>
      <executions>
        <execution>
          <id>default-copy-resources</id>
          <phase>process-resources</phase>
          <goals>
            <goal>copy-resources</goal>
          </goals>
          <configuration>
            <overwrite>true</overwrite>
            <outputDirectory>${project.build.directory}/${project.artifactId}-${project.version}/</outputDirectory>
            <resources>
              <resource>
                <directory>${project.basedir}/angular/dist</directory>
              </resource>
            </resources>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```


Thank you. Now first run `npm install` and `npm run build<code> commands in your angular directory and then <code>mvn clean install` in your root directory (maven project).

If you have any queries feel free to ask.

**Also don't forget to read this**  
[https://github.com/ashishdoneriya/LittleBlueBird/wiki](https://github.com/ashishdoneriya/LittleBlueBird/wiki)
It will describe what's wrong with urls in Angular.