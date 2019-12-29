# Ansible Role: christiangda.amazon_cloudwatch_agent

[![Build Status](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent.svg?branch=master)](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)
[![Ansible Role](https://img.shields.io/ansible/role/39191.svg)](https://galaxy.ansible.com/christiangda/amazon_cloudwatch_agent)

This role [Install AWS CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html)

**Features:**

* Downloads and installs CloudWatch Agent from [AWS distribution package](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/verify-CloudWatch-Agent-Package-Signature.html)
* Reload service when configuration is changed
* Provide a default agent configuration file (a minimal configuration, does not recommended)
* Rotate CloudWatch Agent Log file
* **Allow you to load you own [JSON file](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html) or YAML file or INLINE configuration for agent, metrics and logs sections***

## Requirements

This role work on RedHat, CentOS, Amazon Linux, Debian and Ubuntu distributions

* RedHat
  * 6
  * 7
  * 8
* CentOS
  * 6
  * 7
  * 8
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

To see the compatibility matrix of Python vs. Ansible see the project [Travis-CI build matrix](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)

**RedHat Notes:**

Is important to have a valid subscription in order to avoid dependencies packages installation problems.

## Role Variables

```yaml
# possible values:
# - "{{ lookup('file', 'files/your-cloudwatch-config.json') | from_json }}" where your-cloudwatch-config.json is your custom
#   configuration file according to docs reference.
# - "{{ lookup('file', 'files/your-cloudwatch-config.yaml') | from_yaml }}" where your-cloudwatch-config.yaml is your custom
#   configuration file according to docs reference.
#
# Also is possible to define inline configuration as YAML
#    cwa_conf_json_file_content:
#      metrics:
#        append_dimensions:
#          AutoScalingGroupName: "${!aws:AutoScalingGroupName}"
#          ImageId: "${!aws:ImageId}"
#          InstanceId: "${!aws:InstanceId}"
#          InstanceType: "${!aws:InstanceType}"
#        metrics_collected:
#          mem:
#            measurement:
#              - mem_used_percent
#          swap:
#            measurement:
#              - swap_used_percent
#
# reference: https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html
# default value: ""
# notes:
# * when is empty the role deploy a custom json configuration via template
cwa_conf_json_file_content: ""
```

```yaml
# possible values:
# - "ec2"
# - "onPremise"
# default value: "ec2"
# notes:
# * not necessary when you deploy the agent into AWS, default value is fine.
# * when you set the value 'onPremise' is because you installed the agent outside AWS, so is necessary to set the variables "cwa_aws_region", "cwa_access_key", "cwa_secret_key" also
cwa_agent_mode: "ec2"
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/general/latest/gr/rande.html
# default value: "eu-west-1"
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_aws_region: "eu-west-1"
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: "AmazonCloudWatchAgent"
# notes:
# * Only necessary when use **cwa_agent_mode:** "onPremise", you could use other profile if it is configured properly
cwa_profile: "AmazonCloudWatchAgent"
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html
# - https://docs.ansible.com/ansible/latest/user_guide/vault.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_access_key: ""
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html
# - https://docs.ansible.com/ansible/latest/user_guide/vault.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_secret_key: ""
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: ""
cwa_http_proxy: ""
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: ""
cwa_https_proxy: ""
```

```yaml
# possible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: "169.254.169.254"
# * Always disable proxy for aws metadata ip (169.254.169.254)
cwa_no_proxy: "169.254.169.254"
```

```yaml
# possible values:
# - https://linux.die.net/man/8/logrotate
# default value: "10M"
cwa_logrotate_file_size: "10M"
```

```yaml
# possible values:
# - https://linux.die.net/man/8/logrotate
# default value: 5
cwa_logrotate_files: 5
```

```yaml
# Do we use the christiangda.epel_repo or manage it ourselves?
# - https://galaxy.ansible.com/christiangda/epel_repo
# possible values:
# default value: true
cwa_use_epel_role: true
```

## Dependencies

* [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) imported dynamically in the code when is used with Red Hat family, so you don't need to add this to your ansible playbook.

## Example Playbook

### RedHat/CentOS, Ubuntu and Debian

Reading config file from JSON configuration file

```yaml
- hosts: servers
    gather_facts: True
    roles:
    - role: christiangda.amazon_cloudwatch_agent
        vars:
            cwa_agent_mode: "ec2"
            cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.json') | from_json }}"
```

Reading config file from YAML configuration file

```yaml
- hosts: servers
    gather_facts: True
    roles:
    - role: christiangda.amazon_cloudwatch_agent
        vars:
            cwa_agent_mode: "ec2"
            cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.yaml') | from_yaml }}"
```

Using INLINE YAML configuration file

```yaml
---
- hosts: centos7, centos6, ubuntu1804, ubuntu1810, debian8, debian9, amzn2
  become: True
  roles:
    - role: christiangda.amazon_cloudwatch_agent
      vars:
        cwa_agent_mode: onPremise
        cwa_aws_region: "eu-west-1"
        cwa_access_key: "AKIAIOSFODNN7EXAMPLE"
        cwa_secret_key: "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
        #cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.json') | from_json }}"
        cwa_conf_json_file_content:
          agent:
            metrics_collection_interval: 60
            region: es-west-1
            logfile: "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log"
            debug: false
          metrics:
            metrics_collected:
              collectd: {}
              cpu:
                resources:
                  - "*"
                measurement:
                  - name: cpu_usage_idle
                    rename: CPU_USAGE_IDLE
                    unit: Percent
                  - name: cpu_usage_nice
                    unit: Percent
                  - cpu_usage_guest
                totalcpu: false
                metrics_collection_interval: 10
                append_dimensions:
                  test: test1
                  date: "2017-10-01"
              netstat:
                measurement:
                  - tcp_established
                  - tcp_syn_sent
                  - tcp_close
                metrics_collection_interval: 60
              processes:
                measurement:
                  - running
                  - sleeping
                  - dead
            append_dimensions:
              ImageId: "${!aws:ImageId}"
              InstanceId: "${!aws:InstanceId}"
              InstanceType: "${!aws:InstanceType}"
              AutoScalingGroupName: "${!aws:AutoScalingGroupName}"
            aggregation_dimensions:
              - - AutoScalingGroupName
              - - InstanceId
                - InstanceType
              - []
          logs:
            logs_collected:
              files:
                collect_list:
                  - file_path: "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log"
                    log_group_name: amazon-cloudwatch-agent.log
                    log_stream_name: amazon-cloudwatch-agent.log
                    timezone: UTC
                  - file_path: "/opt/aws/amazon-cloudwatch-agent/logs/test.log"
                    log_group_name: test.log
                    log_stream_name: test.log
                    timezone: Local
            log_stream_name: my_log_stream_name
            force_flush_interval: 15
```

### Amazon Linux 1/2 (my-playbook.yml)

```yaml
- hosts: all
    gather_facts: True
    become: true
    become_user: root
    become_method: sudo
    remote_user: ec2-user
    roles:
    - role: christiangda.amazon_cloudwatch_agent
        vars:
            cwa_agent_mode: "ec2"
            cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.json') | from_json }}"
```

Inventory file sample (inventory)

```ini
[all]
10.14.x.y
10.14.v.z

[amazon-1]
10.14.x.y

[amazon-2]
10.14.v.z
```

How to used it

```bash
ansible-playbook my-playbook.yml \
    --inventory inventory \
    --private-key [~/location of my key.pem] \
    --become \
    --become-user=ec2-user \
    --user ec2-user
```

### Variables examples

```yaml
#cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.json') | from_json }}"
cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.yaml') | from_yaml }}"

cwa_agent_mode: "onPremise"
cwa_aws_region: "eu-west-1"

# You should use ansible-vault to provision it
# Example:
# ansible-vault encrypt_string --ask-vault-pass --name 'cwa_access_key' 'AKIAIOSFODNN7EXAMPLE'
# ansible-vault encrypt_string --ask-vault-pass --name 'cwa_secret_key' 'wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY'
cwa_access_key: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          30376338613338326663373366303234623665633339303338613463313564633832363237306137
          3362643039616631323339383332306536333962346133310a383265376665316235653261616136
          61306566623531356263346439633761633830323636646236373736353530396134636536666532
          3939636433636364310a316639366139366566623337623536346661633339343766323936346336
          65333035366635396138656132643262626438333961326266396466626464643766
cwa_secret_key: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          65643230613939303737336632346432393234616437383532386139616364316233333933643735
          6633636261383163323362623562323333323533336564310a323030343431366135343035326635
          33623161376634643939636464306139386662303034616531346632303039643238373834616266
          3064623232373233610a346432646565396235316631626137653731376365333531323866626665
          62656638623330643539653763636364363738653932653831316238633939356462653636633463
          6130613761633565616533633332376565373062396565396261
```

## Development / Contributing

This role is tested using [Molecule](https://molecule.readthedocs.io/en/latest/) and was developed using
[Python Virtual Environments](https://docs.python.org/3/tutorial/venv.html)

Prepare your environment

* Python 3

```bash
mkdir ansible-roles
cd ansible-roles/

python3 -m venv venv
source venv/bin/activate
pip install pip --upgrade
pip install ansible
pip install molecule
pip install molecule[vagrant]
pip install selinux
pip install docker
pip install pytest
pip install pytest-mock
pip install pylint
pip install rope
pip install autopep8
pip install yamllint
pip install flake8
```

* Python 2.7

Dependencies (based on Fedora)

```bash
sudo dnf install redhat-rpm-config
sudo dnf install python-devel
sudo dnf install libselinux-python
```

```bash
mkdir ansible-roles
cd ansible-roles/

python2.7 -m virtualenv venv
source venv/bin/activate
pip install pip --upgrade
pip install ansible
pip install molecule">=2.22rc1"
pip install molecule[vagrant]
pip install selinux
pip install docker
pip install pytest
pip install pytest-mock
pip install pylint
pip install rope
pip install autopep8
pip install yamllint
pip install flake8
```

Clone the role repository and create symbolic link

```bash
git clone https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent.git
ln -s ansible-role-amazon-cloudwatch-agent christiangda.amazon_cloudwatch_agent
cd christiangda.amazon_cloudwatch_agent
```

Execute the test

Using docker in local

```bash
molecule test [--scenario-name default]
```

Using vagrant in local

```bash
molecule create --scenario-name vagrant
molecule converge --scenario-name vagrant
molecule verify --scenario-name vagrant
```

or

```bash
molecule test --scenario-name vagrant
```

**Additionally if you want to test it using VMs, I have a very nice [ansible-playground project](https://github.com/christiangda/ansible-playground) that use Vagrant and VirtualBox, try it!.**

## License

This module is released under the GNU General Public License Version 3:

* [http://www.gnu.org/licenses/gpl-3.0-standalone.html](http://www.gnu.org/licenses/gpl-3.0-standalone.html)

## Author Information

* [Christian González Di Antonio](https://github.com/christiangda)
