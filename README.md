ansible-hostname-example
========================

An example playbook for using Ansible [hostname](http://docs.ansible.com/ansible/hostname_module.html) module.

This example was created to reproduce the problem reported at [Hostname not set correctly on Ubuntu · Issue #2308 · ansible/ansible-modules-core](https://github.com/ansible/ansible-modules-core/issues/2308#issuecomment-149878918).

However I could not reproduce the problem.

## Setup

### Setup a vagrant Ubuntu box

Create a Ubuntu 14.04 box

```
vagrant up
```

Show ssh config to ssh this box.

```
vagrant ssh-config
```

The example output is:

```
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2200
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/hnakamur/work/ansible-hostname-example/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

Then edit the above config and add it to ~/.ssh/config.

* Change Host from `default` to `ubuntu`.
* Change HostName from `127.0.0.1` to `192.168.33.10`.
* Change Port from `2200` to `22` (The original port may be different).

```
Host ubuntu
  HostName 192.168.33.10
  User vagrant
  Port 22
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/hnakamur/work/ansible-hostname-example/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

### Setup Ansible environment on OS X

Install Python 2.x with [Homebrew](http://brew.sh/)

```
brew install python
```

Create a python virtual env to run Ansible.

```
virtualenv venv
```

Activate the virtual env. Note `(venv)` is added to the prompt.

```
$ source venv/bin/activate
(venv)$
```



Install github master branch of [ansible/ansible](https://github.com/ansible/ansible).

```
pip install git+https://github.com/ansible/ansible
```

## Run the playbook

### Check hostname before running the playbook

Ssh to the vagrant box.

```
vagrant ssh
```

Show the hostname.

```
$ hostname
vagrant-ubuntu-trusty-64
$ sudo hostname
vagrant-ubuntu-trusty-64
```

Add an entry to `/etc/hosts`. Actually it turns out that this is not needed.

```
sudo sh -c "echo '192.168.33.10 vagrant-ubuntu-trusty-64' > /etc/hosts"
```

On OSX, run the ansible playbook at the virtualenv `(venv)$` prompt.

```
(venv)$ ansible-playbook ubuntu.yml
```

On the ubuntu box, show the hostname again.

```
$ hostname
foo
$ sudo hostname
foo
```
