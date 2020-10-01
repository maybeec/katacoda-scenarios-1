## Set up the java project

Inside the devonfw directory navigate to the folder 'workspaces/main/devon4j-kafka/'.
`cd workspaces/main/devon4j-kafka/`{{execute}}

This folder contains a zipped java project. Unzip the project.
`unzip KafkaSampleApplication.zip`{{execute}}

This java project contains a simple application which can create and delete employees. You can find the source code in the github repository https://github.com/devonfw-sample/devon4j-kafka-employeeapp.

To use devon4j-kafka you have to add required starter dependencies to the pom.xml file of the core module.

`groupId: com.devonfw.java.starters`
`artifactId: devon4j-starter-kafka-receiver`

The starter-kafka-receiver dependency includes the starter-kafka-sender dependency which is required for producing messages.

The project already contains the dependencies.
`devonfw/workspaces/main/devon4j-kafka/core/pom.xml`{{open}}

#Build the project

`devon mvn package -Dmaven.test.skip=true`{{execute}}

`devon mvn clean install -Dmaven.test.skip=true`{{execute}}