To test kafka the application contains a unit test.
`devonfw/workspaces/main/devon4j-kafka/KafkaSampleApplication/core/src/test/java/com/devonfw/application/employee/employeemanagement/service/impl/rest/KafkaDemoServiceTest.java`{{open}}

The test creates and deletes an employee using messaging by kafka.

For passing the test you now have to create the kafka producer and consumer in the next steps.