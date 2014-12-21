Here’s how to build a repo on a Linux machine. This has been tested on the following distributions as destination servers (i.e., the server that will serve the repository):

* Amazon Linux (Redhat derived) in EC2
* CentOS 6.5 

This playbook runs locally and downloads the MapR repository, then produces a .repo file with ansible’s best guess at the correct IP for your server.

What you’ll need:

* ansible installed on your mirror server
* The playbook 
* An inventory file, containing the connection information of the host you wish to make the mirror
* Enough storage on /var/www/html to accommodate the repository. For v4.0.1 core and ecosystem, you currently need about 3.5GB.

Download and unpack this repository to the machine you wish to make the mirror host.

Once run, you will have a mirror in `/var/www/html/releases`.

Installing Ansible
===

Follow the below steps to install ansible in a [virtualenv](http://virtualenv.readthedocs.org/en/latest/virtualenv.html). This results in a python installation, along with ansible, installed under the directory containing the playbooks.

```
tar -vzxf mapr-repo-mirror.tar.gz
cd mapr-repo-mirror-master
yum -y install python-devel gcc make python-setuptools
easy_install virtualenv
virtualenv .venv
source .venv/bin/activate
pip install ansible
```

Confirm that ansible runs:

```
ansible --version
```

If this returns something that doesn't look like a stack trace, proceed.

Ansible Inventory
===

If you're dealing with a remote machine that you'll make your repository server, do the following. If not, the default `mirror.hosts` file will direct ansible to work locally to set up the mirror.

Create your inventory file (`mirror.hosts` in this example) to contain a like this, changing username and IP address to match your remote server’s details (again, do not do this if you want to set up the server you're already logged in to as your mirror):

```
mirror ansible_ssh_host=<ip_address> ansible_ssh_user=<username>
```

Test and Run the Playbook
===

Confirm you can connect to your mirror host (whether remote or local) with ansible:

```
ansible mirror -i mirror.hosts -m ping
```

Next, run the playbook. Note that the below will prompt you for the password that unlocks the playbook (it is encrypted because it contains a cleartext password):

```
ansible-playbook -si mirror.hosts mapr-repo-mirror.yml
```

Tarball generated using:

```
tar -cvzf mapr-repo-mirror.tar.gz --exclude .git mapr-repo-mirror
```

