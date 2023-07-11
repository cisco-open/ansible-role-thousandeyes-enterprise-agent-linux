# Ansible Role: ansible-role-thousandeyes-enterprise-agent-linux

Ansible role for Cisco ThousandEyes Enterprise Agent deployment.

## Description

Mass deployment and configuration of Linux package ThousandEyes Enterprise Agent via Ansible role. 
This Ansible role is automating the manual installation method described on the [Enterprise Agent Deployment Using Linux Package Method] Knowledge Base article.
The tasks in this Ansible role are inheriting the logic from the official [shell installation script] and various [Knowledge Base] articles.

## Requirements

- Ansible 2.9
- Supported hosts OS: Ubuntu 20.04 LTS, CentOS 7.9, RHEL 7.9/8, Oracle Linux 7.9/8, Amazon Linux 2. Although not mandatory, all OS minimal server images are preferred.

## Installation

Clone the repository into your Ansible roles folder location:

```bash

git clone https://github.com/cisco-open/ansible-role-thousandeyes-enterprise-agent-linux.git /path/to/ansible/roles

```

## Role Variables

`Before running this role, please review and set the appropriate variables. E.g. vars/main.yml`

INSTALL_LOG:

> If you save Ansible output to a log, you expose any secret data in your Ansible output, such as passwords and user names.
> By default Ansible sends output about plays, tasks, and module arguments to your screen (STDOUT) on the control node.
> If you want to capture Ansible output in a log, you have three options.
> Visit [Logging Ansible output] for more details.

|Key|Type|Description|Default|Possible values|Notes|
|--- |--- |--- |--- |--- |--- |
|ACCOUNT_TOKEN|String|Account Group for Enterprise Agent|`<account_token>`| 32 chars AG Token|[Where can I get the Account Group Token?]|
|CRASH_REPORTS|String|Enables or disables crash reports|`1`|`0`,`1`|[Crash Reporting]|
|INSTALL_BROWSERBOT|String|Installs Browserbot|`yes`|`yes`,`no`|[What is BrowserBot?]|
|INSTALL_NTP|String|Installs NTP service|`no`|`yes`(recommended),`no`|[Enterprise Agent Deployment post-install]|
|INSTALL_NTP_SERVER_CUSTOM1|String|Custom NTP server/pool 1|`""`|`IP`,`hostname`|Read vars/main.yml comments|
|INSTALL_NTP_SERVER_CUSTOM2|String|Custom NTP server/pool 2|`""`|`IP`,`hostname`|Read vars/main.yml comments|
|INSTALL_OS_UPDATES|String|Install operating system (security) updates|`no`|`yes`(recommended),`no`|
|PROXY_TYPE|String|Proxy settings for ThousandEyes Agent|`DIRECT`|`DIRECT`,`STATIC`,`PAC`|[Configuring a Proxy Server]|
|PROXY_LOCATION|String|HTTP proxy|`""`|`IP`,`hostname`,`PAC location`|[Configuring a Proxy Server]|
|PROXY_USER|String|Proxy username|`""`|Valid proxy credentials|[Configuring a Proxy Server]|
|PROXY_PASS|String|Proxy password|`""`|Valid proxy credentials|[Configuring a Proxy Server]|
|PROXY_BYPASS_LIST|String|Proxy bypass list|`""`|`*.example.com;192.168.1.0/24`|[Configuring a Proxy Server]|
|PROXY_AUTH_TYPE|String|Proxy authentication type|`""`|`BASIC`,`NTLM`,`KERBEROS`|[Configuring a Proxy Server]|
|KDC_USER|String|Kerberos username|`""`|Valid Kerberos credentials|
|KDC_PASS|String|Kerberos password|`""`|Valid Kerberos credentials|
|KDC_REALM|String|Kerberos realm|`""`|Kerberos realm|
|KDC_HOST|String|Kerberos Key Distribution Center (KDC) Host|`""`|
|KDC_PORT|String|Kerberos port|`88`|Other port number|
|KERBEROS_RDNS|String|Disable reverse DNS lookup in Kerberos auth|`1`|`0`,`1`|[Kerberos RDNS configuration]|
|KERBEROS_WHITELIST|String|Kerberos whitelist|`""`|IP address range or subnet
|APT_PROXY_HOSTNAME|String|APT Proxy hostname|`""`|`IP`,`hostname`|[Proxy settings for apt package manager]|
|APT_PROXY_PORT|String|APT Proxy port|`80`|Other port number|
|APT_PROXY_USERNAME|String|APT Proxy username|`""`|Valid proxy credentials|
|APT_PROXY_PASSWORD|String|APT Proxy password|`""`|Valid proxy credentials|
|YUM_PROXY_HOSTNAME|String|YUM Proxy hostname|`""`|`IP`,`hostname`|[Proxy settings for yum package manager]|
|YUM_PROXY_PORT|String|YUM Proxy port|`80`|Other port number|
|YUM_PROXY_USERNAME|String|YUM Proxy username|`""`|Valid proxy credentials|
|YUM_PROXY_PASSWORD|String|YUM Proxy password|`""`|Valid proxy credentials|
|REDHAT_REPOSITORY|String|Repository for RPM|`https://yum.thousandeyes.com`|Repository URL|
|DEBIAN_REPOSITORY|String|Repository for DEB|`https://apt.thousandeyes.com/`|Repository URL|
|SKIP_REPO_CREATION|String|Skip the repository creation|`no`|`yes`,`no`|
|SKIP_ORACLE_OPTIONAL_REPO|String|Skip adding the Oracle Linux 7 Addons repository|`no`|`yes`,`no`|[Oracle Linux 7 Addons repository]|
|RHN_REGISTER|String|Register RHEL with RedHat Network|`yes`|`yes`,`no`|[Register RHEL with RedHat Network]|
|SKIP_REDHAT_OPTIONAL_REPO|String|Skip rhel-7-server-extras-rpms repository|`no`|`yes`,`no`|[RHEL 7 Extras repository]|
|RHN_USERNAME|String|RedHat Network username|`""`|RHN username
|RHN_PASSWORD|String|RedHat Network password|`""`|RHN password
|RHN_POOL_ID1|String|Subscribe to a specific pool by ID|`""`|RHN Pool ID
|TEAGENT_LOG|String|log path for te-agent|`/var/log`|Valid path for te-agent log files
|MEM_AGENT_BBOT|Integer|Check recommended memory with Browserbot|`2048`|Integer greater than recommended value|[Enterprise Agent hardware requirements]|
|MEM_AGENT|Integer|Check recommended memory Agent only|`1024`|Integer greater than recommended value|[Enterprise Agent hardware requirements]|
|CPU_CORE_COUNT|Integer|Check recommended CPU count|`2`|Integer greater than recommended value|[Enterprise Agent hardware requirements]|
|SKIP_DESKTOP_CHECK|String|Skip desktop OS software checks|`no`|`yes`,`no`|Skip desktop OS detection|
|START_SERVICE|String|Start te-agent service|`yes`|`yes`,`no`|Delay registering the agent until next restart|

