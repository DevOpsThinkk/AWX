Good morning all
I have been with IBM for mor than 10 years, I know both Ansible and terraform but expecting an advanced level concepts like error handling and etc.
===============================================================================================
sathishbob@gmail.com
===============================================================================================

Facts: Global variables containing information about the system, like network interfaces or operating system.

Play - Execution of a playbook is called a play

User-8
Suman - 20
===============================================================================================

ssh -i "trainee08.pem" centos@ec2-34-236-134-247.compute-1.amazonaws.com
Venkata

https://puttygen.com/download.php?val=49
sathishbob  11:36 AM
sudo su
yum update -y
user name : centos
yum install epel-release -y
yum install ansible -y
hostnamectl set-hostname controller.ibmtraining.com
exec bash
ssh-keygen
cat /root/.ssh/id_rsa.pub
chmod -R 700 .ssh  # Witeable only by the owner

echo > /etc/ansible/hosts (edited) 
vi /etc/ansible/hosts
<centos node ip> ansible_ssh_user=ansible 

https://docs.ansible.com/ansible/2.8/modules/list_of_all_modules.html
ansible all -m ping
ansible all -a "uptime"
ansible all -a "free -h"
ansible all -a "reboot"
ansible all -b -a "reboot"

# Ubuntu
sudo su
apt-get update -y
hostnamectl set-hostname ubuntu.ibmtraining.com
exec bash
useradd -m ansible
visudo
ansible ALL=(ALL)       NOPASSWD: ALL
ctrl + o
enter key
ctrl+x (edited) 
su ansible
cd
mkdir .ssh
vi .ssh/authorized_keys
chmod -R 700 .ssh

From controller
ssh ansible@<public ip of the ubuntu node>
exit
vi /etc/ansible/ansible.cfg
vi /etc/ansible/hosts
ansible all -m ping
cd
yum install git -y
https://github.com/sathishbob/devops-starter.git

GitHub
sathishbob/devops-starter
A DevOps Starter Pack - ansible dynamic inventory - sathishbob/devops-starter

yum install python-pip
pip install --upgrade pip
pip install virtualenv
virtualenv venv
source venv/bin/activate

pip install -r devops-starter/ansible/requirements.txt

export AWS_ACCESS_KEY_ID=AKIAU4VT5HAC3QQS7QFN
export AWS_SECRET_ACCESS_KEY=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
export EC2_INI_PATH=/root/devops-starter/ansible/inventory/ec2.ini
export ANSIBLE_INVENTORY=/root/devops-starter/ansible/inventory/ec2.py
chmod +x /root/devops-starter/ansible/inventory/ec2.py

/root/devops-starter/ansible/inventory/ec2.py
================================================================================
cat /etc/ansible/hosts

[linux]
54.193.42.39
54.151.47.220

# File Create

cat filecreate.yaml
---
- hosts: linux
  tasks:
  - name: Create file
    file:
      path: /home/ansible/testfile
      state: touch


ansible-playbook filecreate.yaml

ansible all -a "ls -l"

================================================================================
# File and directory create

cat filecreate.yaml
---
- hosts: linux
  tasks:
  - name: Create file
    file:
      path: /home/ansible/testfile
      state: touch
  - name: Create Directory
    file:
      path: /home/ansible/testdir
      state: directory


ansible all -a "ls -l"

cat apache.yaml
---
- hosts: linux
  become: yes
  tasks:
  - name: install apache on ubuntu
    apt: name=apache2 update_cache=yes state=latest
    when: ansible_os_family == "Debian"
  - name: start apache on ubuntu
    service: name=apache2 enabled=yes state=started
    when: ansible_os_family == "Debian"
  - name: install apache on centos
    yum: name=httpd update_cache=yes state=latest
    when: ansible_os_family == "RedHat"
  - name: start apache on centos
    service: name=httpd enabled=yes state=started
    when: ansible_os_family == "RedHat"


ansible-playbook apache.yaml
================================================================================
# Using Template

