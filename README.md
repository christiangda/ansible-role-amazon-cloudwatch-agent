Ansible Role: christiangda.amazon-cloudwatch-agent
=========
[![Build Status](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent.svg?branch=master)](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)
[![Ansible Role](https://img.shields.io/ansible/role/39191.svg)](https://galaxy.ansible.com/christiangda/amazon_cloudwatch_agent)

This module [Install CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html)

**My apologies, this is a WIP**

This version only:
* Download and install CloudWatch Agent from AWS OS Package
* Has handlers to reload configuration
* Has a default agent configuration file
* ~~Load my own configuration file~~

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

WIP

Dependencies
------------

* [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) imported dynamically in the code when is used with RedHat family

Example Playbook
----------------

for RedHat/CentOS 6/7

    - hosts: servers
      gather_facts: True
      roles:
        - role: christiangda.amazon-cloudwatch-agent
          vars:
            cwa_agent_mode: ec2

for Amazon Linux 1/2 (my-playbook.yml)

    - hosts: all
      gather_facts: True
      become: true
      become_user: root
      become_method: sudo
      remote_user: ec2-user
      roles:
        - role: christiangda.amazon-cloudwatch-agent
          vars:
            cwa_agent_mode: ec2

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

Development / Contributing
--------------------------

This role is tested using [Molecule](https://molecule.readthedocs.io/en/latest/) and was developed using
[Python Virtual Environments](https://docs.python.org/3/tutorial/venv.html)

Prepare your environment

```bash
mkdir ansible-roles
cd ansible-roles/

virtualenv --no-site-packages --python /usr/bin/python2.7 vend
source vend/bin/activate
pip install pip --upgrade
pip install pytest
pip install pytest-mock
pip install pylint
pip install rope
pip install autopep8
pip install yamllint
pip install molecule
pip install ansible
pip install docker-py
```

Clone the role repository and create symbolic link
```bash
git clone https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent.git
ln -s ansible-role-amazon-cloudwatch-agent amazon-cloudwatch-agent
cd ansible-role-amazon-cloudwatch-agent
```

Execute the test
```bash
molecule test
```

Additionally if you want to test using VMs, I have a very nice [ansible-playground project](https://github.com/christiangda/ansible-playground) that use Vagrant and VirtualBox, try it!.

License
-------

This module is released under the GNU General Public License Version 3:

* [http://www.gnu.org/licenses/gpl-3.0-standalone.html](http://www.gnu.org/licenses/gpl-3.0-standalone.html)

Author Information
------------------

* [Christian González](https://github.com/christiangda)
