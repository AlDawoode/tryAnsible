# Ansible

##### NOTE :
#
    - The host file in your project is the inventory file
    - Playbooks are written in YAML format, They are like a to-do list for 
      Ansible that contains a list of tasks.
    - Roles containing certain vars_files, tasks, and handlers based on which 
      it had declared. 
--------------------------------------------------------
### Installing Ansible

    # The most common and preferred way of installation
    $ pip install ansible

    # Install the epel-release RPM if needed on
    # CentOS, RHEL, or Scientific Linux
    $ sudo yum install epel-release
    $ sudo yum install ansible
    
    # You will need the PPA rep configured
    # sudo apt-get install ansible


### Ansible Ad Hoc
##### Ad-hoc commands are great for tasks you repeat rarely. For example, if you want to power off all the machines in your lab for Christmas vacation, you could execute a quick one-liner in Ansible without writing a playbook.

-a --> argument 

-m  --> module

-i --> inventory

-u --> username 

-b --> execute like root -- > sudo

    # aws is a group of hosts(my project), we can use all instead
    $ ansible -m ping aws -i hosts
    $ ansible -m ping all -i hosts
    $ ansible -m ping all -i hosts -u ec2-user
    
    #show info about disk space
    $ ansible -m shell -a 'df -h' -i hosts all    
    $ ansible -m shell -a 'hostname' -i hosts all     
    $ ansible -m shell -a 'whoami' -i hosts all     // who i  am  --> ec2-user
    $ ansible -m shell -b -a 'whoami' -i hosts all    // 

##### Example of install OR remove "httpd" by using ansible ad hoc :
    # present equals to install
    $ ansible aws -i hosts -m yum -a "name=httpd state=present" -b
    
    # absent equals to remove
    $ ansible aws -i hosts -m yum -a "name=httpd state=absent" -b

##### Example of getting information about the machine by using ansible ad hoc (setup module) :
##### NOTE :
    - The setup module get information about the remote machine and these info called "Ansible facts"

    $ ansible all -i hosts -m setup
    
##### Example of install git by using ansible ad hoc (apt or yum module) :
    $ ansible local -m apt -a "name=git" -i hosts -b
    $ ansible local -m yum -a "name=git" -i hosts -b
    $ ansible local -m yum -a "name=git" -i hosts -b --extra-vars "ansible_sudo_pass=XXXXX"

##### Example of copy file from the host to all node (copy moudle):
    $ ansible all -m copy -a "src=key.pem dest=~/FileName" -i hosts 
 



##### Example of write a new line in some file if the line doesn't exist (lineinfile moudle):
    $ ansible all -m lineinfile -a "path=/home/osboxes/Desktop/test-project/inventory.txt line=someText" -i           inventory.txt 

 
### Ansible inventroy
##### Examples of ansible inventroy file  :
    ## example 1:
    ## simple inentroy file
    server1.company.com
    server2.comapny.com
    
    [mail]
    server3.company.com
    server4.company.com
    
    [db]
    server5.company.com
    server6.company.com
    
    [web]
    server7.company.com
    server8.company.com
    
    ## We can define group of group by adding children file
    [all_servers:children]
    mail
    db
    web
    
    ----------------------------------------
    ## example 2:
    ## simple inentroy file using "alias"
    
    web ansible_host=server1.company.com
    db ansible_host=server2.company.com
    mail ansible_host=server3.company.com
    web2 ansible_host=server4.company.com
    
    ######################################### NOTE #########################################
    ## ansible_host is inventroy parameter to specify fqdn or ip address of a server
    ## other inventroy parameter:
    - ansible_connection 
    - ansible_port by default is set to 22 to ssh but if you want to change that for some reason you use this param.
    - ansible_ssh_pass define the ssh pass for linux
    - ansible_user define the user that want to make remote connection. example:
        ansible_user=root or ansible_user=admin 
    
    example on ansible_connection parameter
    web ansible_host=server1.company.com ansible_connection=ssh
    db ansible_host=server2.company.com ansible_connection=winrm
    mail ansible_host=server3.company.com ansible_connection=ssh    /// linux server
    web2 ansible_host=server4.company.com ansible_connection=winrm   /// windwos server
    localhost ansible_connection=localhost   // to indicate that we like to work with local host :) and not connect to  
    remote host.

### Ansible PlayBook
##### Example of simple playbook file:
    ## simple ansible playbook.yml
    ---
    -
      hosts: localhost
      name: Play1
      tasks:
       - name: Execute command "date"
         command: date
         
       - name: Execute script on server
         script: test_script.sh
         
    -
      hosts: localhost
      name: Play2
      tasks:
       - name: Install web service
         yum:
            name: httpd
            state: present
        
       - name: Start web server
         service:
            name: httpd
            state: started
     

##### Example of run the playbook file from the host machine :
    ## install_git.yml is the playbook file name
    ## hosts is the inventory file name
    $ ansible-playbook install_git.yml -i hosts
    
### Ansible Variables
## Note the format {{}} we are using to use variable is called Jinja2 Templating.
## Remember to enclose it  within quotes '{{}}'
## if the variable is in between a sentence then this is not required {{}}

##### Example of one variable inside the simple playbook file:
    # sample ansible playbook.yml
    -
      name: Add Dns server to resolv.conf
      hosts: localhost
      vars:
         dns_server: 10.1.250.10     //Declare the variable
      tasks:
         -lineinfile:
            path: /etc/resolv.conf
            line: 'nameserver 10.1.250.10'   // We can use the variable here by writing '{{dns_server}}' 

##### Example of variables inside the inventory file:
    # sample Inventory File
  
    server1 http_port=8081 snmp_port=161-162 inter_ip_range=192.0.2.0
    // we can use these variable inside the playbook file by writing '{{ variable name  }}'
    
    
##### The best example of variables is to writing them inside new file:

    Note: the file name should be same to the host name "server1.yml"  "local.yml" and so on
    # sample variable File
    
    server1 http_port=8081
    snmp_port=161-162 
    inter_ip_range=192.0.2.0
