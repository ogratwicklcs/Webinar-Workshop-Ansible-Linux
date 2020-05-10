# Workshop Exercise - Conditionals, Handlers and Loops

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Step 1 - Conditionals](#step-1---conditionals)
* [Step 2 - Handlers](#step-2---handlers)
* [Step 3 - Simple Loops](#step-3---simple-loops)
* [Step 4 - Loops over hashes](#step-4---loops-over-hashes)

# Objective

Three foundational Ansible features are:  
- [Conditionals](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)
- [Handlers](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#handlers-running-operations-on-change)
- [Loops](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

# Guide

## Step 1 - Conditionals

Ansible can use conditionals to execute tasks or plays when certain conditions are met.

To implement a conditional, the `when` statement must be used The condition is expressed using one of the available operators like e.g. for comparison:

|      |                                                                        |
| ---- | ---------------------------------------------------------------------- |
| \==  | Compares two objects for equality.                                     |
| \!=  | Compares two objects for inequality.                                   |
| \>   | true if the left hand side is greater than the right hand side.        |
| \>=  | true if the left hand side is greater or equal to the right hand side. |
| \<   | true if the left hand side is lower than the right hand side.          |
| \< = | true if the left hand side is lower or equal to the right hand side.   |
| in  | Operator is used to check if a value exists in a sequence or not       |



As an example you would like to install an FTP server, but only on hosts that are in the "ftpserver" inventory group.

To do that, first edit the inventory to add another group, and place `node2` in it. Make sure that that IP address of `node2` is always the same when `node2` is listed. Edit the inventory `~/lab_inventory/hosts` to look like the following listing:

```ini
[all:vars]
ansible_user=student1
ansible_ssh_pass=ansible
ansible_port=22

[web]
node1 ansible_host=11.22.33.44
node2 ansible_host=22.33.44.55
node3 ansible_host=33.44.55.66

[ftpserver]
node2 ansible_host=22.33.44.55

[control]
ansible ansible_host=44.55.66.77
```

Next create the file `ftpserver.yml` on your control host in the `~/ansible-files/` directory:

```yaml
---
- name: Install vsftpd on ftpservers
  hosts: all
  become: yes
  tasks:
  - name: Install FTP server when host in ftpserver group
    yum:
      name: vsftpd
      state: latest
    when: inventory_hostname in groups["ftpserver"]
```

Run it and examine the output. The expected outcome: The task is skipped on node1, node3 and the ansible host (your control host) because they are not in the ftpserver group in your inventory file.

```bash
[student<X>@ansible ansible-files]$ ansible-playbook ftpserver.yml

TASK [Install FTP server when host in ftpserver group] *******************************************
skipping: [ansible]
skipping: [node1]
skipping: [node3]
changed: [node2]
```

# Step 2 - Handlers

Sometimes when a task does make a change to the system, an additional task or tasks may need to be run. For example, a change to a service’s configuration file may then require that the service be restarted so that the changed configuration takes effect.

Here Ansible’s handlers come into play. Handlers can be seen as inactive tasks that only get triggered when explicitly invoked using the "notify" statement. 

As a an example, let’s write a Playbook that:

  - manages Apache’s configuration file `/etc/httpd/conf/httpd.conf` on all hosts in the `web` group

  - restarts Apache when the file has changed

First we need the file Ansible will deploy, let’s just take the one from node1. Remember to replace the IP address shown in the listing below with the IP address from your individual `node1`.

```
[student<X>@ansible ansible-files]$ scp node1:/etc/httpd/conf/httpd.conf ~/ansible-files/files/.
student<X>@node1 password:
httpd.conf             
```

Next, create the Playbook `httpd_conf.yml`. Make sure that you are in the directory `~/ansible-files`.

```yaml
---
- name: manage httpd.conf
  hosts: web
  become: yes
  tasks:
  - name: Copy Apache configuration file
    copy:
      src: httpd.conf
      dest: /etc/httpd/conf/
    notify: restart_apache
  handlers:
    - name: restart_apache
      service:
        name: httpd
        state: restarted
```

So what’s new here?

  - The "notify" section calls the handler only when the copy task actually changes the file. That way the service is only restarted if needed - and not each time the playbook is run.

  - The "handlers" section defines a task that is only run on notification.
<hr>
  -  Now change the `Listen 80` line in ~/ansible-files/files/httpd.conf:

```ini
Listen 8080
```

  - Run the Playbook:

      - httpd.conf should have been copied over

      - The handler should have restarted Apache

Apache should now listen on port 8080. Easy enough to verify:

```bash
[student1@ansible ansible-files]$ ansible-playbook httpd_conf.yml
[student1@ansible ansible-files]$ curl node2
curl: (7) Failed connect to 22.33.44.55:80; Connection refused
[student1@ansible ansible-files]$ curl node2:8080
<body>
<h1>This is a production webserver, take care!</h1>
</body>
```

## Step 3 - Simple Loops

Loops enable us to repeat the same task over and over again. For example, lets say you want to create multiple users. Loops can also iterate over more than just basic lists. For example, if you have a list of users with their coresponding group, loop can iterate over them as well.

To show the loops feature we will generate three new users on `node1`. For that, create the file `loop_users.yml` in `~/ansible-files` on your control node as your student user. We will use the `user` module to generate the user accounts.

<!-- {% raw %} -->
```yaml
---
- name: Ensure users
  hosts: node1
  become: yes

  tasks:
  - name: Ensure three users are present
    user:
      name: "{{ item }}"
      state: present
    loop:
        - dev_user
        - qa_user
        - prod_user
```
<!-- {% endraw %} -->

Understand the playbook and the output:

<!-- {% raw %} -->
  - The names are not provided to the user module directly. Instead, there is only a variable called `{{ item }}` for the parameter `name`.

  - The `loop` keyword lists the actual user names. Those replace the `{{ item }}` during the actual execution of the playbook.

  - During execution the task is only listed once, but there are three changes listed underneath it.
<!-- {% endraw %} -->

```bash
[student1@ansible ansible-files]$ ansible-playbook loop_users.yml
```

----
**Navigation**
<br>
[Previous Exercise](../1.4-variables) - [Next Exercise](../1.6-templates)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-1---ansible-engine-exercises)
