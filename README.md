# Ansible Role: christiangda.amazon_cloudwatch_agent

[![Build Status](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent.svg?branch=master)](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)
[![Ansible Role](https://img.shields.io/ansible/role/39191.svg)](https://galaxy.ansible.com/christiangda/amazon_cloudwatch_agent)

This role [Install AWS CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html)

The best way to install this role is using the command `ansible-galaxy install christiangda.amazon_cloudwatch_agent`, the Ansible Galaxy repository is [christiangda.amazon_cloudwatch_agent](https://galaxy.ansible.com/christiangda/amazon_cloudwatch_agent)

The repository code is [https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent)

**Features:**

* Works on [AWS EC2 instances](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance-fleet.html)/[On-Premise Instances](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html)
* Support different versions of Python, Ansible and Operating Systems (see the [Continuos integration matrix for details](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent))
* Downloads and installs CloudWatch Agent from [AWS distribution package](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/verify-CloudWatch-Agent-Package-Signature.html)
* Reload service when configuration is changed
* Provides a default [agent configuration](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html) file (a minimal configuration, It is not recommended)
* Rotate CloudWatch Agent Log file
* **Allow you to load your own [JSON file](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html) / YAML file or INLINE configuration for agent, metrics and logs sections**

**Notes:**

* The version **2**.x.y is not compatible with version **1**.x.y
* Since version **2**.x.y this role no longer installs [EPEL Repository](https://fedoraproject.org/wiki/EPEL) by default, now you need to take care of this, I recommend my `ansible role` [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) instead
* Since version **2**.x.y this role no longer creates [AWS CLI profile (config and credentials)](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html), now you need to take care of this, I recommend my `ansible roles` [christiangda.awscli](https://galaxy.ansible.com/christiangda/awscli) and [christiangda.awscli_configure](https://galaxy.ansible.com/christiangda/awscli_configure) instead.  See examples under (when cwa_agent_mode: "onPremise")
* Installs automatically the `collectd OS package` when detecting the use of [Retrieve Custom Metrics with collectd](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html) in the configuration file. Depending on the `OS` the `collectd package` could need [EPEL Repository](https://fedoraproject.org/wiki/EPEL)
* **RedHat users** It is important to have a valid subscription in order to avoid dependencies packages installation problems.

More details in [VERSION.md](VERSION.md)

## Requirements

This role work on RedHat, CentOS, Amazon Linux, Debian and Ubuntu distributions

* RedHat
  * 7
  * 8
* CentOS
  * 7
  * 8
* Amazon Linux
  * 2
* Ubuntu
  * 18.04
  * 20-04
* Debian
  * buster (10)
  * sid (unstable)

To see the compatibility matrix of Python vs. Ansible versions see [Travis-CI build matrix](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)

## Role Variables

| Variable                   | Default Value           |
| :------------------------- | :---------------------- |
| cwa_conf_json_file_content | "" --> Empty            |
| cwa_agent_mode             | "ec2"                   |
| cwa_aws_region             | ""                      |
| cwa_use_credentials        | false                   |
| cwa_profile                | "AmazonCloudWatchAgent" |
| cwa_agent_profile_path     | /root                   |
| cwa_http_proxy             | "" --> Empty            |
| cwa_https_proxy            | "" --> Empty            |
| cwa_no_proxy               | "169.254.169.254"       |
| cwa_logrotate_file_size    | "10M"                   |
| cwa_logrotate_files        | 5                       |

**More Details:** See the file [defaults/main.yaml](defaults/main.yaml)

## Dependencies

* In case of OS Family RedHat/Centos [EPEL Repository](https://fedoraproject.org/wiki/EPEL) could be necessary
* If you set `cwa_agent_mode: "onPremise"` the [AWS CLI Profile](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) configuration is needed
* If you [Retrieve Custom Metrics with collectd](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html) the package `collectd` will installed automatically and depending on the `OS` the [EPEL Repository](https://fedoraproject.org/wiki/EPEL) could be necessary

## Example Playbook

### When cwa_agent_mode: "onPremise"

```yaml
# You should use ansible-vault to provision it
# Example:
# ansible-vault encrypt_string --ask-vault-pass --name 'cwa_access_key' 'AKIAIOSFODNN7EXAMPLE'
# ansible-vault encrypt_string --ask-vault-pass --name 'cwa_secret_key' 'wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY'

- hosts: servers
    gather_facts: True
    roles:
      - role: christiangda.epel_repo # If you don't have installed EPEL Repository
        when: >
          ansible_os_family == 'RedHat' and (
            ansible_distribution == 'CentOS' or
            ansible_distribution == 'RedHat' or
            ansible_distribution == 'Amazon'
          )
      - role: christiangda.awscli_configure # If you don't have configured AWS CLI Profiles
        vars:
          awscliconf_path: '/root'
          awscliconf_files:
            credentials:
              - AmazonCloudWatchAgent:
                  aws_access_key_id: !vault |
                    $ANSIBLE_VAULT;1.1;AES256
                    30376338613338326663373366303234623665633339303338613463313564633832363237306137
                    3362643039616631323339383332306536333962346133310a383265376665316235653261616136
                    61306566623531356263346439633761633830323636646236373736353530396134636536666532
                    3939636433636364310a316639366139366566623337623536346661633339343766323936346336
                    65333035366635396138656132643262626438333961326266396466626464643766
                  aws_secret_access_key: !vault |
                    $ANSIBLE_VAULT;1.1;AES256
                    65643230613939303737336632346432393234616437383532386139616364316233333933643735
                    6633636261383163323362623562323333323533336564310a323030343431366135343035326635
                    33623161376634643939636464306139386662303034616531346632303039643238373834616266
                    3064623232373233610a346432646565396235316631626137653731376365333531323866626665
                    62656638623330643539653763636364363738653932653831316238633939356462653636633463
                    6130613761633565616533633332376565373062396565396261
            config:
              - profile AmazonCloudWatchAgent:
                  region: "eu-west-1"
      - role: christiangda.amazon_cloudwatch_agent # Using minimal agent configuration provided by the role
          vars:
              cwa_agent_mode: "onPremise"
              cwa_aws_region: "eu-west-1"
              cwa_profile: "AmazonCloudWatchAgent"
```

### When cwa_agent_mode: "ec2"

Reading config file from JSON configuration file

```yaml
---
- hosts: servers
    gather_facts: True
    roles:
      - role: christiangda.amazon_cloudwatch_agent # Using a JSON file provided by you at location `files/CloudWatch.json` relative to this playbook
          vars:
              cwa_agent_mode: "ec2"
              cwa_conf_json_file_content: "{{ lookup('file', 'files/CloudWatch.json') | from_json }}"
```

```yaml
---
- hosts: servers
    gather_facts: True
    roles:
      - role: christiangda.epel_repo
        when: >
          ansible_os_family == 'RedHat' and (
            ansible_distribution == 'CentOS' or
            ansible_distribution == 'RedHat' or
            ansible_distribution == 'Amazon'
          )
      - role: christiangda.awscli_configure
        vars:
          awscliconf_path: '/root'
          awscliconf_files:
            credentials:
              - AmazonCloudWatchAgent:
                  aws_access_key_id: 'AKIAIOSFODNN7EXAMPLE'
                  aws_secret_access_key: 'wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY'
      - role: christiangda.amazon_cloudwatch_agent # Using minimal agent configuration provided by the role
          vars:
              cwa_agent_mode: "ec2"
```

Reading config file from YAML configuration file

```yaml
---
- hosts: servers
    gather_facts: True
    roles:
      - role: christiangda.amazon_cloudwatch_agent # Using a YAML file provided by you at location `files/CloudWatch.yaml` relative to this playbook
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
    - role: christiangda.epel_repo # If you don't have installed EPEL Repository
        when: >
          ansible_os_family == 'RedHat' and (
            ansible_distribution == 'CentOS' or
            ansible_distribution == 'RedHat' or
            ansible_distribution == 'Amazon'
          )
      - role: christiangda.awscli_configure # If you don't have configure AWS CLI Profiles
        vars:
          awscliconf_path: '/root'
          awscliconf_files:
            credentials:
              - AmazonCloudWatchAgent:
                  aws_access_key_id: !vault |
                    $ANSIBLE_VAULT;1.1;AES256
                    30376338613338326663373366303234623665633339303338613463313564633832363237306137
                    3362643039616631323339383332306536333962346133310a383265376665316235653261616136
                    61306566623531356263346439633761633830323636646236373736353530396134636536666532
                    3939636433636364310a316639366139366566623337623536346661633339343766323936346336
                    65333035366635396138656132643262626438333961326266396466626464643766
                  aws_secret_access_key: !vault |
                    $ANSIBLE_VAULT;1.1;AES256
                    65643230613939303737336632346432393234616437383532386139616364316233333933643735
                    6633636261383163323362623562323333323533336564310a323030343431366135343035326635
                    33623161376634643939636464306139386662303034616531346632303039643238373834616266
                    3064623232373233610a346432646565396235316631626137653731376365333531323866626665
                    62656638623330643539653763636364363738653932653831316238633939356462653636633463
                    6130613761633565616533633332376565373062396565396261
            config:
              - profile AmazonCloudWatchAgent:
                  region: "eu-west-1"
    - role: christiangda.amazon_cloudwatch_agent # Using ONLINE YAML configuration for your agent
      vars:
        cwa_agent_mode: onPremise
        cwa_aws_region: "eu-west-1"
        cwa_profile: "AmazonCloudWatchAgent"
        cwa_conf_json_file_content:
          agent:
            metrics_collection_interval: 60
            region: es-west-1
            logfile: "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log"
            debug: false
          metrics:
            metrics_collected:
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
              ImageId: "${aws:ImageId}"
              InstanceId: "${aws:InstanceId}"
              InstanceType: "${aws:InstanceType}"
              AutoScalingGroupName: "${aws:AutoScalingGroupName}"
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

Minimal configuration on AWS EC2 instance

```yaml
---
- hosts: all
    gather_facts: True
    become: true
    become_user: root
    become_method: sudo
    remote_user: ec2-user
    roles:
    - role: christiangda.amazon_cloudwatch_agent # Assuming you are on "EC2 instance" and the default agent configuration from role
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

## Development / Contributing

This role is tested using [Molecule](https://molecule.readthedocs.io/en/latest/) and was developed using
[Python Virtual Environments](https://docs.python.org/3/tutorial/venv.html)

Also, we used to main git branch

* master
* develop

If you want to contribute to this project what you want to do is

* [Fork the project](https://help.github.com/en/github/getting-started-with-github/fork-a-repo)
* [Prepare your environment](#prepare-your-environment)
* Fix the problem in `develop` branch
* Execute `molecule test`
* Create a Pull Request to official project `develop` branch

References

* [Fork a repo](https://help.github.com/en/github/getting-started-with-github/fork-a-repo)
* [Creating a pull request from a fork](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork)

### Prepare your environment

* Python 3

```bash
mkdir ansible-roles
cd ansible-roles/

python3 -m venv venv
source venv/bin/activate
pip install pip --upgrade
pip install ansible
pip install molecule
pip install 'molecule[docker]'
pip install 'molecule[podman]'
pip install 'molecule[lint]'
pip install molecule-vagrant
pip install python-vagrant
pip install selinux
pip install docker
pip install pytest
pip install pytest-mock
pip install pylint
pip install rope
pip install autopep8
pip install yamllint
pip install flake8
pip install ansible-lint
```

### Clone the role repository (From your fork) and create symbolic link

NOTE:  Fork the main repository first

```bash
git clone https://github.com/<your github user>/ansible-role-amazon-cloudwatch-agent.git
ln -s ansible-role-amazon-cloudwatch-agent christiangda.amazon_cloudwatch_agent
cd christiangda.amazon_cloudwatch_agent
```

### Execute the molecule test

Scenearios availables:
* default --> `--driver-name docker`
* podman --> `--driver-name podman`
* vagrant --> `--driver-name vagrant`

#### scenario default

Step by step

```bash
molecule create [--scenario-name default]
molecule converge [--scenario-name default]
molecule verify [--scenario-name default]
molecule destroy [--scenario-name default]
```

or

All in one

```bash
molecule test [--scenario-name default]
```

#### scenario podman

Step by step

```bash
molecule create --scenario-name podman
molecule converge --scenario-name podman
molecule verify --scenario-name podman
molecule destroy --scenario-name podman
```

or

All in one

```bash
molecule test --scenario-name podman
```

#### scenario vagrant

Step by step

```bash
molecule create --scenario-name vagrant
molecule converge --scenario-name vagrant
molecule verify --scenario-name vagrant
molecule destroy --scenario-name vagrant
```

or

All in one

```bash
molecule test --scenario-name vagrant
```

**Additionally, if you want to test it using VMs, I have a very nice [ansible-playground project](https://github.com/christiangda/ansible-playground) that use Vagrant and VirtualBox, try it!.**

## License

This module is released under the GNU General Public License Version 3:

* [http://www.gnu.org/licenses/gpl-3.0-standalone.html](http://www.gnu.org/licenses/gpl-3.0-standalone.html)

## Author Information

* [Christian González Di Antonio](https://github.com/christiangda)
