FROM nuwanbando/javabase

RUN mkdir wso2 

COPY packs/wso2dss-3.5.0.zip wso2/

RUN cd wso2/ && \
        unzip wso2dss-3.5.0.zip
        
COPY packs/libs/mysql-connector-java-5.1.34-bin.jar /wso2/wso2dss-3.5.0/repository/components/lib/

COPY dss/conf/master-datasources.xml /wso2/wso2dss-3.5.0/repository/conf/datasources/

CMD ["sh","/wso2/wso2dss-3.5.0/bin/wso2server.sh"]

EXPOSE 9763
EXPOSE 9443