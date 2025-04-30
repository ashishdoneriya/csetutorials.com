---
title: Jersey Server Side Events Tutorial
date: 2019-12-20T08:43:01+05:30
lastmod: 2020-03-06T14:23:00+05:30
description: Send data from server as soon as data becomes available without pinging server from client frequently.
slug: jersey-sse-tutorial
categories:
  - Web Development
tags:
  - JAVA
  - jersey
  - sse
---
In this article I'm going to explain what is SSE (Server Side Events) and after that will show you an example.

## What is SSE?

In general scenario when we want to send something to the client, then client creates a requests to the server. After that the server fetches necessary information / data and sends to the client and then connection is closed. Suppose what if the time to fetch the data is very long.

Lets say there are 100 people information and client wants server to verify each. So the client sends the information of 100 people to server. Lets say the time taken by server to verify each person's information is 10 seconds. So it would take around 16 minutes for server to verify and send the response to the client. In this case, while waiting, the client could become frustrated as he sees no progress. He could think that there might be some error or he hadn't press the button correctly.

There is a solution to this problem and that is sending the information of person from server to client as soon as that person's information is verified. It means if the server has finished verifying person 1 then notify the client that it has verifed person 1, if the server has finished verifying person 2 then notify the client that it has verifed person 2 and so on. You might not be aware of such facility in which you could send response to client one by one.

SSE (Server Side Events) is such facility using which we could send response one by one. In the below exmple I'm going to explain how you can create an sse rest api using Jersey. Jersey is a web services framework to create rest apis.

## Example Project

1. Create a maven project using this command

```bash
mvn archetype:generate -DarchetypeArtifactId=jersey-quickstart-grizzly2 \
-DarchetypeGroupId=org.glassfish.jersey.archetypes -DinteractiveMode=false \
-DgroupId=com.csetutorials -DartifactId=jersey-sse-example -Dpackage=com.csetutorials \
-DarchetypeVersion=2.29.1
```

This will create the below file structure

```bash
└── jersey-sse-example
	├── pom.xml
	└── src
		├── main
		│   └── java
		│	   └── com
		│		   └── csetutorials
		│			   ├── Main.java
		│			   └── MyResource.java
		└── test
			└── java
				└── com
					└── csetutorials
						└── MyResourceTest.java
```

2. Add SSE dependency to pom.xml

```xml
<dependency>
	<groupId>org.glassfish.jersey.media</groupId>
	<artifactId>jersey-media-sse</artifactId>
	<version>2.29.1</version>
</dependency>
```


The final pom.xml would look like

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<groupId>com.csetutorials</groupId>
	<artifactId>jersey-sse-example</artifactId>
	<packaging>jar</packaging>
	<version>1.0-SNAPSHOT</version>
	<name>jersey-sse-example</name>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.glassfish.jersey</groupId>
				<artifactId>jersey-bom</artifactId>
				<version>${jersey.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<dependencies>
		<dependency>
			<groupId>org.glassfish.jersey.containers</groupId>
			<artifactId>jersey-container-grizzly2-http</artifactId>
		</dependency>
		<dependency>
			<groupId>org.glassfish.jersey.inject</groupId>
			<artifactId>jersey-hk2</artifactId>
		</dependency>

		<!-- uncomment this to get JSON support:
		 <dependency>
			<groupId>org.glassfish.jersey.media</groupId>
			<artifactId>jersey-media-json-binding</artifactId>
		</dependency>
		-->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.glassfish.jersey.media</groupId>
			<artifactId>jersey-media-sse</artifactId>
			<version>2.29.1</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>2.5.1</version>
				<inherited>true</inherited>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>exec-maven-plugin</artifactId>
				<version>1.2.1</version>
				<executions>
					<execution>
						<goals>
							<goal>java</goal>
						</goals>
					</execution>
				</executions>
				<configuration>
					<mainClass>com.csetutorials.Main</mainClass>
				</configuration>
			</plugin>
		</plugins>
	</build>

	<properties>
		<jersey.version>2.29.1</jersey.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
</project>
```


3. Add the below rest call to com.csetutorials.MyResource class

```java
@GET
@Path("sse-api")
@Produces(MediaType.SERVER_SENT_EVENTS)
public void getServerSentEvents(final @Context SseEventSink eventSink, final @Context Sse sse) {
	new Thread() {
		public void run() {
			for (int i = 0; i < 10; i++) {
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				final OutboundSseEvent event = sse.newEventBuilder()
							.name("message-to-client")
							.data(String.class, "Hello world " + i + "!")
							.build();
				// Before sending result check if the client has closed the connection, if
				// connection is closed then there is no need to send the data further
				if (eventSink.isClosed()) {
					return;
				}
				eventSink.send(event);
			}
			eventSink.close();
		}
	}.start();
}
```


In my case the final MyResource class looks like

```java
package com.csetutorials;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.QueryParam;
import javax.ws.rs.core.Context;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.sse.OutboundSseEvent;
import javax.ws.rs.sse.Sse;
import javax.ws.rs.sse.SseEventSink;

/**
 * Root resource (exposed at "myresource" path)
 */
@Path("myresource")
public class MyResource {

  @GET
  @Produces(MediaType.SERVER_SENT_EVENTS)
  public void getServerSentEvents(
	final @Context SseEventSink eventSink,
	final @Context Sse sse) {
	new Thread() {
	  public void run() {
		for (int i = 0; i < 10; i++) {
		  try {
			Thread.sleep(1000);
		  } catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		  }
		  final OutboundSseEvent event = 
				  sse.newEventBuilder().name("message-to-client")
				 .data(String.class, "Hello world " + i + "!")
				 .build();
		  if (eventSink.isClosed()) {
			return;
		  }
		  eventSink.send(event);
		  System.out.println("sending," + i);

		}
		eventSink.close();
	  }
	}.start();
  }
}
```


4. Execute command in the project root.

```bash
mvn clean install
```


5. To test, execute command in the project root.

```bash
mvn exec:java
```


the output is –

```java
[INFO] Scanning for projects...
[INFO]																		 
[INFO] ------------------------------------------------------------------------
[INFO] Building jersey-sse-example 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] >>> exec-maven-plugin:1.2.1:java (default-cli) > validate @ jersey-sse-example >>>
[INFO] 
[INFO] <<< exec-maven-plugin:1.2.1:java (default-cli) < validate @ jersey-sse-example <<<
[INFO] 
[INFO] --- exec-maven-plugin:1.2.1:java (default-cli) @ jersey-sse-example ---
Dec 19, 2019 3:09:32 PM org.glassfish.grizzly.http.server.NetworkListener start
INFO: Started listener bound to [localhost:8080]
Dec 19, 2019 3:09:32 PM org.glassfish.grizzly.http.server.HttpServer start
INFO: [HttpServer] Started.
Jersey app started with WADL available at http://localhost:8080/myapp/application.wadl
Hit enter to stop it...
```


Now open your browser and hit the url –

```bash
http://localhost:8080/myapp/myresource/
```

The response would be like

```bash
event: message-to-client
data: Hello world 0!

event: message-to-client
data: Hello world 1!

event: message-to-client
data: Hello world 2!

event: message-to-client
data: Hello world 3!

event: message-to-client
data: Hello world 4!

event: message-to-client
data: Hello world 5!

event: message-to-client
data: Hello world 6!

event: message-to-client
data: Hello world 7!

event: message-to-client
data: Hello world 8!

event: message-to-client
data: Hello world 9!
```


Source : [Jersey Documentation](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/sse.html)