# Role Template

## Role Philosophy 

Creating a role in Ansible is beneficial when your tasks require more structure, reusability, and organization: 
- If the tasks are complex and involve multiple steps, breaking them down into a role can help manage and organize them better. Roles allow you to modularize your code, making it easier to maintain and understand.
- Roles help in separating different aspects of your infrastructure management. For instance, you might have separate roles for setting up a web server, configuring a database, or managing users. This separation makes it easier to manage and troubleshoot issues. A role should do one thing, and one thing very well. Assume a known good state when programming your role, e.g a logging role should never open firewall ports on it's one or create LVM volumes. The logging role should assume that tasks/roles before have been run to create that expected state. The focus of the logging role should only be on configuring logging. 

In team environments, roles facilitate collaboration by providing a clear structure. Team members can work on different roles simultaneously without conflicts. Roles also make it easier to scale your infrastructure by allowing you to apply the same configurations to different sets of hosts. Roles come with a defined structure (tasks, handlers, templates, files, vars, defaults, meta), which helps in documenting your configurations. This standardization makes it easier for new team members to understand the setup and for existing members to find and update configurations. Roles are useful when you need to manage complex sets of variables and defaults. You can define defaults in defaults/main.yml, and allow overrides in vars/main.yml or through playbook variables.

Refer to the documentation for more information [Ansible Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html). 

You should adhere to the standard structure of Ansible repositorys.

```bash 
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   └── httpd.conf.j2
    ├── files/
    │   └── index.html
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
    └── README.md
```

###  Example Scenario: Setting Up a Web Server
Suppose you need to set up a web server, which involves:

- Installing the web server package.
- Configuring the web server.
- Managing the service.
- Deploying web content.
- Ensuring security settings.
- Creating a role named webserver can encapsulate all these tasks and provide a clean, reusable structure.

Creating a role is the right approach when dealing with complex, reusable, and multi-step tasks. It promotes better organization, reusability, and maintainability of your playbooks. If your task meets any of the above criteria, consider creating a role to encapsulate it. This will lead to a more scalable and manageable Ansible setup. 

## Writing Roles: 

### Adhere to best practices

Try to keep to the following best practices: 
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html#role-directory-structure
- https://redhat-cop.github.io/automation-good-practices/#_roles_good_practices_for_ansible
- https://www.golinuxcloud.com/create-ansible-role-with-example-playbooks/
- https://www.digitalocean.com/community/tutorials/how-to-use-ansible-roles-to-abstract-your-infrastructure-environment

### Role Variables 

If you are creating role-variables, only publish relevant variables in the `defaults/main.yml` and keep internal variables in `vars/main.yml`

[Vars vs defaults](https://redhat-cop.github.io/automation-good-practices/#_vars_vs_defaults)

### Role Dependencies 

Role dependencies are prerequisites, not true dependencies. The roles do not have a parent/child relationship. Ansible loads all listed roles, runs the roles listed under dependencies first, then runs the role that lists them. The play object is the parent of all roles, including roles called by a dependencies list.

[Dependencies](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html#using-role-dependencies)