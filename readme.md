# Ansible
courtesy of this video! https://www.youtube.com/watch?v=wgQ3rHFTM4E

## What ansible?
Helps with server management. For example I could have to manage [web servers] powered by Apache, or [database servers] powered by MySQL. But as number of servers increases it becomes more complex
and we would start to lose consistency. Ansible solves this code written *ONCE* for the installation and deployed *MULTIPLE* times. So write one script and each script and deployed to teach environment.

## Why ansible?
Ansible is a tool that provides:
- IT automation, so instructions that automate the IT set-up we typically used to do manually.
- Having consistent configuration and identical setups of the [web servers] for example.
- Automatic deployment, to make the speed of everything more efficient.

## Ansible - pull configuration tool
2 different ways to setup different environments for server farms:

1. [PULL] One is to have a key server that holds all of the instructions, and then on each of the servers that connect to the key server (main master server) we would have a piece of software known as the 'client' which will communicate with the main master server and then periodically update or change configuration of slave server.

2. [PUSH] Alternative is different, main difference is that in push there is no 'client' software installed on each of the slave servers, you simply force push from the key server and force a restructure or a fresh clean installation in that environment.

Ansible is a [PUSH] configuration tool. This contrast with other products such as Chef and Puppet which are [PULL] configurations. With ansible you are just pushing the script onto the remote hardware, advantages include things such as not having to worry about the client overhead installed on the remote servers that would constantly have to communicate with the key server.

## Ansible architecture
First thing is to have a 'Local machine' which is where all the instructions and scripts we will be pushing out to the remote servers will be. Connected from the local machine will be all the different nodes which will have small programs pushed out to them, these are called 'ansible modules' which contain 'playbooks'. The local machine also has a second job of managing the inventories of the nodes (remote server) we have in our environment, the local machine can ssh client into each node.

## Playbook
This is where we create the instructions to define the architecture of the hardware and configure the nodes. These instructions are written in YAML. This is an example playbook:
````
--- (Three dashes indicate start of ansible playbook.)
(The script itself is characterised in this example by two distinct plays. In each play we must define the node/'remote server' we are targeting by specifying which one under 'hosts')
(The in each play we have the specific tasks we want to execute on the node.)
(Play 1)
- hosts: webservers
  remote_user: root

  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
(Play 2)
- hosts: databases
  remote_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest
  - name: ensure that postgresql is started
    service:
      name: postgresql
      state: started
````

## Inventory
By default, Ansible represents what machines it manages using a very simple INI file that puts all of your managed machines in groups of your own choosing.  

To add new machines, there is no additional SSL signing server involved, so there's never any hassle deciding why a particular machine didn’t get linked up due to obscure NTP or DNS issues.

So this is where we maintain the structure of our network environment. Here for example we have two nodes.
````
[webservers]
web1.machine
web2.machine
web3.machine

[databaseserver]
db1.machine

````
Under the webserver node we actually have the name of specific machines within that environment, so when we write our script we just have to refer to either webserver or databaseserver. So we
can just add the specific machine or ip address to the inventory list and then be able to just use the same playbook with the node specified.

Once inventory hosts are listed, variables can be assigned to them in simple text files (in a subdirectory called 'group_vars/' or 'host_vars/') or directly in the inventory file.

## Modules
Modules in ansible are the basis for constructing playbooks, and modules are categorised into
separate functions with specific keywords, for example there are modules which can handle cloud services to
window services and everything in between. This link leads to the documentation with an index of all the modules:
https://docs.ansible.com/ansible/latest/modules/modules_by_category.html
Each module also has parameters that must be used with the keyword of said module, here is a simple example,
imagine we want to use the 'File module', we can check which key words can execute commands associated with
the file module in the documentation. In this example lets use the keyword 'iso_extract' which has the
required parameters 'dest, files, image'. so the example playbook task with this module would be:
````
- name: Extract kernel and ramdisk from a LiveCD
  iso_extract:
    image: /tmp/rear-test.iso
    dest: /tmp/virt-rear/
    files:
    - isolinux/kernel
    - isolinux/initrd.cgz

````
