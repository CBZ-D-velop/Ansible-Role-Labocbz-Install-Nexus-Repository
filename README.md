# Ansible role: labocbz.install_nexus_repository

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: Sonatype](https://img.shields.io/badge/Tech-Sonatype-orange)
![Tag: Nexus Repository Manager](https://img.shields.io/badge/Tech-Nexus%20Repository%20Manager-orange)
![Tag: Docker](https://img.shields.io/badge/Tech-Docker-orange)

An Ansible role to install and configure a Nexus Repository Manager (by Sonatype) on your host  

This Ansible role simplifies the deployment of a Nexus3 repository using Docker. The role orchestrates the setup of a Nexus image, configuring it with maximum JVM settings. The Web UI is accessible through a specified address and port, while repository management addresses for Docker, NPM, Gradle, etc., are also defined. It is advisable to implement a reverse proxy in front of Nexus, as SSL is not handled by the solution. Users can customize the location of the data storage folder to suit their preferences.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
install_nexus_repository__container_name: "nexus"
install_nexus_repository__data_path: "/mnt/nexus-data"
install_nexus_repository__heap: "4096m"

install_nexus_repository__web_address: "0.0.0.0"
install_nexus_repository__web_port: 8081
install_nexus_repository__web_port_min: 8082
install_nexus_repository__web_port_max: 8100

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_install_nexus_repository__container_name: "nexus"
inv_install_nexus_repository__data_path: "/mnt/nexus-data"
inv_install_nexus_repository__heap: "4096m"

inv_install_nexus_repository__web_address: "0.0.0.0"
inv_install_nexus_repository__web_port: 8081
inv_install_nexus_repository__web_port_min: 8082
inv_install_nexus_repository__web_port_max: 8100

```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.install_nexus_repository"
  tags:
    - "labocbz.install_nexus_repository"
  vars:
    install_nexus_repository__container_name: "{{ inv_install_nexus_repository__container_name }}"
    install_nexus_repository__data_path: "{{ inv_install_nexus_repository__data_path }}"
    install_nexus_repository__heap: "{{ inv_install_nexus_repository__heap }}"
    install_nexus_repository__web_address: "{{ inv_install_nexus_repository__web_address }}"
    install_nexus_repository__web_port: "{{ inv_install_nexus_repository__web_port }}"
    install_nexus_repository__web_port_min: "{{ inv_install_nexus_repository__web_port_min }}"
    install_nexus_repository__web_port_max: "{{ inv_install_nexus_repository__web_port_max }}"
  ansible.builtin.include_role:
    name: "labocbz.install_nexus_repository"
```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-09-10: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez
* Role download sources, create service file, enable and start it
* Custom user is possible
* Service dont really start, need work on JVM

### 2023-10-06: New CICD, new Images

* New CI/CD scenario name
* Molecule now use remote Docker image by Lord Robin Crombez
* Molecule now use custom Docker image in CI/CD by env vars
* New CICD with needs and optimization

### 2024-01-08: Install with Docker

* Role use now Docker to deploy the Nexus
* Role create a path, with root permissions, maybe use an user ?
* Role expose default web UI (8081) but you can define a custom port / address
* Role expose a range with port to expose you repositories, with custom address to

### 2024-02-24: Fix and CI

* Added support for new CI base
* Edit all vars with __
* Tested and validated on Docker DIND (no binding)
* Removed docker socket local with ports

### 2024-05-19: New CI

* Added Markdown lint to the CICD
* Rework all Docker images
* Change CICD vars convention
* New workers
* Removed all automation based on branch

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
* [Download Sonatype Nexus Repository](https://help.sonatype.com/repomanager3/product-information/download)
* [Run as a Service](https://help.sonatype.com/repomanager3/installation-and-upgrades/run-as-a-service)
