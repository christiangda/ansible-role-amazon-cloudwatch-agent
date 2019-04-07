# Ansible Role: christiangda.amazon-cloudwatch-agent

[![Build Status](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent.svg?branch=master)](https://travis-ci.org/christiangda/ansible-role-amazon-cloudwatch-agent)
[![Ansible Role](https://img.shields.io/ansible/role/39191.svg)](https://galaxy.ansible.com/christiangda/amazon_cloudwatch_agent)

This role [Install CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html)

**My apologies, this is a WIP**

This version only:
* Download and install CloudWatch Agent from AWS OS Package
* Has handlers to reload configuration
* Has a default agent configuration file
* Rotate the CloudWatch Agent Log
* ~~Load my own configuration file~~

## Requirements

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

## Role Variables

```yaml
# posible values:
# - "ec2"
# - "onPremise"
# default value: "ec2"
# notes:
# * not necessary when you deploy the agent into AWS, default value is fine.
# * when you set the value 'onPremise' is because you installed the agent outside AWS, so is necessary to set the variables "cwa_aws_region", "cwa_access_key", "cwa_secret_key" also
cwa_agent_mode: "ec2"
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/general/latest/gr/rande.html
# default value: "eu-west-1"
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_aws_region: "eu-west-1"
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: "AmazonCloudWatchAgent"
# notes:
# * Only necessary when use **cwa_agent_mode:** "onPremise", you could use other profile if it is configured properly
cwa_profile: "AmazonCloudWatchAgent"
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html
# - https://docs.ansible.com/ansible/latest/user_guide/vault.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_access_key: ""
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html
# - https://docs.ansible.com/ansible/latest/user_guide/vault.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_secret_key: ""
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_http_proxy: ""
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_https_proxy: ""
```

```yaml
# posible values:
# - https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html
# default value: ""
# notes:
# * This is the region where the agent have access to push logs/metrics, only necessary when use **cwa_agent_mode:** "onPremise"
cwa_no_proxy: ""
```

```yaml
# posible values:
# - https://linux.die.net/man/8/logrotate
# default value: "10M"
cwa_logrotate_file_size: "10M"
```

```yaml
# posible values:
# - https://linux.die.net/man/8/logrotate
# default value: 5
cwa_logrotate_files: 5
```

## Dependencies

* [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) imported dynamically in the code when is used with RedHat family, so you don't need to add this to your ansible playbook

## Example Playbook

for RedHat/CentOS 6/7

```yaml
- hosts: servers
    gather_facts: True
    roles:
    - role: christiangda.amazon-cloudwatch-agent
        vars:
        cwa_agent_mode: "ec2"
```

for Amazon Linux 1/2 (my-playbook.yml)

```yaml
- hosts: all
    gather_facts: True
    become: true
    become_user: root
    become_method: sudo
    remote_user: ec2-user
    roles:
    - role: christiangda.amazon-cloudwatch-agent
        vars:
        cwa_agent_mode: "ec2"
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

Variables examples:

```yaml
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

## License

This module is released under the GNU General Public License Version 3:

* [http://www.gnu.org/licenses/gpl-3.0-standalone.html](http://www.gnu.org/licenses/gpl-3.0-standalone.html)

## Author Information

* [Christian González Di Antonio](https://github.com/christiangda)
