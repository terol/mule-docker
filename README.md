# Mule ESB Community Edition Docker Image
Docker image with Mule ESB on Debian Linux and OpenJDK Java 8.

## Building
Before building with Maven, the docker.host.url property in the pom.xml file needs
to be set to the URL on which the Docker daemon API is exposed.<br/>
To build a Docker image for a particular version of the Mule ESB, use docker:build with the appropriate profile. Example:<br/>
```mvn -Pmule-4.0.0 docker:build```

## Running
In order for the time of the container to be synchronized (using ntpd), it must be run with the SYS_TIME capability.
In addition you may want to add the SYS_NICE capability, in order for ntpd to be able to modify its priority.

Example:
```
docker run --cap-add=SYS_TIME --cap-add=SYS_NICE ivankrizsan/mule-docker:latest
```

## Volumes
- /opt/mule-standalone/logs       - Log output directory.
- /opt/mule-standalone/config     - Directory containing configuration for applications, domains etc.
- /opt/mule-standalone/apps       - Deployment directory for Mule applications.
- /opt/mule-standalone/domains    - Deployment directory for Mule domains.

## Environment
- SET_CONTAINER_TIMEZONE - Whether to set the timezone of the Docker container when starting it. Default is true.
- CONTAINER_TIMEZONE - Timezone to use in container. Default is Europe/Stockholm.
- MULE_EXTERNAL_IP - External IP address of the Docker container. On Mac and Windows this will be the Docker machine's IP address.
If not set, an attempt will be made to use the IP address of the container once it is started.
This IP address is used to expose JMX of the Mule ESB instance running in the Docker container.

Example:
```
docker run -e "SET_CONTAINER_TIMEZONE=true" -e "CONTAINER_TIMEZONE=Europe/Stockholm" -e "MULE_EXTERNAL_IP=192.168.99.100" -p "1099:1099" ivankrizsan/mule-docker:latest
```

## Exposed ports
- 8081  - Default HTTP port.
- 1099  - JMX port.
- 8899  - Jolokia HTTP service port.

## JXM Monitoring
To monitor a Mule ESB instance running in a Docker container, use the following JMX service URL:<br/>
```
service:jmx:rmi:///jndi/rmi://192.168.99.100:1099/jmxrmi
```
<br/>Note that the IP address may need to be updated and the port number depends on the port mapping configuration when the container was launched.<br/>

## JMX Monitoring with Jolokia
Jolokia is installed in the Mule ESB running in containers created from this Docker image.
Its HTTP API is exposed on the Jolokia HTTP service port as listed above.
To change this port, modify the mule-config.xml file in the jolokia-enabler Mule application.
For more information on Jolokia, please refer to https://jolokia.org/

## Mule ESB 4
The Docker image containing version 4 of Mule ESB will not have Jolokia installed.
