# Project Name 

This is a brief explanation of our preferred project structure! 

## Inventory

### Multi-Stages and Multi-Platforms

To address the challenge of managing multi-stage environments with Ansible, it is recommended read through the following resources. This applies to situation where you have multiple stages (dev, test, and prod) as well as when you have multiple platforms (AWX, Google Cloud, Azure, VmWare, etc) that have their own multiple stages (dev, test, prod). No matter the situation it is important to establish a good inventory structure so you can manage your configuration (vars) appropriately.

- [How to Manage Multistage Environments with Ansible](https://www.digitalocean.com/community/tutorials/how-to-manage-multistage-environments-with-ansible).
- [Ansible Playbook Structure](http://www.oznetnerd.com/ansible-playbook-structure/)
- [Ansible: Directory Layout](https://dev.to/tmidi/ansible-directory-layout-5edj)

In this repository there are two examples of inventory structures. You may choose either one for your purposes. Ansible Inventories can be constructed using either `folders` or groups or both. In the first example `hostsv1` only folders and symbolic links are used along with multiple inventory files. In the second example `hostsv2` only groups are used in a single inventory file.

### Testing the Inventory

Before you decide on your final inventory structure be sure to test it out. Use the `ansible-inventory` command to determine the output when Ansible reads your inventory structure.

Navigate to an `inventory` file and run the following command:
```
ansible-invenory -i inventory --list
```

Ensure you do not see any warning messages and you see the desired output. Variables should be assigned to hosts and group names should be listed appropriately under the right parent groups and so on.

## Ansible Roles and Collections

In this repo the `requirements.yml` file will be used to define our Ansible Role **dependencies** that are used by our Ansible Playbooks. Additionally, you can use `include:` statements inside your requirements file to break apart the dependencies by whatever means you wish. For example, each file can represent a specific application or business vertical.

When testing playbooks on a terminal, you must manually perform the `ansible-galaxy` command to install the dependent Ansible Roles into the `roles` folder. This can be achieved using the following command:

```bash
# download ansible roles
ansible-galaxy role install -r galaxy/requirements.yml
```

For quickly developing roles and you don't to do the whole "ansible-galaxy" loop over and over again, consider using a temporary full-path in your playbook like: 

```yaml
---
- hosts: all
  become: true
  become_method: sudo
  gather_facts: true
  vars:
  roles:
    - name: /home/lraus/./ansible/roles/sth.gitlab-runner
      tags: sth.gitlab-runner
```

Multiple roles can be installed by listing them in the `requirements.yml` file. The format of the file is YAML, and the file extension must be either .yml or .yaml.
- [Installing Multiple Roles From a File](https://galaxy.ansible.com/docs/using/installing.html#installing-multiple-roles-from-a-file)

Look into namespaces if you and your team are writing Ansible roles. Consider creating a naming-convention and namespace for your team: 
- https://old-galaxy.ansible.com/docs/contributing/namespaces.html

Whenever possible, use prefabricated roles from internal teams or external certified content. Refer to the following resources: 
- [rhel-system-roles (Standard tasks by RedHat)](https://access.redhat.com/documentation/pa/red_hat_enterprise_linux/8/html/automating_system_administration_by_using_rhel_system_roles/intro-to-rhel-system-roles_automating-system-administration-by-using-rhel-system-roles)
- [RedHat Automation Hub](https://www.redhat.com/de/technologies/management/ansible/automation-hub)

Browse your local Source-Code repositorys for roles from other teams that might fit your use-case! 

### What needs a good documentation in your project

Try to keep some precise information in your repository and link to external sources like Confluence from there. The following sections are a must to include: 

### Project Description 

Provide a generic description of your repository 

### Environments 

The following provides a description of the environments in this repository.

#### projectA/test 

Provide documentation about the Ansible Environment here. 

#### projectA/prod  

You can also provide some simple install instructions here in these sections

```bash 
ansible-playbook -i environments/hetzner/hcloud.yml node-playbooks/runner-node.yaml -t sth.gitlab-runner -D
```

### Technology Stack 

Link to documentation about the used technology stack here. Add interesting documentation about used roles and functions here. 