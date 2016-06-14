# data-to-api#

**Environment Setup**
	
  1. Add a host entry to the host machine
     - `docker machine start` - will start the docker machine if you are using `windows` or `OSx`.
     - `docker machine ip` - will display the IP address of the docker machine.
     - Open the `hosts` file of the host machine, and add an entry as `<ip-address> docker.machine`.
  2. Download WSO2 product binary packages
     - WSO2 API Manager 1.10.0 - `wso2am-1.10.0.zip`
     - WSO2 Data Analytics Server 3.0.1 - `wso2das-3.0.1.zip`
     - WSO2 Data Services Server 3.5.0 - `wso2dss-3.5.0.zip`
     - WSO2 Enterprise Service Bus 4.9.0 - `wso2esb-4.9.0.zip`
  3. Add these 4 binary packs to the `packs` directory.
  
  **Data Services Server (DSS)**

  1. Start the service container
     - `docker-compose up -d dss` - will create the DSS container image and boot it up.
     - `docker ps` - lists the running container information. 
     - Observe the NAME value ( dss.dbtoapi.com ) of the container.
  2. Upload Artifacts
     - Tail DSS logs, and observe.
         - Run `docker exec -it esb.example.com tailf wso2/wso2dss-3.5.0/repository/logs/wso2carbon.log`
     - Open the admin management console with [https://docker.machine:9445/carbon](https://docker.machine:9445/carbon).
     - Go to `Manage > Carbon Applications > Add`.
     - Upload `artifacts/data-to-api-composite-project_1.0.0.car`.
     - Observe DSS logs and verify the artifacts were deployed successfully.
  2. Open SOAPUI, and load project  `artifacts/data-to-api-soapui-project-soapui-project.xml`.
  3. Observe operations of the two Data Services.
     - CourseInformationDataService
         - Try the operations `getEnrollments`: and `getEnrollmentsBySubject` with `5242GW` as `subjectCode`.
     - StudentInformationDataService
         - Try the operations `getStudents`: and `getStudentById` with `02341334` as `studentId`.
     