## CobiGen generation

Now you can use the CobiGen generator to create your angular components.

Cobigen cli will generate the files used by the angular application. Use the EmployeeEto.java file as input parameter.
`devon cobigen generate -o devon4ng-application-template/src/ devon4j_templateServer/api/src/main/java/com/devonfw/example/templateserver/employeemanagement/logic/api/to/EmployeeEto.java`{{execute}}

Cobigen cli will ask you which files should be generated. You can type the numbers seperated by comma.
`13,14,15,16`{{execute}}