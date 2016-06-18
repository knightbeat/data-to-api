# data-to-api#

**Demonstration Scenario**

![alt text](https://github.com/knightbeat/data-to-api/blob/master/artifacts/data-to-api-scenario.png "Demostration Scenario")


**Environment Setup**
	
  1. Add a host entry to the host machine
     - `docker-machine start` - will start the docker machine if you are using `windows` or `OSx`.
     - `docker-machine ip` - will display the IP address of the docker machine.
     - Open the `hosts` file of the host machine, and add an entry as `<ip-address> docker.machine`.
  2. Download WSO2 product binary packages
     - WSO2 API Manager 1.10.0 - `wso2am-1.10.0.zip`
     - WSO2 Data Analytics Server 3.0.1 - `wso2das-3.0.1.zip`
     - WSO2 Data Services Server 3.5.0 - `wso2dss-3.5.0.zip`
     - WSO2 Enterprise Service Bus 4.9.0 - `wso2esb-4.9.0.zip`
  3. Add these 4 binary packs to the `packs` directory.
  
**Demostration Setup Overview**

![alt text](https://github.com/knightbeat/data-to-api/blob/master/artifacts/data-to-api-topology-and-ports-mapping.png "Container Topology and Ports Mapping")
  
**WSO2 Data Services Server (DSS)**

  1. Start the DSS container
     - `docker-compose up -d dbs` - will create the container image and boot it up.
     - `docker-compose up -d dss` - will create the DSS container image and boot it up.
     - `docker ps` - lists the running container information. 
     - Observe the NAME values ( dss.dbtoapi.com , dbs.dbtoapi.com ) of the containers.
  2. Upload Artifacts
     - Tail DSS logs, and observe.
         - Run `docker exec -it dss.dbtoapi.com tailf wso2/wso2dss-3.5.0/repository/logs/wso2carbon.log`
     - Open the admin management console with [https://docker.machine:9445/carbon](https://docker.machine:9445/carbon) (user=admin, password=admin).
     - Go to `Manage > Carbon Applications > Add`.
     - Upload `artifacts/data-to-api-composite-project_1.0.0.car`.
     - Observe DSS logs and verify that the artifacts were deployed successfully.
  3. Open SOAPUI, and load project  `artifacts/data-to-api-soapui-project-soapui-project.xml`.
  4. Observe operations of the two Data Services.
     - CourseInformationDataService
         - Try the operations `getEnrollments`: and `getEnrollmentsBySubject` with `5242GW` as `subjectCode`.
     - StudentInformationDataService
         - Try the operations `getStudents`: and `getStudentById` with `02341334` as `studentId`.
         
**WSO2 Enterprise Service Bus (ESB)**

  1. Start the ESB container
     - `docker-compose up -d esb` - will create the ESB container image and boot it up.
     - `docker ps` - lists the running containers information. 
     - Observe the NAME value ( esb.dbtoapi.com ) of this container.
  2. Upload Artifacts
     - Tail ESB logs, and observe.
         - Run `docker exec -it esb.dbtoapi.com tailf wso2/wso2esb-4.9.0/repository/logs/wso2carbon.log`
     - Open the admin management console with [https://docker.machine:9444/carbon](https://docker.machine:9444/carbon) (user=admin, password=admin).
     - Go to `Manage > Carbon Applications > Add`.
     - Upload `artifacts/data-to-api-composite-project_1.0.0.car`.
     - Observe ESB logs and verify that the artifacts were deployed successfully.
  3. Start a REST client like [POSTMAN](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en)
  3. Add `http://docker.machine:8281/registrations/enrollments/<an existing subject code>`
     - i.e. `http://docker.machine:8281/registrations/enrollments/5242GW`
  4. Invoke and observe the response
  5. Add header `Accept` with value `application/json`
  6. Invoke and observe the response and ESB logs
  7. Add header `Accept` with value `application/xml`
  8. Invoke and observe the response and ESB logs

**WSO2 API Manager (API-M)**

  1. Start the API-M container
     - `docker-compose up -d apim` - will create the API-M and WSO2 Data Analytics Server(DAS) container images and boot them up.
     - `docker ps` - lists the running containers information. 
     - Observe the NAME values ( api.dbtoapi.com, das.dbtoapi.com ) of this container.
  2. Open API-M user interfaces (user=admin, password=admin).
     - API-M Admin Console [https://docker.machine:9443/carbon](https://docker.machine:9443/carbon)
     - API-M Publisher [https://docker.machine:9443/publisher](https://docker.machine:9443/publisher)
     - API-M Store [https://docker.machine:9443/store](https://docker.machine:9443/store)
  3. Publish the API which was created on the ESB earlier.
     - Create an API on API-M publisher.
     - Provide a proper resource path (i.e. `/registrarapi`)
     - Add resource `/enrollments/{subjectCode}` with GET method.
     - Provide `http://esb.dbtoapi.com:8280/registrations` as the production URL
         - In this case, communication happens directly between the API-M and ESB docker containers.
         - It does not happen accross the host machine.
         - Therefore, instead of using `docker.machine` as the hostname to contact the ESB,
         - it is neccessary to use the ESB container name `esb.dbtoapi.com` as host name.
         - When the host machine doesn't involve in communication, the docker-compose port mappings do not apply.
         - Therefore, instead of using port '8281', directly use port '8280' (ignoring the port mapping).
  4. Subscribe to the API via the API-M Store.
  5. Try the API with Swagger based API Console on the API-M Store UI.
  6. Or, use a REST client like [POSTMAN](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en)
     - i.e. `http://docker.machine:8280/registrarapi/1.0.0/enrollments/5242GW`
     
**Additionally note**
  You can also expose the Data Services endpoints (both SOAP and REST) directly through the API Manager, without making a route through the ESB installation.

**When You are done...**

  1. Run `docker stop $(docker ps -a -q)` to stop all containers.
  2. Run `docker-machine stop` to stop the machine.
  
