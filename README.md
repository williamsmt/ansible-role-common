# ansible-role-common
![Build Test](https://github.com/williamsmt/ansible-role-common/workflows/Build%20Test/badge.svg)

A common role to configure a virtual linux host with all basic packages and settings to function. Generally, this role updates the host, installs ppa, creates a local administrator, and hardens remote access. It lends to a VMware operating environment for both provisioning (using Packer) and day 2 maintenance (ci/cd) unless otherwise instructed - see below for details.

Requirements
------------

This role requires the ansible.posix collection as notated in the meta yaml. If using a MattsLab playbook, this has been included in the requirements.yml. If using your own playbook, you must install the ansible.posix collection prior to running the play.

Role Variables
--------------

| Variable | Required | Default | Choices | Comments |
|----------|----------|---------|---------|----------|
| proxy_host | no | | "proxy.example.com" | can be either fqdn or ip address |
| proxy_port | no | | "3128" | |
| packages | no | | list of packages | |
| root_login | no | False | True/False | controls if the root account is disabled and blocked from ssh access |
| ssh_password | no | True | True/False | If enabled, password authentication is allowed for ssh - consider disabling this! |
| sudo_group | no | | "myCoolGroup" | freeform group name |
| local_admin | no | | list of users | list of usernames (assumed member of sudo_group) |
| local_admin_key_http | no | | list of url locations to public ssh keys | Github user keys is a good example |
| local_admin_key_file | no | | list of file paths to public ssh keys | Can be relative path to host where Ansible is being executed |
| vmware_env | yes | False | True/False | Controls whether or not to install VMware specific tooling
| upgrade_guest | yes | False | True/False | Indicates Debian based guest should dist-upgrade

Dependencies
------------

No dependent roles required. This role does require the ansible.posix collection to successfully create an authorized_keys file for local administrators. Many of the tasks are currently conditioned for Debian flavor support, but can be easily modified to support other linux distributions. Please submit a PR for any enhancements you choose to include in the role.

Sample requirements.yml file for custom playbook:

    collections:
      - name: ansible.posix

    roles:
      - src: https://github.com/williamsmt/ansible-role-common.git
        version: 21.5.2
        name: ansible-role-common

To install this role using a requirements.yml file in the playbook directory:

`ansible-galaxy role install -r requirements.yml -p ./roles`

To install the ansible.posix collection using a requirements.yml file in the playbook directory:

`ansible-galaxy collection install -r requirements.yml`

A file `files/banner.txt` is included with this role. Amend this to your desired login text to show banner at both console and ssh logins. It will be installed at `/etc/issue`.

Example Playbook
----------------

Sample playbook passing common parameters for both image build (with Packer) and day 2 maintenance:

    - name: Build linux host
        hosts: all
        vars:
          packages:
            - htop
            - net-tools
            - vim
            - git
            - openssh-server
          ssh_password: False
          sudo_group: zsudoers
          local_admin: 'localadmin'
          local_admin_key_file: '~/.ssh/id_rsa.pub'
          vmware_env: True
          upgrade_guest: True

        roles:
          - ./roles/ansible-role-common

To run the playbook:

`ansible-playbook -i {ip_address_of_host_or_inventory_file} playbook.yml`

License
-------

Released under the [Apache-2.0 license](LICENSE)

Author Information
------------------

https://github.com/williamsmt

This is not an officially supported Google product