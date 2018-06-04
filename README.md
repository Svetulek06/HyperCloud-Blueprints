<figure>
<img src="http://www.hypergrid.com/wp-content/themes/hypergrid/img/logo.png" alt="" />
</figure>

HyperCloud - Blueprints Repository
===========================

This repository is meant to house all HyperCloud Blueprint examples.  All Blueprint files in this repository are in the HyperCloud export format.  You can simply import these into HyperCloud to create new Application Container Blueprints.

Some of these blueprints have a dependency on one or more plugins.  These are listed as "Required Plugins" in the section below for each particular blueprint.  Required plugins will be packaged in a zip file with the blueprint itself.  Importing a plugin is a separate activity from importing a blueprint into HyperCloud portal.  After the blueprint and plugin(s) are imported, you will have to identify the plugin ID that was associated with the imported plugin(s).  Take this ID and modify the associated plugin ID(s) in the YAML.


### Clone this project

You can clone this project:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
git clone https://github.com/hypergrid-inc/HyperCloud-Blueprints.git
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## Blueprint Listing

### Splunk Enterprise

Deploys a Splunk Enterprise container from the latest Docker image published by Splunk.

Splunk Enterprise is the platform for operational intelligence. The software lets you collect, 
analyze, and act upon the untapped value of big data that your technology infrastructure, security systems, 
and business applications generate. It gives you insights to drive operational performance and business results.

YAML
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
splunk:
  image: splunk/splunk:latest
  publish_all: true
  hostname: splunkenterprise
  environment:
  - "SPLUNK_START_ARGS=--accept-license"
  - "SPLUNK_USER=root"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### 3-Tier Java (Nginx-Tomcat-MySQL)

A simple Names Directory Java application. This 3-Tier Docker Java Application is deployed on Nginx, 
Tomcat Application Server, and MySQL. Once the application is deployed, get access to monitoring, alerts and notifications, 
continuous delivery using Jenkins, application backups, scale in/out, in-browser terminal to access the containers, 
log viewing, and container updates using custom plug-ins.

__Required Plugins:__
- Nginx Setup Plugin
- Deploy Java War File Plugin

YAML
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
LB:
  image: nginx:latest
  publish_all: true
  mem_min: 50m
  host: host1
  plugins:
    - !plugin
      id: 0H1Nk
      restart: true
      lifecycle: on_create, post_scale_out:AppServer, post_scale_in:AppServer, post_stop:AppServer, post_start:AppServer
      arguments:
        # Use container_private_ip if you're using Docker networking
        - servers=server {{AppServer | container_private_ip}}:8080;
        # Use container_hostname if you're using Weave networking
        #- servers=server {{AppServer | container_hostname}}:8080;
AppServer:
  image: tomcat:8.0.21-jre8
  mem_min: 600m
  host: host1
  cluster_size: 1
  environment:
    - database_driverClassName=com.mysql.jdbc.Driver
    - database_url=jdbc:mysql://{{MySQL|container_hostname}}:3306/{{MySQL|MYSQL_DATABASE}}
    - database_username={{MySQL|MYSQL_USER}}
    - database_password={{MySQL|MYSQL_ROOT_PASSWORD}}
  plugins:
    - !plugin
      id: oncXN
      restart: true
      arguments:
        - file_url=https://github.com/dchqinc/dchq-docker-java-example/raw/master/dbconnect.war
        - dir=/usr/local/tomcat/webapps/ROOT.war
        - delete_dir=/usr/local/tomcat/webapps/ROOT
MySQL:
  image: mysql:latest
  host: host1
  mem_min: 400m
  environment:
    - MYSQL_USER=root
    - MYSQL_DATABASE=names
    - MYSQL_ROOT_PASSWORD={{alphanumeric|8}}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### Grafana & InfluxDB: Monitoring & Alerting Stack v2

This blueprint deploys the hosted components needed to deliver a robust and feature monitoring/alerting
 stack built on the time series database InfluxDB and Grafana dashboards. A wide range of data reporting 
 agents can stream data to the InfluxDB instance, to begin capturing from Linux & Windows hosts it is 
 suggested to deploy Telegraf on your hosts and point them towards the IP & port of the deployed InfluxDB 
 container. The agents will automatically create the database. Add your InfluxDB instance as a datasource 
 in Grafana and users can begin building custom dashboards.