cat index.html.j2
<html><center>
<h1> This system is running {{ ansible_os_family }} </h1>
<h2> System hostname is {{ ansible_hostname}} </h2>
</center></html>

cat apache.yaml
---
- hosts: linux
  become: yes
  tasks:
  - name: install apache on ubuntu
    apt: name=apache2 update_cache=yes state=latest
    when: ansible_os_family == "Debian"
  - name: start apache on ubuntu
    service: name=apache2 enabled=yes state=started
    when: ansible_os_family == "Debian"
  - name: install apache on centos
    yum: name=httpd update_cache=yes state=latest
    when: ansible_os_family == "RedHat"
  - name: start apache on centos
    service: name=httpd enabled=yes state=started
    when: ansible_os_family == "RedHat"
  - name: cretae index file using template
    template:
      src: /root/playbooks/index.html.j2
      dest: /var/www/html/index.html


ansible-playbook apache.yaml
===================================================================================
# Handlers

cat apache.yaml

---
- hosts: linux
  become: yes
  tasks:
  - name: install apache on ubuntu
    apt: name=apache2 update_cache=yes state=latest
    when: ansible_os_family == "Debian"
  - name: start apache on ubuntu
    service: name=apache2 enabled=yes state=started
    when: ansible_os_family == "Debian"
  - name: install apache on centos
    yum: name=httpd update_cache=yes state=latest
    when: ansible_os_family == "RedHat"
  - name: start apache on centos
    service: name=httpd enabled=yes state=started
    when: ansible_os_family == "RedHat"
  - name: cretae index file using template on ubuntu
    template:
      src: /root/playbooks/index.html.j2
      dest: /var/www/html/index.html
    notify: restart apache2
    when: ansible_os_family == "Debian"
  - name: cretae index file using template on centos
    template:
      src: /root/playbooks/index.html.j2
      dest: /var/www/html/index.html
    notify: restart httpd
    when: ansible_os_family == "RedHat"
  handlers:
    - name: restart apache2
      service: name=apache2 state=restarted
    - name: restart httpd
      service: name=httpd state=restarted

==========================================================================================
# Loop Conditions

cat loop.yaml
---
- hosts: linux
  tasks:
  - name: create multiple directory
    file:
      path: "{{ item }}"
      state: directory
    with_items:
    - /home/ansible/folder1
    - /home/ansible/folder2
    - /home/ansible/folder3
    - /home/ansible/folder4

==========================================================================================
# Nested Loop Conditions

cat nested.yaml
---
- hosts: linux
  tasks:
  - name: print nested loop
    debug: msg=" ansible {{ item[0] }} on day {{ item[1] }} is {{ item[2] }}"
    with_nested:
    - [ 'training', 'lab', 'handson' ]
    - [ 1, 2, 3 ]
    - [ 'good', 'bad', 'great' ]
==========================================================================================
# show messages

cat register.yaml
---
- hosts: linux
  tasks:
  - name: print std output
#    command: echo "i am in training"
    shell: echo #HOSTNAME
    register: output
  - debug: msg="standard output={{output.stdout}}"
  - debug: msg="standard error={{output.stderr}}"
==========================================================================================
# Ignore error and show the error message

cat register.yaml

---
- hosts: linux
  ignore_errors: yes
  tasks:
  - name: print std output
    shell: ls -l test.txt
    register: output
  - debug: msg="standard output={{output.stdout}}"
  - debug: msg="standard error={{output.stderr}}"

==========================================================================================
# Show Custom Messages

cat custommessage.yaml
---
- hosts: linux
  tasks:
  - name: print file content
    command: cat testfile
    register: filecontent
  - debug: msg="File is empty"
    when: filecontent.stdout==""
  - debug: msg="file is not empty"
    when: filecontent.stdout!=""

==========================================================================================
# Inline File

---
- hosts: linux
  tasks:
  - name: create file
    file:
      path: /home/ansible/hostname.conf
      state: touch
  - name: add a line if not present
    lineinfile:
      dest: /home/ansible/hostname.conf
      line: Hostname={{ ansible_hostname }}
      state: present


