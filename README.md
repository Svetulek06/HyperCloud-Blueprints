<figure>
<img src="http://www.hypergrid.com/wp-content/themes/hypergrid/img/logo.png" alt="" />
</figure>

HyperCloud - Blueprints Repository
===========================

This repository is meant to house all HyperCloud blueprint examples.  All Blueprint files in this repository are in the HyperCloud export format.  You can simply import these into HyperCloud to create new Application Container Blueprints.


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

