# Ansible - pt2

## ansible.cfg

[Ansible docs](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings-locations)

Example for ansible.cfg stored in the playbook folder:

```ini
[defaults]
nocows = True
roles_path = ./roles:/etc/ansible/roles
inventory = inventory
become = true
stdout_callback = yaml
```

## Roles

Just an easy way to structure plays, files, templates etc.

`ansible-galaxy init --offline training`

There are other ways to go about it, but considering this is the upstream standard to create roles we going to stick with it.

- defaults – location for the default variable for your role. I’m still new with ansible variable precedence but, I’ve learned so far that variable defined in the defaults directory of a role can be easily overwritten because they have the lowest priority. This is where you define a variable value just in case there is none defined when the role is called.
- files – this is where you store any file that must be copied on the destination machine. Within this directory you can store any files such as configuration files, rpm packages. Note that files stored within this directory cannot be modified as templates so usually they are just copied to the destination machines.
- handlers – notify directives within ansible tasks usually interact with system services. Within the handlers directory you store any definition for System services.
- meta – role dependencies are stored within the meta directory. It also hosts other information for a specific role such as the author of the role, the description, company name, license, minimum ansible version, supported platforms, categories.
- tasks – installation and configuration actions for that specific role
- templates – location for ansible templates. These are files that are written in Jinja2 templating language and can be modified as the machine is provisioned. Note that you can customize a template for any structure or configuration needed.
- tests – this is where you would place any verification mechanism for the results of your role
- vars – directory used to store variables for the role. Variable stored here have higher priority than those stored on the defaults directory.

## Ansible-vault

encrypt, decrypt, edit, rekey, call it in playbook

## Conditionals

[Ansible conditionals](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)

- when

## Delegate

[Ansible delegation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html)

```yaml
delegate_to: 127.0.0.1
```

Will execute the task on `localhost`

## Loops

- item
- with_items
- loop
- loop_control

## Handlers

[Handlers Ansible doc](https://docs.ansible.com/ansible/latest/user_guide/playbooks_handlers.html)
Running a task on change. Useful when want to run something immediately after something changed.
Can be included in a playbook under `handlers`or in a galaxy formated role under `handlers folder`.

Example:

```yaml
---
- name: Verify apache installation
  hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest

  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
    notify:
    - Restart apache

  - name: Ensure apache is running
    ansible.builtin.service:
      name: httpd
      state: started

  handlers:
    - name: Restart apache
      ansible.builtin.service:
        name: httpd
        state: restarted
```

## Linting and testing

- [yamllint](https://github.com/adrienverge/yamllint)
- ansible-playbook --syntax-check (least useful or trustworthy)
- [ansible-lint](https://ansible-lint.readthedocs.io/en/latest/)
- molecule (not covering it)
- [ansible-playbook --check](https://docs.ansible.com/ansible/latest/user_guide/playbooks_checkmode.html) (against prod)

### yamllint

`pip install yamllint`

Example config to avoid issues:

```yaml
---
$ .yamllint

extends: default

rules:
  line-length:
    max: 180
    level: warning

ignore: |
  .github/stale.yml
```
