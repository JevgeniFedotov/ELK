# Repository for ELK deployment via Ansible to Ubuntu servers
This playbook will install ELK stack (Elasticsearch, Kibana, Logstash, Filebeat) on remote servers.
# Requirements
1. 5 Ubuntu VM's (20.04 LTS), 3 for Elasticsearch, 1 for Kibana, 1 for Logstash/Filebeat.
2. Installed ansible on local machine in order to run playbook.
3. Network rules:
* 3.1 Elasticsearch - port 9200 has to be open for communication between Elastic nodes and in order for Kibana and Logstash to reach Elastic nodes via this port.
* 3.2 Kibana - port 80 has to be open, Nginx webserver is listening on that port. 443 is for SSL, but this playbook doesn't cover SSL.
* 3.3 Kibana - port 5601 has to be open, which is Kibana server port.
* 3.4 logstash - port 5042 should be open as Logstash is listening on that port.
# Instructions
1. Clone this repo to your local machine:
```
git clone https://github.com/JevgeniFedotov/ELK
```
2. Edit inventory/hosts file and replace IP's with your servers.
3. Cd into ELK directory and run:
```
ansible-playbook install.yml -i inventory/hosts
```
# Accessing Kibana after deployment
There's 2 ways to access Kibana:
1. http://167.99.133.251:5601
This won't prompt any login and just takes you to Kibana.
2. http://167.99.133.251
This will ask for creditentials, which were created in nginx role (user/pw defined in global_vars/all.yml).
But for some reason this results in "502 Bad Gateway", probably some issue with network rules, but I couldn't figure it out.
# Adding index in Kibana after deployment
1. Go to Elastic01 and performe following curl (This will provide list of indexes that Elastic is receiving.)
```
curl -X GET <InsertElastic01ipPHere>:9200/_cat/indices?v
```
2. Take some index that has some docs.count , for example filebeat-2020.12.20.
3. Go to Kibana home, Manage, Index Patterns, press Create Index Pattern (In this example: http://167.99.133.251:5601/app/management/kibana/indexPatterns/create)
4. And Add desired index there: filebeat-2*
5. And on next step, select @Timestamp under Time field.
6. Now that desired index is added, you should see logs from that index in "Discover" category in Kibana.
