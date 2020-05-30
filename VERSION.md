# VERSION Details

## 2.0.6

* Improved the use of the `cwa_aws_region` Variable, resolve issue #24[Leaving the Region Value out of amazon-cloudwatch-agent.json #24](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/issues/24)
* Now the variable `cwa_aws_region` is empty by default, so you need to take care to define it when are you are in OnPremise

## 2.0.5

* Fix collectd search failed #23 [ubuntu: fix gpg signature download](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/pull/23)
* Refactored to support molecule 3.x
* Documentation improved

## 2.0.4

* Fixed ubuntu: fix gpg signature download #18 [ubuntu: fix gpg signature download](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/pull/18)

## 2.0.3

* Fixed variable problem reporte in issue #14 [cwa_use_credentials: false is not working for role based installation](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/issues/14)
* Debian Family now detect automatically the installation of collectd dependency

## 2.0.2

* Improved to support ansible playbooks with option `--check` reporte in issue #8 [playbook fails on --check](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/issues/8)

## 2.0.1

* Remove the need to install collectd package when you are not [Retrieve Custom Metrics with collectd](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html), reported in issue #7 --> [collectd is not a required dependency](https://github.com/christiangda/ansible-role-amazon-cloudwatch-agent/issues/7)

## 2.0.0

* The version 2.x.y is not compatible with version 1.x.y
* Since version 2.x.y this role no longer installs [EPEL Repository](https://fedoraproject.org/wiki/EPEL) by default, now you need to take care of this, I recommend my role [christiangda.epel_repo](https://galaxy.ansible.com/christiangda/epel_repo) instead
* Since version 2.x.y this role no longer creates [AWS CLI profile (config and credentials)](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html), now you need to take care of this, I recommend my roles [christiangda.awscli](https://galaxy.ansible.com/christiangda/awscli) and [christiangda.awscli_configure](https://galaxy.ansible.com/christiangda/awscli_configure) instead.  See examples under (when cwa_agent_mode: "onPremise")
* Refactored to remove the oldest version of many Operating System on molecule test
* Refactored to remove the oldest version of Ansible on Ansible galaxy meta manifest, now support ansible >= 2.7
