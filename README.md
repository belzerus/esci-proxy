# Embedded Simple Continuous Integration (ESCI) Proxy Board

This repo contains ansible playbooks to setup, configure and maintain esci proxy boards,
that act as a proxy board to communicate with embedded devices for example within CI/CD.

One of the main purposes with this setup is to make it possible to access the embedded device
through serial interface in a simple way.

## Setup a new board

To setup a new board from scratch below steps is needed.

### Prepare target proxy board

**NOTE: Only tested on rpi using Raspberry Pi OS Lite (former Rasbian)**

 1. Install a linux system on the target (for example Rasbian if using a RPI device)
 2. Ensure that the target device has `ssh-server` and `python` installed
 3. Copy ssh-keys to target `ssh-copy-id user@target_ip`

### Prepare host system

 1. `pip install -r requirements.txt`
 2. Add the new target ip to 'unconfigured' section in inventories/servers, e.g.)

```
...
[unconfigured]
192.168.1.2
...
```

### Configure new board

Run playbook (if hostname is not set, default hostname on target will be used..)

```
ansible-playbook -i inventories/servers -u <target_username> site.yml -e "hostname=<hostname>"
```

When successful, the board will by default be configured with mdns and reachable using <hostname>.local
remove the target ip from `inventories/servers` **unconfigured** section and add either the local domain
or ip to the **configured** section.

```
[unconfigured]
...
[configured]
hostname.local
...
```

## Finalize installation on configured boards

When you have one-or-many boards configured, run through the entire playbook

```
ansible-playbook -i inventories/servers -u <target_usernam> site.yml
```

### Update and maintain existing boards

To update configured board with latest updates of the playbook run:

```
ansible-playbook -i inventories/servers -u <target_usernam> site.yml
```

You can also run manual commands on all configured boards with. This is useful for maintenance (upgrade)
and grabbing data etc.

```
ansible configured -i inventories/servers -m command -a "top -n 1" -u <target_username>
```