## Example Playbook

install_thousandeyes.yml
```yml
---
- hosts: allagents
  remote_user: ansible
  gather_facts: no
# Uncommenting this line allows each host to run until the end of the play as fast as it can
# strategy: free
  pre_tasks:
    - setup:
        gather_subset:
          - '!all'
          - '!any'
          - hardware
          - virtual
  become: true
  roles:
#    - common
#    - rhel-system-roles.timesync
    - ansible-role-thousandeyes-enterprise-agent-linux
#    - other-roles
```
## Example using Ansible Vault
vars/main.yml contains sensitive information like Account Group token or credentials.

The following example will encrypt the variables file with the password in vault_password_file
```bash
ansible-vault encrypt --vault-id thousandeyes@vault_password_file vars/main.yml
ansible-playbook install_thousandeyes.yml --vault-id thousandeyes@vault_password_file
```
More details:
- [Ansible Vault]
- [Using Vault in playbooks]

### Todos
The following are work in progress new features :
-  [Add CA root certificates to system certificate store](https://docs.thousandeyes.com/product-documentation/enterprise-agents/installing-ca-certificates-on-enterprise-agents)
-  [Add CA root certificates to Browserbot](https://docs.thousandeyes.com/product-documentation/enterprise-agents/installing-ca-certificates-on-enterprise-agents)

## Contributing to ansible-role-thousandeyes-enterprise-agent-linux
This project is maintained by the ThousandEyes Customer Engineering team and accepts community contributions.
If you have a bug or an idea, browse the open issues before [opening a new issue](https://github.com/cisco-open/ansible-role-thousandeyes-enterprise-agent-linux/issues).

### Write bug reports with detail, background, and sample code
Please consider to include the following in a bug report:

- A quick summary and/or background
- Steps to reproduce
  - Be specific!
  - Give sample code if you can.
- What you expected would happen
- What actually happened
- Notes (possibly including why you think this might be happening, or stuff you tried that didn't work)

### Any contributions you make will be under the Apache License, Version 2
By contributing, you agree that your contributions will be licensed under its Apache License, Version 2.
In short, when you submit code changes, your submissions are understood to be under the same [Apache License](LICENSE) that covers the project.
Feel free to contact the maintainers if that's a concern.

## License
Copyright (c) 2023 Cisco Systems, Inc. and its affiliates
All rights reserved.

[Enterprise Agent Deployment Using Linux Package Method]: <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/enterprise-agent-deployment-using-linux-package-method>

[shell installation script]: <https://downloads.thousandeyes.com/agent/install_thousandeyes.sh>

[Knowledge Base]: <https://docs.thousandeyes.com>

[Where can I get the Account Group Token?]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/where-can-i-get-the-account-group-token>

[Crash Reporting]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/crash-reporting-for-enterprise-agents>

[What is BrowserBot?]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/what-is-browserbot>

[Enterprise Agent Deployment post-install]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/enterprise-agent-deployment-using-linux-package-method#post-installation>

[Kerberos RDNS configuration]: <https://docs.thousandeyes.com/release-notes/2019/2019-08-20-release-notes#kerberos-rdns-configuration>

[Proxy settings for apt package manager]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/configuring-an-enterprise-agent-to-use-a-proxy-server#configuring-proxy-settings-for-the-apt-package-manager-on-ubuntu>

[Proxy settings for yum package manager]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/configuring-an-enterprise-agent-to-use-a-proxy-server#configuring-proxy-settings-for-the-yum-package-manager-on-rhel-centos-oracle-linux>

[Oracle Linux Addons repository]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/install-the-enterprise-agent-with-browserbot-on-oracle-linux-server-7>

[Register RHEL with RedHat Network]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/missing-dependencies-for-enterprise-agent-on-redhat-enterprise-linux-rhel-7-installation>

[Enterprise Agent hardware requirements]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/enterprise-agent-hardware-requirements>

[Configuring a Proxy Server]: <https://docs.thousandeyes.com/product-documentation/enterprise-agents/configuring-an-enterprise-agent-to-use-a-proxy-server>

[Ansible Vault]: <https://docs.ansible.com/ansible/latest/user_guide/vault.html>

[Using Vault in playbooks]: <https://docs.ansible.com/ansible/latest/user_guide/playbooks_vault.html>

[Logging Ansible output]: <https://docs.ansible.com/ansible/latest/reference_appendices/logging.html>
