# My homeserver playbook

A set of Ansible playbooks to deploy my homeserver.

## What *exactly* is that?

This repository contains a set of Ansible playbooks. I am using both singular and plural here because, to this day, I only own a small Raspberry Pi 4 as my homeserver, but I want this repo to contain all of my upcoming servers' playbook.

## Usage

First install roles:

```sh
ansible-galaxy install -r requirements.yml
```

This playbook requires a hard drive with two logical volumes named `ValfurSSD-data` and `ValfurSSD-docker--volumes`. You can check [Vagrant Disks](https://developer.hashicorp.com/vagrant/docs/disks). Here is my actual configuration:

```
sda                                    8:0    0 931.5G  0 disk
├─ValfurSSD-docker--volumes          253:1    0   150G  0 lvm  /var/lib/docker/volumes
└─ValfurSSD-data                     253:3    0 616.5G  0 lvm  /data
```

The [inventories](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html) contain my Raspberry Pi 4 (`pi.valfur.fr`) as well as a `vagrant-rpi4` server, which is a [Vagrant](https://www.vagrantup.com/) box that allows to test the playbook during development.

To deploy to the real production server:

```sh
ansible-playbook -K -i inventory/production playbook-rpi4.yml
```

To deploy to the development server (after having started the Vagrant box):

```sh
ansible-playbook -i inventory/development playbook-rpi4.yml
```

> For development, you should disable strict host key checking, but you will soon encounter a bug.
> Checkout [Troubleshooting section](#The-playbook-failse-on-a-task-that-uses-SSH-includes-SSH-Git-cloning).

## Troubleshooting

### The playbook fails on a task that uses SSH (includes SSH Git cloning)

This is a known bug, that seems to not be fixed yet: https://github.com/ansible/ansible/issues/9442.

I will explain the problem. When the host key changes (e.g. you destroy and recreate the development Vagrant box), you will see that famous warning:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
...
```

But that's not a big deal because you don't care (**not in production right??**) and you know what you are doing so you just disabled strict host key checking in with the environment variable `ANSIBLE_HOST_KEY_CHECKING=False`. So what is the problem?

Well, even though you disabled strict host key checking, if the host key is invalid, SSH agent forwarding won't work. That means that your SSH keys won't be forwarded and you won't be able to connect to remote hosts (or SSH clone Git repositories).

Now the workaround (I can't call that a solution nor a solution), run your playbook as follows:

```sh
ANSIBLE_SSH_ARGS='-o ForwardAgent=yes -o UserKnownHostsFile=/dev/null' ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ...
```

I prefere to put these options in environment variable because I don't want to globally use them: in a real-world situation, you really want to keep these options off. But it forces me to repeat the `ForwardAgent` option (environment variables overwrite the ones set in `ansible.cfg`).
