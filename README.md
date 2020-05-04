# Ansible role mounts

Managing mounts.

- [Ansible role mounts](#ansible-role-mounts)
  - [License](#license)
  - [Author Information](#author-information)
  - [Requirements](#requirements)
  - [Dependencies](#dependencies)
  - [Compatibility](#compatibility)
  - [Role Variables](#role-variables)
  - [Example Playbook](#example-playbook)
  - [Useful shell commands](#useful-shell-commands)
  - [Additional documentation resources](#additional-documentation-resources)
  - [Testing with Molecule](#testing-with-molecule)
  - [CI/CD with Travis CI](#cicd-with-travis-ci)
  - [Useful links](#useful-links)

## License

MIT / BSD

## Author Information

- Made and maintained by: [Kasra Amirsarvari](https://www.linkedin.com/in/caseraw)
- Ansible Galaxy community author: <https://galaxy.ansible.com/caseraw>
- Dockerhub community user: <https://hub.docker.com/u/caseraw>

## Requirements

- Ensure a package manager is available and configured with the correct package sources and repositories.
- Ensure privileged permissions are set for the user executing this role to:
  - Install packages.
  - Edit and create directories as mount paths.
  - Mount permissions.
  - Edit `/etc/fstab`.
  - Create and edit automount configuration files.

## Dependencies

N/A

## Compatibility

Compatible with the following list of operating systems:

- CentOS 7
- CentOS 8
- RHEL 7.x
- RHEL 8.x

## Role Variables

| Variable name | Description |
|---------------|-------------|
| role_mounts_list | Combined list of other lists that start with the name `role_mounts_list_`. |
| role_mounts_required_packages | A dictionary with lists to install the required packages. |

## Example Playbook

```yaml
---
- name: Manage mounts
  become: True
  gather_facts: False
  tasks:
    - import_role:
        name: ansible_role_mounts
      vars:
        role_mounts_list_default:
          - type: nfs
            state: mounted
            src: 192.168.122.201:/srv/nfsexports
            path: /srv/nfsmounts
            fstype: nfs
            opts: defaults,sync
            path_owner: root
            path_group: radix
            path_mode: '0770'
          - type: cifs
            state: mounted
            src: //192.168.122.201/smbexports
            path: /srv/smbmounts
            fstype: cifs
            opts: defaults,noperm,credentials=/root/creds.txt
            path_owner: root
            path_group: root
            path_mode: '0775'
          - type: autofs
            state: present
            path: /srv/autofs
            path_owner: root
            path_group: root
            path_mode: '0755'
            master_file: /etc/auto.master.d/autofsexports.autofs
            master_file_owner: root
            master_file_group: root
            master_file_mode: '0640'
            master_params:
              - /srv/autofs /etc/auto.autofsexports
            map_file: /etc/auto.autofsexports
            map_file_owner: root
            map_file_group: root
            map_file_mode: '0640'
            map_params:
              - autofsexports -fstype=nfs,rw,soft,intr 192.168.122.201:/srv/autofsexports
              - autofsexports2 -fstype=nfs,rw,soft,intr 192.168.122.201:/srv/autofsexports2
        role_mounts_required_packages:
          nfs:
              - nfs-utils
          cifs:
              - cifs-utils
          autofs:
              - autofs

...
```

## Useful shell commands

N/A

## Additional documentation resources

- <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/storage_administration_guide/index#s1-nfs-start>
- <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_file_systems/assembly_mounting-an-smb-share-on-red-hat-enterprise-linux_managing-file-systems>

## Testing with Molecule

This role is locally tested with the use of [Molecule](https://molecule.readthedocs.io/en/latest/), the configuration is located at: [molecule/default](molecule/default).  
The Molecule tests are run (using the [docker driver](https://molecule.readthedocs.io/en/latest/configuration.html#docker)) on [Dockerhub images](https://hub.docker.com/u/caseraw) built for this purpose:

- [CentOS](https://hub.docker.com/r/caseraw/ansible-molecule-centos)
- [Fedora](https://hub.docker.com/r/caseraw/ansible-molecule-fedora)

## CI/CD with Travis CI

This role uses [Travis CI](https://travis-ci.org/) to run online tests with the use of [Molecule](https://molecule.readthedocs.io/en/latest/) and pushes notifications to import the role into [Ansible Galaxy](https://galaxy.ansible.com/) once the tests are successful. The Travis CI configuration is located at the root of the Ansible role [.travis.yml](.travis.yml)

## Useful links

- GitHub repository: <https://github.com/Caseraw/ansible_role_postfix>
- Travis CI build status: <https://travis-ci.org/Caseraw/ansible_role_postfix>
- Ansible Galaxy role: <https://galaxy.ansible.com/caseraw/ansible_role_postfix>
