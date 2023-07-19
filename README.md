# My homeserver playbook

A set of Ansible playbooks to deploy my homeserver.

## What *exactly* is that?

This repository contains a set of Ansible playbooks. I am using both singular and plural here because, to this day, I only own a small Raspberry Pi 4 as my homeserver, but I want this repo to contain all of my upcoming servers' playbook.

## Usage

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
