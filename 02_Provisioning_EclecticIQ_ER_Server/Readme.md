Provisioning the EclecticIQ ER Server 
===================================

The EclecticIQ ER server is a headless server platform that is flexible and allows
you to effectively manage and control data. Here are the features that EclecticIQ ER
server offers:

-   Agent and policy management

-   Data forwarding and management

-   OpenC2-based out-of-band command and control

-   Incident response, including proactive threat hunting and remediation

This chapter includes these topics:

-   [Before you Begin](#before-you-begin)

-   [Installing the EclecticIQ_ER Server](#installing-the-polylogyx-server)

-   [Uninstalling the Server](#uninstalling-the-server)

-   [Upgrading the Server](#upgrading-the-server)

-   [Troubleshooting Server Installation
    Issues](#troubleshooting-server-installation-issues)

<br>Before you Begin
--------------------

Before you begin installation of the EclecticIQ ER server, ensure you read the
following information.

-   Server installation is supported only on the Ubuntu platform version 16.04 (or later) .

-   Recommended configuration for the PoC (supports 10-25 clients)

    -   250 GB of free space

    -   Quad Core processor


    -   16 GB RAM (Use 64 GB RAM if number of suppoerted clients > 500)

-   Docker and Docker Compose are required to install the EclecticIQ ER server.

-   In-bound Ports 5000, 9000 and 443 are open on the system. Port 5000 is used for UI, 9000 and 443 for agent communication

| Component      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Docker (18.03.1-CE)         | Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. To get started with Docker, review the information on their [website](https://docs.docker.com/install/overview/). Review instructions for [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce) or [Docker EE](https://docs.docker.com/install/linux/docker-ee/ubuntu/) to install Docker on Ubuntu. |
| Docker Compose (1.21.1 or later) | Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To install Docker Compose, complete the [prerequisites](https://docs.docker.com/compose/install/#prerequisites) and then perform [installation](https://docs.docker.com/compose/install/#install-compose). 


Installing the EclecticIQ ER Server
-------------------------------

After you install Docker and Docker Compose, you can install the EclecticIQ ER
server.


1. Checkout this repository on the local server.

2.  Switch to the repository directory.

    ```~/Downloads\$ cd plgx-esp/```
3.  Enter the certificate-generate.sh script to generate certificates for
    osquery.  
    ```~/Downloads/plgx-esp$ sh ./certificate-generate.sh <IP address>```
    ```x.x.x.x
    Generating a 2048 bit RSA private key
    .........................................................................................+++
    .........................+++
    writing new private key to 'nginx/private.key'
    ``` 
            
    In the syntax, \<IP address\> is the IP address of the system on which on to host the EclecticIQ ER server. This will generate 
    the certificate for osquery (used for provisioning clients) and place the certificate in the plgx-esp folder.

4.  Modify and save the .env file.

    1.  Edit the following configuration parameters in the file. In the syntax, replace the values in angle brackets with required values.
    ```
    ENROLL_SECRET=<secret value>
    POLYLOGYX_USER=<user login name> 
    POLYLOGYX_PASSWORD=<login password> 
    RSYSLOG_FORWARDING=true
    VT_API_KEY=<VirusTotal Api Key> 
    IBMxForceKey=<IBMxForce Key> 
    IBMxForcePass=<IBMxForce Pass>
    PURGE_DATA_DURATION=<number of days>  
    THREAT_INTEL_LOOKUP_FREQUENCY=<number of minutes>
     ```   
| Parameter | Description                                                                                                                                                                                  |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ENROLL_SECRET | Specifies the enrollment shared secret that is used for authentication.                                                                                                                              |
| POLYLOGYX_USER       | Refers to the user login name for the EclecticIQ ER server.                                                                                                         |
| POLYLOGYX_PASSWORD       | Indicates to the password for the EclecticIQ ER server user.                                                                                                              |
| RSYSLOG_FORWARDING       | Set to true to enable forwarding of osquery and EclecticIQ ER logs to the syslog receiver by using rsyslog. |
| VT_API_KEY       | Represents the VirusTotal API key.                                                                            | 
| IBMxForceKey       | Represents the IBMxForce key.                                                                            | 
| IBMxForcePass       | Specifies the IBMxForce pass.                                                                            | 
| PURGE_DATA_DURATION       | Specifies the frequency (in number of days) for purging the data.                                                                            | 
| THREAT_INTEL_LOOKUP_FREQUENCY       | Specifies the frequency (in minutes) for fetching threat intelligence data.                                                                            |   
    2. Ensure all the ports specified in the YAML file are open and accessible
    3. Save the file.
    


   
5.  Run the following command to start Docker compose.

    ```docker-compose -p 'plgx-esp' up -d```
    
    Typically, this takes approximately 10-15 minutes. The following lines appear on
    the screen when Docker starts:
    ````Starting plgx-esp_rabbit1_1  ... done
        Starting plgx-esp_postgres_1 ... done
        Starting plgx-esp_plgx-esp_1     ... done
        Attaching to plgx-esp_rabbit1_1, plgx-esp_postgres_1, plgx-esp_plgx-esp_1
        .
        .
        .
        Server is up and running```
        
6.  Log on to server using following URL using the latest version of Chrome or
    Firefox browser.
    
    ```https://<ip address>:5000```

    In the syntax, `<IP address>` is the IP address of the system on which the
    EclecticIQ ER server is hosted. This is the IP address you specified in step 4.

7.  Ignore the SSL warning, if any.

8.  Log on to the server using the credentials provided above at step 5.

9.  Provision the clients. For more information, see [Provisioning the EclecticIQ ER
    Client for Endpoints](../03_Provisioning_EclecticIQ_ER_Client/Readme.md).

Uninstalling the Server 
------------------------

To uninstall the EclecticIQ ER server, run the following command to clean-up
existing Docker images and containers.

```~/Downloads\$ sh ./docker-cleanup.sh```

**Note:** This will clean **all** the images and containers.

Upgrading the Server
--------------------

EclecticIQ ER can be upgraded from the earlier supported release through an upgrade script.

The upgrade_script.sh script is part of the platform bundle.

EclecticIQ ER could also be upgraded manually. Complete these steps to perform an upgrade:

1. Upgrade the code to the latest version with git pull or downloading the released bundle

2. Back up the certificate.crt and private.key files stored in the nginx directory in the installation directory. 

3. Execute ```sudo bash upgrade_script.sh --path <Path to the old installation directory>```  

4. Update the ```.env``` file 

5. Execute ```docker-compose -p 'plgx-esp' up --build -d```

|										|																							|
|:---									|													   								    ---:|
|[Previous << Architecture Overview](../01_Architecture/Readme.md)  | [Next >> Provisioning the EclecticIQ ER Client](../03_Provisioning_EclecticIQ_ER_Client/Readme.md)|
