#Start the Kafka server

`/root/kafka/bin/zookeeper-server-start.sh config/zookeeper.properties`{{execute T2}}

`/root/kafka/bin/kafka-server-start.sh config/server.properties`{{execute T3}}

#Run the unit test
`devonfw/workspaces/main/devon4j-kafka/core/`{{execute}

`mvn clean test -Dtest=com.devonfw.application.employee.employeemanagement.service.impl.rest.KafkaDemoServiceTest`{{execute}