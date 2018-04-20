<figure>
<img src="http://www.hypergrid.com/wp-content/themes/hypergrid/img/logo.png" alt="" />
</figure>

HyperCloud - Blueprints Repository
===========================

This repository is meant to house all HyperCloud Blueprint examples.  All Blueprint files in this repository are in the HyperCloud export format.  You can simply import these into HyperCloud to create new Application Container Blueprints.


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

__Required Plugins:__
- Nginx Setup Plugin
- Deploy Java War File Plugin

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
