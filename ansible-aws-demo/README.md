# Handful examples of ansible playbooks and roles

### PlayBooks
Create a playbook with a file name, not necessary to have the filename with any extension, but create with extension with ".yml" else you wont find the difference of normal text file and a playbook.

The file starts with: ---

Basically Playbooks consists of three sections,
- Host declaration
- Variable declaration (optional)
- Action / Tasks Declaration

### Roles

- Ansible roles are consists of many playbooks, which is similar to modules in puppet and cook books in chef. We term the same in ansible as roles.
- Roles are a way to group multiple tasks together into one container to do the automation in very effective manner with clean directory structures.
- Roles are set of tasks and additional files for a certain role which allow you to break up the configurations.
- It can be easily reuse the codes by anyone if the role is suitable to someone.
- It can be easily modify and will reduce the syntax errors

### Facts
Ansible facts are a way of getting data about remote systems for use in playbook variables. Usually these are discovered automatically by the setup module in Ansible.
If your not using any variables this can be turned off to run the playbook faster.