YAML
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
influxdb:
  image: library/influxdb:latest
  mem_min: 4096m
  publish_all: true
  restart: always
grafana:
  image: grafana/grafana:latest
  publish_all: true
  restart: always
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### HCP Reporter

This plugin provides detailed admin level reports in both a Web UI and CSV format. Fields can filtered & sorted for quick global views across a tenancy. 

Currently supports the following instance details: Created By User, Email, Instance Name, Host/IP, End Point Type (Cloud Provider), Region, Hardware Type, Connectivity Status, Image, HCP Id, Docker cluster name, Network, Hardware size details, Image Id (template, AMI, etc). 

With this you can quickly:
* View all instances in a specific cloud provider - "Show me all AWS instances"
* View all instances on a single subnet - "Show me all instances on 10.0.8.x"
* View all instances of a certain size - "Show me all m4.large", or "Sort by Size"
* View all instances owned by a certain user
* Find the most popular AMIs deployed by users
* Find the most popular cloud regions for users

Setup: In the blueprint YAML, be sure to change the HG User, HG Password, and URL

#### Demo

![alt text](https://github.com/hypergrid-inc/HyperCloud-Blueprints/blob/master/HCP%20Reporter/HCP-Reporter.gif?raw=true)

YAML
```
nginx:
  image: nginx:latest
  publish_all: true
  restart: always
  plugins:
    - !plugin
      id: 9Ud4X
      restart: true
      arguments:
        - hguser=admin@web.url
        - hgtoken=password
        - hcpurl=https://hcpurl.com
 ```

 ### Oracle-XE Cluster
 
 A simple Names Directory Java application. This 2-Tier Docker Java Application is deployed on IBM WebSphere Application Server and Oracle XE.
 
 YAML
```
 master_oracle:
  image: wnameless/oracle-xe-11g:latest
  host: host1
  mem_min: 400m
  environment:
    - SLAVE_IP={{slave_oracle | container_hostname}}
    - username=system
    - password=oracle
    - sid=xe
  plugins:
    - !plugin
      id: dkdG6
      restart: false
      lifecycle: on_create
      arguments:
        - SLAVE_IP={{slave_oracle | container_hostname}}
        - SLAVE_PASSWORD={{slave_oracle | password}}
slave_oracle:
  image: wnameless/oracle-xe-11g:latest
  host: host1
  mem_min: 400m
  environment:
    - username=system
    - password=oracle
    - sid=xe
  plugins:
    - !plugin
      id: UQvDO
      restart: false
      lifecycle: post_create
 ```
 
 Required Plugins:
	Oracle XE Master Setup (id: dkdG6)
	Oracle XE Slave Setup (id: UQvDO)
	
### AWS M3-Large West

This Machine Compose template creates an m3.large instance of Ubuntu 16.04 in the us-west-1 region. It also copies a CA certificate for the private registry from a secure S3 bucket.

Required Options:
  subnet - This is an available AWS subnet
  keypair - This is created and stored in AWS
  password - This is created in the HyperCloud Portal Credential Store. Username must be "ubuntu".

YAML
```
Machine: 
  # Note: Some of the fields are optional based on provider and can be left empty.
  group: <VM Name Prefix ie AWS-USWest>
  region: us-west-1
  description: This will provision an m3.large instance in the us-west-1 region.
  instanceType: m3.large
  image: us-west-1/ami-07585467
  subnet: <AWS-Subnet>
  affinity: true
  securityGroup: 
  keyPair: <Keypair>
  username: ubuntu
  password: "{{credentials | <Credential Store ID>}}"
  plugins:
    - !plugin
    # plugin_Copy CA Certificate for Private Registry_1.0
      id: BFtbm
    - !plugin
    # plugin_Touch Plugin_1.0
      id: SDzqZ
  count: 1
 ```
 
  Required Plugins:
     Copy CA Certificate for Private Registry_1.0 (id: BFtbm)
     Touch Plugin_1.0 (id: SDzqZ)