ansible all -a "cat hostname.conf"
==========================================================================================
# Find and replace regular expressions

cat lineinfile.yaml
---
- hosts: linux
  tasks:
  - name: create file
    file:
      path: /home/ansible/hostname.conf
      state: touch
  - name: add a line if not present
    lineinfile:
      path: /home/ansible/hostname.conf
      regexp: '^Hostname'
      line: hostname={{ ansible_hostname }}
      state: present

==========================================================================================

cat program.sh
#!/bin/bash
HOST=
echo "Hostname of the server is $HOST"
==========================================================================================
# Copy a file and change the lineinfile
cat application.yaml
---
- hosts: linux
  tasks:
  - name: upload the application to host
    copy:
      src: /root/playbooks/program.sh
      dest: /home/ansible/program.sh
      mode: 0755
  - name: modify the script
    lineinfile:
      state: present
      dest: /home/ansible/program.sh
      regexp: '^HOST='
      line: HOST={{ ansible_hostname }}
  - name: run the script
    shell: /home/ansible/program.sh
    register: output
  - debug: msg=" Script output is {{ output.stdout }}"

==========================================================================================
# Download a file from internet

 cat download.yaml
---
- hosts: linux
  tasks:
  - name: download file
    get_url:
      url: https://storage.googleapis.com/kubernetes-release/release/v1.19.0/bin/linux/amd64/kubectl
      dest: /home/ansible/kubectl
      mode: 0755

==========================================================================================
# Usage of Tags

cat mariadb.yaml
---
- hosts: 54.183.197.194
  become: yes
  tasks:
  - name: install mariadb server
    yum:
      name=mariadb-server
      state=latest
    tags:
    - install
  - name: start maria db
    service:
      name=mariadb
      state=started
    tags:
    - start


ansible-playbook mariadb.yaml --tags "install"
ansible-playbook mariadb.yaml --tags "install,start"
==========================================================================================
# Skip tags

cat mariadb.yaml
---
- hosts: 54.183.197.194
  become: yes
  tasks:
  - name: install mariadb server
    yum:
      name=mariadb-server
      state=latest
    tags:
    - install
  - name: start maria db
    service:
      name=mariadb
      state=started
    tags:
    - start
  - name: add user
    user:
      name=testuser

ansible-playbook mariadb.yaml --skip-tags "install"

==========================================================================================
# Skip specific task

cat mariadb.yaml
---
- hosts: 54.183.197.194
  become: yes
  tasks:
  - name: install mariadb server
    yum:
      name=mariadb-server
      state=latest
    tags:
    - install
    - all
  - name: start maria db
    service:
      name=mariadb
      state=started
    tags:
    - start
    - all
  - name: add user
    user:
      name=testuser

ansible-playbook mariadb.yaml --tags "all" --skip-tags "install"
==========================================================================================
# Execute only tagged and untagged tasks

ansible-playbook mariadb.yaml --tags tagged
ansible-playbook mariadb.yaml --tags untagged
==========================================================================================

# 22 precedences

