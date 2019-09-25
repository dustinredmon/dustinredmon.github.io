---
layout: post
title: Blog 03 - How to edit files with Ansible using lineinfile
---

This week I had to learn how to write playbooks for Ansible in my senior porject class. Writing playbooks is pretty straight forward. There are hundreds of modules you can use to accomplish the task you want to perform. However, some modules use regex, which stands for regular expression and can be quite confusing to understand. Ansible uses Python regular expressions to perform certain search fuctions in some of thier modules. The first time I saw the syntax for Python regular expressions it looked like Greek to me. 

  Here is an example of regular expression:
    Original text: server1.cyberciti.biz
    Regular expression: '(\s+)server1\.cyberciti\.biz(\s+.*)?$'

I mean seriously, what is that!? That looks like it was written by ancient alien civilization. Thankfully, I have figured out how to understand this jumbled mess of characters in symbols. How starters it help to have a Python regular expression cheat sheet. Before discovering this, I was pulling my hair out trying to find out what each character actually does. Because Python regular expressions use a lot of different symbols, google searchs for a single character was not very fruitful. Lucky for you, I found a cheat shee you can use to make your life 100 times easier. For the life of me, I cannot understand why Ansible doesnt include this in thier documentation.

  Python Regular Expression cheat sheet:
  https://www.debuggex.com/cheatsheet/regex/python
  
After familiarizing yourself with the cheat sheet above, let is go through an example of how you can use regular expression and lineinfile to edit a config file. In most cases, I think the example here is applicable in most situations.

  - name: Edit 000-default.conf
    lineinfile:
      path: /etc/apache2/sites-available/000-default.conf
      state: present
      regexp: '^\tDocumentRoot \/var\/www\/html'
      line: '	DocumentRoot /var/www/html/public'
      
So what we want to look at here is the entry for 'regex'. This is the expression that we are going to search for in the file. Note the '^' symbol and the '\' symbol that are being used. If you used the cheat sheet I provided, you will see that the '^' symbol is used to indicate where the string starts. The '\' is an escape character. An escape character is used whenever your expression includes a character that can be interpreted as a Python regular expression. So for this expression, I have to insert a '\' in front of every '/' I used in the string. The last thing to note in this regex is the use of '\t' at the beginning of the string. This is used to insert a Tab into your regular expression. My string included a tab space before it, so i had to use the '\t' to represent that space.
 
The entry for 'line' is the string of text that you will replace your expression if there is a match in your file. If a match does not exist, it will insert this line at the end of your file. If there are multiple matches, then lineinfile will replace the last match in the file. Notice that the string for 'line' does notrequire the use of regular expression. You can write this text as a normal string.
