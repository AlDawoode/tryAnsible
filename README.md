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
 
 
 
### Ansible inventroy
##### Examples of ansible inventroy file  :
    ## example one:
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
    ## example one:
    ## simple inentroy file using "alias"
    
    web ansible_host=server1.company.com
    db ansible_host=server2.company.com
    mail ansible_host=server3.company.com
    web2 ansible_host=server4.company.com
    

### Ansible PlayBook
##### Example of run the playbook file from the host machine :
    ## install_git.yml is the playbook file name
    ## hosts is the inventory file name
    $ ansible-playbook install_git.yml -i hosts
