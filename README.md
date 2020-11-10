Ansible role: Elasticsearch
=========

This role is used to install elasticsearch cluster.

For now, it does the following:
- generates elasticsearch root CA and server/peer certificates if needed
- if separate disk path if defined, creates lvm volumes from it
- does all necessary OS preparations
- configures java and elasticsearch options, sets password for elastic and kibana users
- separate playbook to create elasticsearch users if defined
	

Requirements
------------

This is not strict requirements and it may not work with other versions than tested ones.
Anyway. feel yourself free to test by yourself, suggest addition of new functionality and contribute.

Role is tested with:
- Ansible version >= 2.8.6
- CentOS version >= 7.6 (1803)

Currently supports installation of Elasticsearch versions >= 7.3.

cfssl and cfssljson must be installed on localhost if elasticsearch_certs_create variable is set to True (default).


Role Variables
--------------

Variables and their descriptions copied from defaults/main.yml

```bash

# Elasticsearch is designed to work better with Transparent Huge Pages turned off
# it's better not to change this setting expect when your RAM size is very large,
# when enabling THP in OS and JVM gives performance gains, includes no_thp role as dependency:
elasticsearch_no_thp: true

# Variable which is common for most projects, used in
# configuration files or file/directory names.
# By default used as reference for elasticsearch_project_dir variable:
elasticsearch_project_name: test

# Variable which is common for most projects, used as
# project working directory on the localhost for the role.
# Currently is used to store created certificates:
elasticsearch_project_dir: "{{ elasticsearch_project_name }}"

# Use CFSSL to create elasticsearch cluster Root CA and self-signed certificates:
elasticsearch_certs_create: true

# Common name for elasticsearch Root CA:
elasticsearch_certs_ca_cn: "elasticsearch root ca"

# Key algorithm for elasticsearch Root CA:
elasticsearch_certs_ca_key_algo: "rsa"

# Key size for elasticsearch Root CA:
elasticsearch_certs_ca_key_size: 4096

# Distinguished names for elasticsearch Root CA:
elasticsearch_certs_ca_names:
- c: "Neverland"
  l: "Rivia"
  o: "Witchers"
  ou: "Caer Morhen"

# elasticsearch server certificate expiry in hours:
elasticsearch_certs_server_expiry: "17520h"

# Common name for elasticsearch server and peer certificate:
elasticsearch_certs_server_cn: "elasticsearch server"

# Key algorithm for elasticsearch server and peer certificate:
elasticsearch_certs_server_key_algo: "rsa"

# Key size for elasticsearch server and peer certificate:
elasticsearch_certs_server_key_size: 2048

# Distinguished names for elasticsearch server and peer certificate:
elasticsearch_certs_server_names:
- c: "Neverland"
  l: "Rivia"
  o: "Witchers"
  ou: "Caer Morhen"

# Hostnames, DNS names and/or IP addressed for which elasticsearch
# server and peer certificate will be signed. If your server hostnames
# and IP addressed are different from ones in inventory,
# provide them manually using this variable:
elasticsearch_certs_server_hosts: []

# Name of separate device in /dev/disk/by-path/ for elasticsearch storage:
elasticsearch_disk_path: ""          # pci-0000:13:00.0-scsi-0:0:0:0

# Version of elasticsearch package which will be installed:
elasticsearch_version: 7

# Revision of elasticsearch package which will be installed:
elasticsearch_revision: 3.2

# Elasticsearch cluster name:
elasticsearch_cluster_name: elasticsearch

# Elasticsearch transport ssl verification mode:
elasticsearch_transport_ssl_verification_mode: certificate

# Elasticsearch server ssl verification mode:
elasticsearch_server_ssl_verification_mode: none

# Maximum RAM amount in megabytes for JVM to set in "/etc/sysconfig/elasticsearch" file:
elasticsearch_java_opts_maxram_mb: 8192

# Password for "elastic" database admin user:
elasticsearch_elastic_pass: elastic

# Password for "kibana" system user:
elasticsearch_kibana_pass: kibana

# New users to create in elasticsearch:
elasticsearch_users: []
# - username: ""
#   body:
#     roles:
#     - ""
#     full_name: ""
#     email: ""
#     metadata: {}
#     enabled:
#     password: ""

```


Dependencies
------------

If elasticsearch_no_thp variable set to True (default), installs no_thp role to permanently disable Transparent Huge Pages, which is strongly recommended if nodes RAM is not too high.

cfssl and cfssljson must be installed on localhost if elasticsearch_certs_create variable is set to True (default).


Example Playbook
----------------

```bash
---
- hosts: localhost
  gather_facts: false
  become: no
  tasks:
  - name: Check ansible version >=2.8.6
    assert:
      msg: Ansible must be v2.8.6 or higher
      that:
      - ansible_version.string is version("2.8.6", ">=")
    tags:
    - check
  vars:
    ansible_connection: local

- hosts: all
  become: yes
  tasks:
  - import_role:
      name: elasticsearch
  # import this if you want to create new elasticsearch users only:
  - import_role:
      name: elasticsearch
      tasks_from: crud_objects

```

More detailed examples ( inventories, playbooks etc. ) of this and other roles can be found [here](https://github.com/caermeglaeddyv/examples/tree/dev/ansible).

It's highly recommended to start you test deploys from there, especially if you use Google Cloud Platform or VMware vCenter as your infrastructure, for now that [repository](https://github.com/caermeglaeddyv/examples) contains [Packer](https://github.com/caermeglaeddyv/examples/tree/dev/packer) and [Terraform](https://github.com/caermeglaeddyv/examples/tree/dev/terraform) examples to build templates and deploy machines on this platforms.


License
-------

[Apache 2.0](https://github.com/caermeglaeddyv/ansible-role-rear/blob/dev/LICENSE)


Author Information
------------------

Copyright 2020 [caermeglaeddyv](https://github.com/caermeglaeddyv)
