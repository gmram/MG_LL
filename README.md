# LearningLocker on CentOS 7.3
 gmram

 This is straight fork of David Pesce's development environment for LL. I have forked it so I can experiment. If you need to install LL  on CentOS (I've done it on a DO droplet), please please refer to https://github.com/davidpesce/learninglocker-v1-dev-env for the instructions. 
 
 I've detailed out how to install Ansible & Vagrant on a DO droplet, assuming you are the root user. If you aren't, please append 
 `sudo` to each command

# Thank you David! :)


The main intent of this repository is for developers of LearningLocker (v1) to rapidly create a local instance of LearningLocker. 

This project contains [Vagrant](https://www.vagrantup.com/) and [Ansible](http://www.ansible.com/) scripts to provision [CentOS 7.3](https://www.centos.org/) machines with [LearningLocker](http://learninglocker.net/). 

This LearningLocker installation will depend on [Apache2](http://httpd.apache.org/) and [MongoDB](https://www.mongodb.org/).

## Preparation
1. Download and install Vagrant: https://www.vagrantup.com/downloads.html
https://linuxconfig.org/vagrant-installation-on-centos-linux-system

As of March 2017, this corresponds to:

`wget -q https://releases.hashicorp.com/vagrant/1.9.3/vagrant_1.9.3_x86_64.rpm
 yum localinstall vagrant_1.9.3_x86_64.rpm`

Now, install dkms & then virtualbox as a provider for Vagrant:
(https://jonathansblog.co.uk/installing-vagrant-on-centos-7)

`yum install dkms` 

`cd /etc/yum.repos.d
wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo`

run system update

`yum update`
`yum provides virtualbox`

Copy the filename in the last entry and use it to install virtualbox. In my case (March 2017), this was:

`yum install VirtualBox-5.1-5.1.18_114002_el7-1.x86_64`


2. Install Ansible: http://docs.ansible.com/ansible/intro_installation.html
   * If on Mac with Homebrew installed: http://brewformulas.org/Ansible

(https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-centos-7)

To do this, first install red hat EPEL using the following command:
 `yum install epel-release`
 
 Now, update your system using
 `yum update`
 
 Now, install Ansible using
 
 `yum install ansible`
 
## Install Apache
`yum install httpd`

## Install Mongodb
https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-red-hat/

`nano  /etc/yum.repos.d/mongodb-org-3.0.repo`

Paste the following in the file:

`[mongodb-org-3.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.0/x86_64/
gpgcheck=0
enabled=1`

Now install Mongodb
`yum install -y mongodb-org`


## Start httpd & mongodb

`systemctl enable httpd.service` to start apache everytime the server restarts
`sudo service mongod start` to start mongodb
`sudo chkconfig mongod on` to start mongodb everytime the server restarts

## Clone this project onto your server
 
 Step 1: Install git
 
 `yum install git`
 
 Now, change your directory to /var/www
 
 `cd /var/www`
 
 clone the git project using this command:
 
 `git clone https://github.com/davidpesce/learninglocker-v1-dev-env.git`
 
  ## Configuration

1. Copy _vars.secret.yml.edit_ file to  _vars.secret.yml_.
2. Edit the _vars.secret.yml_ file following the instructions detailed in the file.
3. Modify the learninglocker file in the _group\_vars_ directory by specifying the ll_repo and ll_repo_version.

If you need to use a proxy, please see the _proxy_ fork of this repository.

## Typical usage

This section explains two use cases of this project:

 1. Create one VM machine with LL installed.
 2. Create two VM machines with LL installed: one for the webserver and the other for the database.

### Use case 1. Create one VM machine

To create a virtual machine and provision it with LearningLocker, simply run:

    vagrant up

If the installation is completed successfully, you will be able to:

 * Access LL through your web browser: http://localhost:8082
 * Connect to the machine: ```vagrant ssh```

### Use case 2. Create two VM machines

To create a virtual machine and provision it with LearningLocker, modify the _Vagrantfile_:
From this:

    machines_file ||= './vagrant/one_machine'
    
To this:

    machines_file ||= './vagrant/two_machines'

If the installation is completed successfully, you will be able to:

 * Access LL through your web browser: http://localhost:8082
 * Connect to the machines:
  * ```vagrant ssh ll-web```
  * ```vagrant ssh ll-db```
    
  
# Acknowledgements
To create this I relied heavily on the Vagrant/Ansible playbook written by Aitor Gómez-Goiri that can be found here: https://github.com/gomezgoiri/learninglocker-centos7 
