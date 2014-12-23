ansible-docker-registry 
=======================

[![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-docker--registry-yellow.svg)](https://galaxy.ansible.com/list#/roles/2343)

Ansible Role to deploy/configurat Private Docker Registry on Ubuntu 14.04 (or later)

# Description

This is a simple playbook creating a docker-registry role to setup an independent private docker registry and configuring by registry,nginx and redis on the docker container. Role is based upon docker registry [v0.9.0] (https://registry.hub.docker.com/_/registry/).

For now, The role used a host port based connection between containers.

# Quick start

- Install the role.

```
sudo  ansible-galaxy install anarcher.docker-registry 
```

- playbook.yml

```
---
- hosts: registries
  sudo: yes
  roles:
      - anarcher.docker-registry
  pre_tasks:
      - name: check apt last update
        stat: path=/var/cache/apt
        register: apt_cache_stat
      - name: update apt if needed
        apt: update_cache=yes
        when: ansible_date_time.epoch|float - apt_cache_stat.stat.mtime > 60*60*12
  tasks:
      - user: name=ubuntu groups=docker append=yes
  vars:
      docker:
          version: "1.3.0*"
      postgres:
          use: no
      registry:
          image: "anarcher/docker-registry"
          version: "0.9.0"
          aws_region: [AWS_REGIN]
          aws_bucket: [AWS_BUCKET_NAME]
          aws_key: [AWS_KEY]
          aws_secret: [AWS_SECRET_KEY]
          storage_redirect: true 
          settings_flavor: s3
      nginx:
          domain: [DOMAIN]
          username: [USERNAME]
          password: [PASSWORD]
          #use_ssl: True
          #use_redirect_ssl: True
          #ssl_certificate_src: "files/nginx.crt"
          #ssl_certificate_key_src: "files/nginx.key.nopass.pem"

```

- ansible.cfg

```

[defaults]
hash_behaviour = merge

```

Note that the role uses an nested hash variables (redis,postgres,registry,nginx).
If you want to use it. you should change *hash_behaviour = merge* in ansible.cfg. (https://github.com/anarcher/ansible-docker-registry/blob/master/defaults/main.yml)

- play playbook

``` 
ansible-play -i hosts.yml playbook.yml
```

# State

- "WORK IN PROGRESS"

