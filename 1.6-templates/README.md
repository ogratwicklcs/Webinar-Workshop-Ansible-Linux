# Workshop Exercise - Templates

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Step 1 - Using Templates in Playbooks](#step-1---using-templates-in-playbooks)


# Objective

This exercise will cover Jinja2 templating. Ansible uses Jinja2 templating to modify files before they are distributed to managed hosts.   

# Guide

## Step 1 - Using Templates in Playbooks

When a template for a file has been created, it can be deployed to the managed hosts using the `template` module, which supports the transfer of a local file from the control node to the managed hosts.

Create the directory `templates` to hold template resources in `~/ansible-files/`:

```bash
[student<X>@ansible ansible-files]$ mkdir templates
```

Then in the `~/ansible-files/templates/` directory create the template file `motd-facts.j2`:

<!-- {% raw %} -->
```html+jinja
Welcome to {{ ansible_hostname }}.
{{ ansible_distribution }} {{ ansible_distribution_version}}
deployed on {{ ansible_architecture }} architecture.
```
<!-- {% endraw %} -->

The template file contains the basic text that will later be copied over. It also contains variables which will be replaced on the target machines individually.

Next we need a playbook to use this template. In the `~/ansible-files/` directory create the Playbook `motd-facts.yml`:

```yaml
---
- name: Fill motd file with host data
  hosts: node1
  become: true
  tasks:
    - name: Deploy Jinja2 Template
      template:
        src: motd-facts.j2
        dest: /etc/motd
        owner: root
        group: root
        mode: 0644
```
Execute the Playbook `motd-facts.yml`.

```bash
[student<X>@ansible ansible-files]$ ansible-playbook motd-facts.yml
```

Login to node1 via SSH and check the message of the day content.

```bash
[student<X>@ansible ansible-files]$ ssh node1
```

Log out of node1.
```bash
[student<X>@ansible ansible-files]$ exit
```


You should see how Ansible replaces the variables with the facts it discovered from the system.

----
**Navigation**
<br>
[Previous Exercise](../1.5-handlers) - [Next Exercise](../2.1-intro)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-1---ansible-engine-exercises)
