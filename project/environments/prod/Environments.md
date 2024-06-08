# Environments (aka inventorys for projects)

The approach we will demonstrate in this project relies on Ansible group variables and multiple inventories. However, there are several other strategies that are worth considering. We will explore some of these ideas below and why they might present problems when implemented in complex environments.

Typically, the requirements for different stages will lead to different numbers and configurations of components. For example, the memory requirements for a development server might be different than those for staging and production and it’s important to have explicit control over how the variables that represent those requirements are prioritized. In this article, we will discuss some ways that these differences can be abstracted and some constructs that Ansible provides to encourage configuration reuse.

Create different inventory-folders for your environments: 
```bash
project/environments
├── prod
│   ├── Environments.md
│   ├── group_vars
│   │   ├── all.yml
│   │   ├── external.yml
│   │   └── webservers.yml
│   ├── hosts.yml
│   └── host_vars
│       └── ubuntu-test0.example.com.yaml
└── test
    ├── group_vars
    │   ├── all.yml
    │   ├── external.yml
    │   └── webservers.yml
    ├── hosts.yml
    └── host_vars
        └── ubuntu-test0.example.com.yaml
```

If you are working with the same technology-stack on different projects, you can also seperate your environments with one level depper like 'environments/athena/test' and 'environments/athena/prod'!

## Why not to rely solely on Group Variables 

At first glance, it may appear that group variables provide all of the separation between environments that Ansible requires. You can designate certain servers as belonging to your development environment, and others can be assigned to the staging and production areas. Ansible makes it easy to create groups and assign variables to them.

Group intersection, however, brings serious problems for this system. Groups are often used to categorize more than one dimension. For example:

- deployment environments (local, dev, stage, prod, etc.)
- host functionality (web servers, database servers, etc.)
- datacenter region (NYC, SFO, etc.)
In these cases, hosts will usually be in one group per category. For example, a host may be a web server (functional) on stage (deployment environment) in NYC (datacenter region).

If the same variable is set by more than one group for a host, Ansible has no way of explicitly specifying precedence. You may prefer the variables associated with the deployment environments to override other values, but Ansible doesn’t provide a way to define this. Instead, Ansible uses the last loaded value. Since Ansible evaluates groups alphabetically, the variable associated with whichever group name happens to be last in dictionary ordering will win. This is predictable behavior, but explicitly managing group name alphabetization is less than ideal from an administrative perspective.

That's why we split up our environments into multiple different folders and each one get's its own inventory source.

## Cross Inventory Group Variables 
One thing that is not possible in the recommended setup is variable sharing across environments. There are a number of ways we could implement cross-environment variable sharing. One of the simplest is to leverage Ansible’s ability to use directories in place of files. You can use symbolic links on your filesystem, to map cross-enviornmental variable files. 

