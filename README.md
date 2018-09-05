# Ansible

Requirements for running the staging code on your OSX laptop:

1) Virtual Box

2) Virtual Box Extension Pack (Required for PXE Booting)
   - https://www.virtualbox.org/wiki/Downloads

2) Vagrant

3) homebrew/fink
  - ansible
  - git
  
cd to the ansible git directory

```
vagrant plugin install vagrant-vbguest vagrant-share
```

Add the following to your hosts file:

```
172.16.0.254    ww-master.vagrant ww-master
```

Add your users pubkey {{ sshd_users_id_rsa_key }} to use to log in to the VM to the following files:

```
vagrant_playbook.yaml
ansible/inventories/staging/host_vars/ww-master.vagrant
```

This will use the Vagrant file to boot ALL of the nodes defined within it, or you can give it a subset by adding the name.
```
vagrant up
vagrant up ww-master
```

ww-master is the Warewulf master.

Once the machines are booted you can run Ansible on them.

```
ansible-playbook -i inventories/staging/hosts -u root ohpc_masters.yaml
```

This will log into the ww-master virutal nodes and try and make them a ohpc master for xcat and wareful.
If you want to limit the run to one host you can use "-l fc-master" for example. If you want to limit the
number of plays running you can use the "-t warewulf" tag to limit it to warewulf and its dependencies.

```
ansible-playbook -i inventories/staging/hosts -u root -t warewulf ohpc_masters.yaml
```

## Start over from scratch and destroy the VMs

```
vagrant destroy
```
