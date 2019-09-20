---
layout: post
title: Blog 02 - Learning YAML Syntax
---
Ansible playbooks are the files where Ansible code is written. The purpose of these files is to orchestrate what you want ansible to do. These are a set on instructions you provide Ansible to work it's magic. It can be as simple as running a series of commands or as complicated as running hundreds of VMs on a public/private cloud infrastructure. They are like a to-do list for Ansible that contains a list of tasks. Playbooks are run sequentially, and contain the steps which the user wants to execute on a particular machine. 

Playbooks are written in YAML format. YAML stands for Yet Another Markup Language. Before we start writing our first playbook, it is helpful to learn the proper YAML syntax. For Ansible, nearly every YAML file starts with a list. Each item in the list is a list of key/value pairs, commonly called a “hash” or a “dictionary”. So, we need to know how to write lists and dictionaries in YAML.

Spacing and indentation level is very important when writing YAML. Avoid using tabs over spaces when writing in YAML. Spacing is your friend! Tabs are not always  translated properly, and may cause issues with your code. So it is good practice to avoid using tabs alotgether. All members of a list are lines beginning at the same indentation level starting with a "- " (a dash and a space). In an Ansible playbook, it is common to see tasks ordered using this format to create a list:

  #Simple Ansible Playbook1.yml
  --- 
      name: Play1
      hosts: localhost
      tasks:
          - name: Execute command 'date'
            command: date
            
          - name: Execute script on server
            script: test_script.sh
            
          - name: Install httpd service
            yum:
                name: httpd
                state: present
                
           - name: Start web server
             service:
                 name: httpd
                 state: started
                
Notice how the file starts with 3 hyphens. A YAML file always starts with 3 hyphens. If you examine this playbook further, you will see how all the information is presented in a key/value pair. For instance, to specify host information, we entered localhost as the host we would like to use. In this line of code, host is the 'key', and localhost is the 'value'. Together they arrange information in a key/value pair. Notice the values assigned to the 'tasks' key. Since there is more than one task, we must create a list to include every operation we want to perform. Notice how each item on the list starts at the same indentation level, and starts with a "- ". Identation level is very important. If just one item is not properly indented it will be considered a child to the preious item on the list, rather than an actual item on the list.

After a little pratice, you will find that writing playbooks in ansible is quite easy. Just follow the guidelines specified in this blog, and remember to use spaces instead of tabs!
