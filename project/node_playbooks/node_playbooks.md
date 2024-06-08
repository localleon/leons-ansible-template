# Node Playbooks 

Let's assume we have a common structure for different environments (development, staging, production), and we use variables to differentiate between these environments. We also have roles that perform specific tasks for each type of node.

```bash
── project
│   ├── ansible.cfg
│   ├── ansible.log
│   ├── environments
│   │   ├── prod
│   │   │   ├── group_vars
│   │   │   │   ├── all.yml
│   │   │   │   ├── application_type1.yml
│   │   │   │   └── external.yml
│   │   │   ├── hosts.yml
│   │   │   └── host_vars
│   │   │       └── ubuntu-test0.example.com.yaml
│   │   └── test
│   │       ├── group_vars
│   │       │   ├── all.yml
│   │       │   ├── application_type1.yml
│   │       │   └── external.yml
│   │       ├── hosts.yml
│   │       └── host_vars
│   │           └── ubuntu-test0.example.com.yaml
│   ├── node_playbooks
│   │   ├── application.yml
│   │   ├── files
│   │   │   └── FILES.md
│   │   ├── node_playbooks.md
```

node_playbooks/webserver.yml
```yaml
---
- name: Configure webserver nodes
  hosts: webservers
  roles:
    - webserver
```

node_playbooks/dbserver.yml
```yaml
---
- name: Configure dbserver nodes
  hosts: dbservers
  roles:
    - dbserver
```

Node-Playbooks are always executed against the whole inventory. The playbooks themself limit them to the hosts they need! You can run the playbooks for different environments using the -i option to specify the inventory:



```bash 
# For development environment
ansible-playbook -i inventory/development playbooks/webserver.yml
ansible-playbook -i inventory/development playbooks/dbserver.yml

# For staging environment
ansible-playbook -i inventory/staging playbooks/webserver.yml
ansible-playbook -i inventory/staging playbooks/dbserver.yml

# For production environment
ansible-playbook -i inventory/production playbooks/webserver.yml
ansible-playbook -i inventory/production playbooks/dbserver.yml
```

This setup demonstrates how node playbooks include only roles and use variables to control environment-specific configurations. This approach helps maintain a clean and scalable Ansible structure, making it easy to manage different environments and types of hosts.

## Tags

You can use tags to control which parts of a playbook should be run. 

```yaml
---
- name: Configure dbserver nodes
  hosts: dbservers
  roles:
    - name: dbserver
      tag: dbserver 
    - name: user
      tag: user 
```

This will only run the user role.
```bash
ansible-playbook -i inventory/production playbooks/dbserver.yml -t user 
```

Only use tags in top-level-playbooks e.g your node-playbooks. Tags are not always transitive, e.g if you use tags in roles and then control the execution of the role via tag weird things happen. There are also special tags which are sometimes helpful [e.g always and never](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tags.html#always-and-never)!

## Master Playbook. 

Take a look into `node_playbooks/site.yml` to see how to run tasks across different environments. You can use a master-playbook to include all other playbooks if you want to run roles on all node-types. Then control which parts of the playbook run with tags! [See the tag documentation!](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tags.html)