command line values (for example, -u my_user, these are not variables)
role defaults (defined in role/defaults/main.yml) 1
inventory file or script group vars 2
inventory group_vars/all 3
playbook group_vars/all 3
inventory group_vars/* 3
playbook group_vars/* 3
inventory file or script host vars 2
inventory host_vars/* 3
playbook host_vars/* 3
host facts / cached set_facts 4
play vars
play vars_prompt
play vars_files
role vars (defined in role/vars/main.yml)
block vars (only for tasks in block)
task vars (only for the task)
include_vars
set_facts / registered vars
role (and include_role) params
include params
extra vars (for example, -e "user=my_user")(always win precedence)
==========================================================================================
# Variables are case sensitive

cat playbookvar.yaml
---
- hosts: linux
  vars:
    test: welcome to training
    Test: testing
  tasks:
  - name: print variab;e
    debug:
      msg="{{ test }}"
  - name: print another variable
    debug:
      msg="{{ Test }}"

ansible-playbook playbookvar.yaml

==========================================================================================
cat listvariables.yaml
---
- hosts: linux
  vars:
    teams:
      - csk
      - srh
      - mi
      - rcb
  tasks:
  - name: pring list variables
    debug:
      msg="{{ item }}"
    with_items:
      - "{{ teams }}"

==========================================================================================
# LIST VARIABLE

cat listvariables.yaml
---
- hosts: linux
  vars:
    teams: [csk, srh, mi, rcb]
  tasks:
  - name: pring list variables
    debug:
      msg="{{ item }}"
    with_items:
      - "{{ teams }}"
==========================================================================================
# Key and value variable

cat dictvaraible.yaml
---
- hosts: linux
  vars:
    training:
      topic: ansible
      trainer: sathish
      lab: aws
  tasks:
  - name: print dict variable
    debug:
      msg: "key is {{ item.key }} and value is {{ item.value }}"
    with_dict: "{{ training }}"


==========================================================================================
cat hostvars.yaml
---
- hosts: linux
  tasks:
  - name: print host variable
    debug:
      msg="http posrt no is {{ http_port }}"


cat /etc/ansible/hosts

[linux]
54.183.197.194 http_port=80
3.101.42.77 http_port=81

ansible-playbook hostvars.yaml

==========================================================================================

cat /etc/ansible/hosts

[linux]
54.183.197.194 http_port=80
3.101.42.77 http_port=81
[linux:vars]
http_port=8080

==========================================================================================
cat hostvars.yaml
---
- hosts: linux
  tasks:
  - name: print host variable
    debug:
      msg="http posrt no is {{ http_port }}"
  - name: print group vars
    debug:
      msg=" group port no is {{ port }}"

cat /etc/ansible/hosts
[linux]
54.183.197.194 http_port=80
3.101.42.77 http_port=81
[linux:vars]
port=8080
==========================================================================================

cat useradd.yaml
---
- hosts: linux
  vars:
    username: testuser
  become: yes
  tasks:
  - name: cretae user
    user:
      name: "{{ username }}"
      state: present
      shell: /bin/bash

ansible-playbook useradd.yaml
ansible-playbook useradd.yaml --extra-vars "username=test"

==========================================================================================
# extra vars will have highest preference and overtake the vars
cat useradd.yaml
---
- hosts: linux
  vars:
    username: testuser
  become: yes
  tasks:
  - name: cretae user
    user:
      name: "{{ username }}"
      state: present
      shell: "{{ shell }}"


ansible-playbook useradd.yaml --extra-vars "username=test shell=/bin/sh"

ansible-playbook useradd.yaml --extra-vars "username=test" --extra-vars "shell=/bin/bash"
	
==========================================================================================
# Custom Setup file

cat datetime.sh.j2
#!/bin/bash
DATE=`date`
echo "{\"date\" : \"${DATE}\"}"

cat customfact.yaml
---
- hosts: linux
  become: yes
  tasks:
  - name: create fact directory
    file:
      path=/etc/ansible/facts.d
      recurse=yes
      state=directory
  - name: create fact script using template
    template:
      src=/root/playbooks/datetime.sh.j2
      dest=/etc/ansible/facts.d/date_time.fact
      mode=0755
  - name: reload ansible fact
    setup: filter=ansible_local
  - name: print local fact
    debug:
      msg: " custom fact is {{ ansible_local.date_time }}"

ansible all -m setup -a "filter=ansible_local 
==========================================================================================
# Vault

ansible-vault create sensitive.yaml
---
- hosts: linux
  tasks:
  - name: create a sensitive file
    copy:
      content="This file contains sessitive information"
      dest=/home/ansible/sensitive.txt

ansible-vault edit sensitive.yaml

ansible-playbook sensitive.yaml --ask-vault-pass

==========================================================================================
# Ansible Role

cat /etc/ansible/roles/apache/tasks/install.yml
---
- name: install http package
  yum: name=httpd state=latest

cat /etc/ansible/roles/apache/tasks/configure.yml
---
- name: copy index html
  copy:
    src=files/index.html
    dest=/var/www/html/index.html
  notify:
  - restart apache

cat /etc/ansible/roles/apache/tasks/service.yml
---
- name: start and enable apache
  service: name=httpd state=started enabled=yes

cat /etc/ansible/roles/apache/tasks/main.yml
---
# tasks file for /etc/ansible/roles/apache
- import_tasks: install.yml
- import_tasks: configure.yml
- import_tasks: service.yml

 /etc/ansible/roles/apache/handlers/main.yml
---
# handlers file for /etc/ansible/roles/apache
- name: restart apache
  service: name=httpd state=restarted

/etc/ansible/roles/apache/files/index.html
Home page for apache role

cat apacherole.yaml
---
- hosts: <centos ip>
  become: yes
  roles:
  - apache

==========================================================================================
# Ansible tower

yum install -y epel-release

yum groups install -y "Development Tools"

yum install -y jq python2-pip yum-utils device-mapper-persistent-data lvm2 python-devel

yum update

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum -y install docker-ce

systemctl enable docker && systemctl start docker

LATEST_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | jq -r '.tag_name')

curl -L "https://github.com/docker/compose/releases/download/$LATEST_VERSION/docker-compose-$(uname -s)-$(uname -m)" > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

pip install --upgrade pip

pip install ansible

pip install wheel

pip install docker-compose --ignore-installed

cd

curl -L -o ansible-awx-11.0.0.tar.gz https://github.com/ansible/awx/archive/11.0.0.tar.gz

tar xvfz ansible-awx-11.0.0.tar.gz

rm -f ansible-awx-11.0.0.tar.gz

cd awx-11.0.0/

sed -i "s|^dockerhub_base=ansible|#dockerhub_base=ansible|g" installer/inventory

mkdir -p /opt/awx-psql-data

sed -i "s|^postgres_data_dir.*|postgres_data_dir=/opt/awx-psql-data|g" installer/inventory

mkdir -p /etc/awx-ssl/

openssl req -subj '/CN=secops.tech/O=Secops Tech/C=TR' -new -newkey rsa:2048 -sha256 -days 1365 -nodes -x509 -keyout /etc/awx-ssl/awx.key -out /etc/awx-ssl//awx.crt

cat /etc/awx-ssl/awx.key /etc/awx-ssl/awx.crt > /etc/awx-ssl/awx-bundled-key.crt

sed -i -E "s|^#([[:space:]]?)ssl_certificate=|ssl_certificate=/etc/awx-ssl/awx-bundled-key.crt|g" installer/inventory

cd ..

curl -L -o awx-logos.tar.gz https://github.com/ansible/awx-logos/archive/master.tar.g

tar xvfz awx-logos.tar.gz

mv awx-logos-master awx-logos

rm -f *awx*.tar.gz

cd awx-11.0.0/

sed -i -E "s|^#([[:space:]]?)awx_official=false|awx_official=true|g" installer/inventory

sed -i "s|^admin_user=.*|admin_user=awx-admin|g" installer/inventory

sed -i "s|^admin_password=.*|admin_password=CHANGE_ME|g" installer/inventory

cd installer

ansible-playbook -i inventory install.yml

awx-admin / CHANGE_ME

uncommet this line dockerhub_base=ansible

file awx-11.0.0/installer/inventory

mv /etc/ansible/roles/ .

cat /root/.ssh/id_rsa
==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================

==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================


==========================================================================================

==========================================================================================

ANSIBLE.CFG --> CHANGE REMOTE USER TO ANSIBLE
hostnamectl set-hostname ubuntu08
exec bash
==========================================================================================

- name: Import a key from a url
  rpm_key:
    state: present
    key: http://apt.sw.be/RPM-GPG-KEY.dag.txt
