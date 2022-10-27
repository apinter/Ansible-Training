# Intro

## What is Ansible?

_Ansible_ is a modular configuration management tool for IT automation, and _DevOps_. Can be run on _Linux_, _Windows_, and _Mac_, integrated with CI platforms

### Ansible Documentation

- [Official docs](https://docs.ansible.com)
- [Ansible blog](https://www.ansible.com/blog)

## Installing Ansible

_Ansible_ can be installed a few different ways, but the upstream recommended way is to use _pip_ and avoid using the distribution packaged versions. Using _pip_ will also allow for using the latest version of _Ansible_.

`pip3 install ansible`

Another method is to run _Ansible_ in a _toolbox_ type container, this way the system doesn't get cluttered by _python_ libs.

## Requirements

_Ansible_ is agentless and can connect a number of different ways to hosts. By default _Ansible_ will connect to a host over _ssh_. It is preferred to use keypairs to connect to hosts which will make it easy to run _playbooks_, _roles_ in a CI pipeline.
With that being said _Ansible_ does require a version of _Python_ to be installed on the target host.

__NOTE:__ Fedora CoreOS doesn't have _Python_ installed by default and the package must be layered. This can be done within the _Ignition_ file during first boot.

## Ansible structure

Main components of _Ansible_.

## Modules

_Ansible_ hold a bunch of modules which are written in _Python_. The utilized modules will get copied over to the host and executed locally with the parameters used in the task. Thus the need to have _Pyhton_ installed on the target.

[Ansible modules](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)

## Facts

`ansible -i inventory all -m setup`

## Inventory

The inventory file holds the list of hosts that _Ansible_ can manage. This file traditionally written in an _ini_ style, but _yaml_ supported as well. The hosts can be grouped by a preferred way. The inventory can hold a number of different variables specified for a group or an individual host.
Example inventory:

```ini
[example]
demo.antavo.com
14.15.17.2
example_host ansible_host=35.234.72.202 

[example:vars]
ansible_ssh_private_key_file=~/.ssh/antavo_gcp 
ansible_user=attila.pinter
```

A real life example is available [here](https://gitlab.antavo.com/infrastructure/ansible/inventory)

## Playbooks

## Tasks

Tasks are configured modules to run on a host

__Important:__ _Ansible_ is item potent therefore if a task is running, but the host already has the task impelemented changes in existence it will not run it again and give back an __OK__ message.

Example task:

```yaml
- name: Install ranger on openSUSE
  community.general.zypper:
    name: ranger
    state: latest
```

## Variables

_Ansible_ is using the _jinja2_ variable mechanism. In yaml this gets tricky from time to time and must be used to following way:

- If the variable if the first value to a key it must be double quoted "" and within the quotes must use a double curly bracket {{ }}.
- If the varaible if not the first value then quotes can be left out.

## Variables in playbooks, files

- `vars`
- `vars_files`

## Ansible ad-hoc commands

The ad-hoc commands can be used by not writing a task or a playbook, but running a command with _ansible_ from the cli. This can be done by using the `-a` flag which will default to the `command` module and executes the desired command on the targeted host(s).

Example:

```bash
ansible -i inventory all -m ping ## Check if every host is accessible by _Ansible_ using the ping module

ansible -i inventory all -a "free-h" ## Show memory usage from all hosts in the inventory 

ansible -i inventory www -a "df -h" ## Show disk usage from the www group hosts only
```
