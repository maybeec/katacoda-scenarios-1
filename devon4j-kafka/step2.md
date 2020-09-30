## Set up the java project

Inside the devonfw directory navigate to the folder 'workspaces/main/devon4j-kafka/'.
`cd workspaces/main/devon4j-kafka/`{{execute}}

This folder contains a zipped java project. Unzip the project.
`unzip KafkaSampleApplication.zip`{{execute}}

This java project contains an employee class with some attributes. This class is used to generate the frontend files later in this tutorial.

The following dependencies have to be added to the pom.xml of the core module. The project already contains these dependencies.

`<dependency>
	<groupId>com.devonfw.java.starters</groupId>
	<artifactId>devon4j-starter-kafka-receiver</artifactId>
	<version>2020.08.001</version>
</dependency>
<dependency>
    <groupId>org.awaitility</groupId>
	<artifactId>awaitility</artifactId>
	<version>4.0.3</version>
	<scope>test</scope>
</dependency>`

#Build the project

`devon mvn package -Dmaven.test.skip=true`{{execute}}

`devon mvn clean install -Dmaven.test.skip=true`{{execute}}