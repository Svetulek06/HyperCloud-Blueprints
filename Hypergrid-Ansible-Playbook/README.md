# Ansible Playbook for Provisioning Blueprints from HyperCloud Portal

This is an Ansible playbook for provisioning HyperCloud Portal Blueprints. 

Populate the following variables with the data from you environment:

* **hypercloud_url** - The URL of your HCP instance (either SaaS or On-Premises)
* **hcp_user** - User's API Access Key
* **hcp_password** - User's API Secret Key
* **hcp_blueprint** - Blueprint ID, which can be found by clicking the blueprint in the App Store
* **hcp_cloudprovider** - Find cloudprovider and resourcepool by using curl --user <api access key>:<api secret key> <hypercloud_url>/api/blueprints/<hcp_blueprint>
* **hcp_resourcepool** - See note above
  
  ### Run
  
  `ansible-playbook hypercloud-ansible-playbook.yml`
  
  done!


![demo](https://raw.githubusercontent.com/mascij/hypergrid-ansible-playbook/master/hypercloud-ansible.gif)


```
---
- hosts: localhost
  vars:
    hypercloud_url: "https://hypercloudportallocation.local"
    hcp_user: "xncw779KYGr5xBpd79GH" 
    hcp_password: "gPECNgKaQowafMUlj0IXOgflD1dphv6e0Mu8g1TL" 
    hcp_blueprint: "2c91808664f9637f0165159b27f94f2c" 
    hcp_cloudprovider: "2c91808663f16d050163f534253f00d8"
    hcp_resourcepool: "2c91808664f9637f0165159c3ed84f37"

  tasks:
    - name: "Provision Blueprint from HyperCloud Portal"
      uri:
        url: "{{ hypercloud_url }}/api/dockerservers/sdi"
        method: POST
        user: "{{ hcp_user }}"
        password: "{{ hcp_password }}"
        #validate_certs: false
        body_format: json
        body: '{
            "blueprint":"{{ hcp_blueprint }}",
            "cloudProvider":"{{ hcp_cloudprovider }}",
            "resourcePool":"{{ hcp_resourcepool }}",
            "cluster":null,"params":[],
            "terminationProtection":"DISABLED",
            "skipAgentInstall":"true"
          }'
        return_content: true
        force_basic_auth: true
        status_code: 200
        register: json_resp

    - debug:
      var: json_resp
      when: debug is defined
```



