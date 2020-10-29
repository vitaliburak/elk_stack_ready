

Elk_Stack
==========
Introduction

The Elastic Stack — formerly known as the ELK Stack — is a collection of open-source software produced by Elastic which allows you to search, analyze, and visualize logs generated from any source in any format, a practice known as centralized logging. Centralized logging can be very useful when attempting to identify problems with your servers or applications, as it allows you to search through all of your logs in a single place. It’s also useful because it allows you to identify issues that span multiple servers by correlating their logs during a specific time frame.

The Elastic Stack has four main components:

Elasticsearch: a distributed RESTful search engine which stores all of the collected data.
Logstash: the data processing component of the Elastic Stack which sends incoming data to Elasticsearch.

Kibana: a web interface for searching and visualizing logs.

Beats: lightweight, single-purpose data shippers that can send data from hundreds or thousands of machines to either Logstash or Elasticsearch.

Here you will install the Elastic Stack on a CentOS 7 server. You will learn how to install all of the components of the Elastic Stack — including Filebeat, a Beat used for forwarding and centralizing logs and files — and configure them to gather and visualize system logs. Additionally, because Kibana is normally only available on the localhost, you will use Nginx to proxy it so it will be accessible over a web browser. At the end of this tutorial, you will have Elasticsearch, Kibana and Logstash on a  server, referred to as the Elastic Stack server, and Filebeat to push logs on the remote server.



Ansible-elk
===========
Ansible Playbook for setting up the ELK/EFK Stack and Filebeat client on remote hosts


[![CI](https://travis-ci.org/sadsfae/ansible-elk.svg?branch=master)](https://travis-ci.org/sadsfae/ansible-elk)

## What does it do?
   - Automated deployment of a full 6.x series ELK or EFK stack (Elasticsearch, Logstash, Kibana)
     * `5.6` and `2.4` ELK versions are maintained as branches and `master` branch will be 6.x currently.
     * Uses Nginx as a reverse proxy for Kibana, or optionally Apache via `apache_reverse_proxy: true`
     * Generates SSL certificates for Filebeat or Logstash-forwarder
     * Deploys ELK clients using SSL and Filebeat for Logstash (Default)
     * All service ports can be modified in ```elk_stack_ready/main.yml```
     * This is also available on [Ansible Galaxy](https://galaxy.ansible.com/sadsfae/ansible-elk/)

## Requirements
   - RHEL7 or CentOS7 server/client with no modifications and t2.medium size
   - RHEL7/CentOS7 or Fedora for ELK clients using Filebeat
   - ELK/EFK server with at least 8G of memory (you can try with less but 5.x series is quite demanding - try 2.4 series if you have scarce resources).
   

## Notes
   - Current ELK version is 6.x 
   - I will update this playbook for major ELK versions going forward as time allows.
   - Sets the nginx htpasswd to admin/admin initially
   - nginx ports default to 80/8080 for Kibana and SSL cert retrieval (configurable)
   - Uses OpenJDK for Java
   - It's fairly quick, takes around 3minutes on a test VM
   - Default Logstash
     - Set ```logging_backend: fluentd``` in ```roles/logstash/task/main.yml```
## ELK/EFK Server Instructions
   - Clone repo and setup your hosts file with your own ip of remote agent
```
git@github.com:tyncha/elk_stack_ready.git
cd elk_stack_ready and run modify the ip in inventory hosts file
```
   - If you're using a non-root user for Ansible, e.g. AWS EC2 likes to use ec2-user then set the follow below, default is root.

```
ansible_system_user: ec2-user
```

   - Run the playbook
```
ansible-playbook -i inventory main.yml -b
```

![ELK](/image/elk1.png?raw=true "Plan of ELK")

### Create your Kibana Index Pattern
   - Next you'll login to your Kibana instance and create a Kibana index pattern.

![ELK](/image/elk6-0.png?raw=true "Click Explore on my Own")

   - Note: Sample data can be useful, you can try it later however.

![ELK](/image/elk6-1.png?raw=true "Click Discover")

![ELK](/image/elk6-2.png?raw=true "Create index pattern")

![ELK](/image/elk6-3.png?raw=true "Select @timestamp from the drop-down and create index pattern")

![ELK](/image/elk6-4.png?raw=true "Click Discover")

   - At this point you can setup your client(s) to start sending data via Filebeat/SSL

   - Once this completes return to your ELK and you'll see log results come in from ELK/EFK clients in the Kibana 

![ELK](/image/elk6-5.png?raw=true "watch the magic")

   - You can view a deployment video here:

[![Ansible Elk](http://img.youtube.com/vi/6is6Ecxc2zE/0.jpg)](http://www.youtube.com/watch?v=6is6Ecxc2zE "Deploying ELK with Ansible")


## File Hierarchy
```
.
├── elk_stack_ready
│   ├── main.yml
|   ├── README.md
│   ├── inventory
│   │   └── hosts
│   └── roles
│       ├── elasticsearch
│       │   ├── files
│       │   │   ├── elasticsearch.in.sh
│       │   │   └── elasticsearch.repo
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       └── elasticsearch.yml.j2
│       ├── filebeat
│       │   ├── meta
│       │   │   └── main.yml
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       ├── filebeat.yml.j2
│       │       └── rsyslog-openstack.conf.j2
│       ├── kibana
│       │   ├── files
│       │   │   └── kibana.repo
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       └── kibana.yml.j2
│       ├── logstash
│       │   ├── files
│       │   │   ├── filebeat-index-template.json
│       │   │   └── logstash.repo
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       ├── 02-beats-input.conf.j2
│       │       ├── logstash.conf.j2
│       │       └── openssl_extras.cnf.j2
│       ├── nginx
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       ├── kibana.conf.j2
│       │       └── nginx.conf.j2
│       ├── prerequisites
│       │   ├── task
│       │   │   └── main.yml
│       │   ├── handlers
│       │   │   └── main.yml


42 directories, 61 files

```





Note: When installing the Elastic Stack, you should use the same version across the entire stack. This tutorial uses  Elasticsearch 6.5.2, Kibana 6.5.2, Logstash 6.5.2, and Filebeat 6.5.2

