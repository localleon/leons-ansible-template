# single_playbooks 

 There are scenarios where splitting tasks into a single playbook for one-off tasks is beneficial, especially when roles are not needed. 

Simplicity and Focus: When the task is simple and not part of a recurring workflow, a single playbook can keep things straightforward. For example, running a one-time script, making a quick configuration change, or patching a specific issue.

Ad-Hoc Operations: If you need to perform an operation that is not part of your regular deployments or maintenance procedures, a single playbook can be ideal. This could include tasks like:
- Gathering specific information from nodes.
- Running diagnostic commands.
- Testing and Troubleshooting: When experimenting or troubleshooting, having a dedicated playbook can help isolate the changes you're making without affecting other parts of your infrastructure. This makes it easier to revert or adjust your changes.

Small Scale or Limited Scope: If the tasks affect only a small number of nodes or services, creating a full role might be overkill. A single playbook is quicker to write and easier to manage for such limited scope activities.

Immediate Execution: For tasks that need immediate execution and don’t require the overhead of role creation, a playbook is more efficient. This might include urgent security patches or configuration tweaks.

Non-Standard Environments: When working in environments that don't conform to your typical infrastructure (e.g., a temporary lab setup or a demo environment), using a single playbook allows for quick adjustments without impacting your standard roles and playbooks.

Example of a One-Off Task Playbook
Here’s a simple example of a playbook for a one-off task to update the system packages on a group of servers:

```yaml
---
- name: Update system packages
  hosts: webservers
  become: yes
  tasks:
    - name: Ensure all packages are up to date
      apt:
        update_cache: yes
        upgrade: dist
```
You can write and execute a single playbook faster than defining a new role with associated tasks, handlers, templates, and files. Less overhead in terms of file organization and management, especially for tasks that won’t be reused. The task at hand is clearly defined within a single file, making it easier to understand and execute.

Use these single playbooks with care, if you need to share tasks in your single playbook files across projects, reconsider if a playbook is a good fit. 