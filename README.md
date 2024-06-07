# leons-ansible-template
Template for building small and large Ansible projects

## Intro

Ansible best practice suggests an Ansible Project repository and separate repositories for your Ansible Roles. The purpose of having an Ansible Project repo is to maintain a single source of truth with regards to the following elements:

- Inventory (environments, hosts, groups, platforms)
- Configuration Management (variables)
- Plugins for custom behevior
- Linting rules for Continuous Integration tests
- Playbooks to run each use case
- Vagrantfile definitions for testing
- etc.

`Ansible Playbooks` should be very short and simple - they define **WHERE** and **WHAT**. `Ansible Roles` should be environment-agnostic and answer **HOW**. Therefore they should be built independently from the Ansible Project repository. This allows **any** Ansible Playbook to use the Ansible Role and run the tasks against **any** environment. The overall goal for Ansible Roles should be a reusable, encapsulated, and environment-agnostic object.

This repository provides both a "role template" and a template for your project. 

## Ansible Community - Automation Good Practices 

The Community of Practice for Ansible from RedHat has it's own [Good Practices for Ansible - GPA](https://redhat-cop.github.io/automation-good-practices/). It's much more detailed than this project and provides you with information about more specific questions. 

## Version Control 

Managing multiple environments in Ansible can be handled within a single Git repository or across multiple repositories, depending on various factors such as complexity, team structure, and project requirements. Try to structure environments based on the technology stack and access rights of the persons working on the projects. Changes in one environment won't inadvertently affect others, reducing the risk of deployment issues. If different teams are responsible for different environments, having separate repositories can align with team boundaries and responsibilities. Different repositories allow finer-grained access control, ensuring that only authorized personnel can make changes to sensitive environments like production.

## Development 

You should write YAML-Files in an Text-Editor like VSCode. Please use proper Syntax Highlighting and format your code via 'ansible-linter' 

