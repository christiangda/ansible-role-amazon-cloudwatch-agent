Ansible Role: christiangda.amazon-cloudwatch-agent
=========
[![Build Status](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent.svg?branch=master)](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)
[![Ansible Role](https://img.shields.io/ansible/role/35159.svg)](https://galaxy.ansible.com/christiangda/amazon-cloudwatch-agent)

This module [Install CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html)

**My apologies, this is a WIP**
This version only:
* Download and install CloudWatch Agent from AWS OS Package
* Have handlers to reload configuration
* Have a default Advanced configuration file, your configuration could be load form JSON File

Requirements
------------

This role work on RedHat, CentOS, Amazon Linux, Debian and Ubuntu distributions

specific version:
* RedHat
  * 6
  * 7
* CentOS
  * 6
  * 7
* Amazon Linux
  * 1
  * 2
* Ubuntu
  * 14.04
  * 16.04
  * 18.10
* Debian
  * buster
  * jessie
  * sid
  * stretch

Role Variables
--------------


Dependencies
------------

* [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) (when is used with RedHat family)

Example Playbook
----------------

for RedHat/CentOS 6/7

    - hosts: servers
      gather_facts: True
      roles:
         - christiangda.amazon-cloudwatch-agent

for Amazon Linux 1/2 (my-playbook.yml)

    - hosts: all
      gather_facts: True
      become: true
      become_user: root
      become_method: sudo
      remote_user: ec2-user
      roles:
        - christiangda.amazon-cloudwatch-agent

Inventory file sample (inventory)

    [all]
    10.14.x.y
    10.14.v.z

    [amazon-1]
    10.14.x.y

    [amazon-2]
    10.14.v.z

How to used it

    ansible-playbook my-playbook.yml \
      --inventory inventory \
      --private-key [~/location of my key.pem] \
      --become \
      --become-user=ec2-user \
      --user ec2-user
      [--extra-vars "show_debug_messages=True"] --> additional

Development / Contributing
--------------------------


License
-------

This module is released under the GNU General Public License Version 3:

* [http://www.gnu.org/licenses/gpl-3.0-standalone.html](http://www.gnu.org/licenses/gpl-3.0-standalone.html)

Author Information
------------------

* [Christian González](https://github.com/christiangda)
