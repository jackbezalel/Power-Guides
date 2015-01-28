# Installing and Using Ansible on Redhat 7 Power Guide

##### #*RESOURCE: http://docs.ansible.com/intro_installation.html*

##### #OnMaster #TODO: Install the control machine (no need for DB or agents at all)

	sudo easy_install pip

	sudo pip install ansible


#OnSLAVES #TODO: Install Python 2.6 or supported newer on Controlled Machines or compile from source as described here

	#Failed: Failed installing on REDHAT EL4 (even with EPEL repository)
	# Tried to setup pip to install Python since EL4's latest python is v2.3.x
	#RESOURCE: https://pip.pypa.io/en/latest/installing.html#python-os-support
	# Had to install pre-req for yum, yum, setup yum.conf to get pip installed via yum (python-pip)
	# Still pip failed to run since its pre-req was python > 2.3 :-(
	# So decided to compile python 2.6 from sources

	#INSTEAD:
	#RESOURCE: https://www.python.org/download/releases/2.6/Python-2.6.tgz MD5:d16d29a77db2cd3af882a591f431a403

	cd /tmp
	mkdir python26
	cd python26
	wget https://www.python.org/download/releases/2.6/Python-2.6.tgz
	gzip -d gzip -d Python-2.6.tgz
	tar xfp Python-2.6.tar
	Python-2.6
	./configure
	make
	make install

	#WARNING: Failed to find the necessary bits to build these modules:
	# _sqlite3           bsddb185           sunaudiodev
	# To find the necessary bits, look in setup.py in detect_modules() for the module's name.

#OnMaster #OnSLAVES #TODO: create ansible user ON ALL MACHINES

	useradd ansible

	# Enable sudo

	echo "ansible ALL=(ALL) ALL" >> /etc/sudoers

#TODO: #onMaster

	mkdir /etc/ansible/

	# Edit /etc/ansible/hosts
	#RESOURCE: http://docs.ansible.com/intro_inventory.html
	# Sample file:
	#
		master ansible_connection=local
		controlled1 ansible_connection=ssh ansible_ssh_private_key_file=/home/ansible/.ssh/id_rsa ansible_python_interpreter=/usr/local/bin/python


	su - ansible
	ssh-keygen # decide about the password for your ansible user cert file

	# Copy the contents of #MASTER ~ansible/.ssh/id_rsa.pub into each controlled machine's ~ansible/.ssh/authorized_keys

	# Test connection using ssh

	ssh controlled1

	#NOTE This will require the password entered for the ansible user cert

	# Test connection via ansible

	ansible controlled1 -m ping


	#NOTE It looks like ansible will "cache" the cert password for future invocations (persistent across logout and re-login, and fades away only after reboot)

	# To avoid entering the password you can use "ssh-agent" and "ssh-add"

	#

	