- Install Ansible extension
  - [VSCode Ansible extension](https://marketplace.visualstudio.com/items?itemName=redhat.ansible)
- Configure Ansible extension
  - Configure the settings for the extension to use your custom execution environment image you built using `ansible-builder`
  - Adjust the lint rules by creating `.ansible-lint` configuration file in your repository
  - It requires using `skip_list` otherwise you'll still see red-marked issues in your editor window
  - `ansible-lint` will leverage the `.yamllint.yml` configuration file to additionally check the yaml rules

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

[Installing Multiple Roles From a File](https://galaxy.ansible.com/docs/using/installing.html#installing-multiple-roles-from-a-file)

Whenever possible, use prefabricated roles from internal teams or external certified content. Refer to the following resources: 
- [rhel-system-roles (Standard tasks by RedHat)](https://access.redhat.com/documentation/pa/red_hat_enterprise_linux/8/html/automating_system_administration_by_using_rhel_system_roles/intro-to-rhel-system-roles_automating-system-administration-by-using-rhel-system-roles)
- [RedHat Automation Hub](https://www.redhat.com/de/technologies/management/ansible/automation-hub)

## Gathering Facts

It is best practice to change the default setting in `ansible.cfg` from using `implicit` gathering to `explicit`. This forces developers to explicitly state that facts need to be gathered for particular hosts, otherwise it can result in unwanted fact gathering tasks that consume time.

This is set in the `ansible.cfg` file:

```yaml
gathering = explicit
```

## Variables

Variables can be defined in many different places in this repo. It is recommended to get familiar with the common filter functions such as `default`, `mandatory`, etc. Filter functions stem from Jinja and help transform data.

- For Jinja core filter functions see the [list of builtin filters](http://jinja.pocoo.org/docs/2.10/templates/#builtin-filters).
- For Ansible core filter functions see [this page](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html).

Some common filter functions:

- [default](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#defaulting-undefined-variables)
- [lower](http://jinja.pocoo.org/docs/2.10/templates/#lower)
- [sort](http://jinja.pocoo.org/docs/2.10/templates/#sort)
- [trim](http://jinja.pocoo.org/docs/2.10/templates/#trim)
- [unique](http://jinja.pocoo.org/docs/2.10/templates/#unique)
- [undefined](http://jinja.pocoo.org/docs/2.10/templates/#undefined)
- [mandatory](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#forcing-variables-to-be-defined)

### Group Variables

This repo contains a `group_vars` folder for each `environments` folder. that includes standard connectivity variables defined for each Operating System Family. The Ansible `setup` module gathers facts about a target server and produces the `ansible_os_family` fact that describes the OS family. Therefore the filenames are named after the OS family since this is also the inventory group that our servers will be using.

For example, a Windows server will be assigned to the `windows` inventory group. As a result, Ansible will automatically load the `group_vars/windows.yml` file. For Windows servers, the recommended authentication method uses `winrm` transport and `kerberos` authentication.

For further information on Windows Remote Management (winrm), see this [link](https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html).

## Secret Management

Secrets should never be commited to Git in clear text, however it's really comfortable to have them stored encrypted side-by-side with your normal project. We recommend to use: 
 - https://github.com/getsops/sops (for storing the variables backed by mozilla)
 - https://github.com/FiloSottile/age (modern encryption tool age backed by mozilla)

You can find a sample-workflow on this public Github Repository [sops-examples-ansible-and-k8s](https://github.com/localleon/sops-examples-ansible-and-k8s). Ansible Vault has been troublesoom in production and is not recommended. 

## Continuous Integration

Your repo should implements various mechanisms to ensure we "Fail early and fail fast" using CI tools such as ansible-lint, pre-commit as well as others.

TODO: Implement Gitlab-CI Ansible Workflows 

### Ansible Lint

This repo uses [Ansible Lint](https://docs.ansible.com/ansible-lint/) to perform various lint tests on YAML files.

The repo contains an Ansible Lint configuration file (.ansible-lint) to configure the default options and determine rules to skip as well as rules to enforce. A list of all default rules and their descriptions can be found [here](https://docs.ansible.com/ansible-lint/rules/default_rules.html#default-rules).

Additionally, Ansible Lint allows for custom rules written in Python script. These have been placed inside the `.ansible_lint` folder. See the following link for more information on writting custom Python scripts for new Ansible Lint rules:

https://docs.ansible.com/ansible-lint/rules/rules.html#creating-custom-rules

### Yaml Lint

Another linting tool that is quite helpful is the YAML Lint tool.

### Testing

Refer to (Molecule)[https://ansible.readthedocs.io/projects/molecule/] on how to get started with Unit-Testing your Ansible roles.

## Inventory

### Multi-Stages and Multi-Platforms

To address the challenge of managing multi-stage environments with Ansible, it is recommended read through the following resources. This applies to situation where you have multiple stages (dev, test, and prod) as well as when you have multiple platforms (AWX, Google Cloud, Azure, VmWare, etc) that have their own multiple stages (dev, test, prod). No matter the situation it is important to establish a good inventory structure so you can manage your configuration (vars) appropriately.

- [How to Manage Multistage Environments with Ansible](https://www.digitalocean.com/community/tutorials/how-to-manage-multistage-environments-with-ansible).
- [Managing multiple environments with Ansible - best practices](https://rock-it.pl/managing-multiple-environments-with-ansible-best-practices/)
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

## Open Topics

We are welcoming contributions to this template, some open topics are: 
- Unit-Testing via GitlabCI, providing some example workflows. 

## References

[The Inside Playbook - USING ANSIBLE AND ANSIBLE TOWER WITH SHARED ROLES](https://www.ansible.com/blog/using-ansible-and-ansible-tower-with-shared-roles)
[Inspiration for README comes from here!](https://github.com/ansiblejunky/ansible-project-template)

## License

MIT 

## Author 

Leon Rauschenbach 