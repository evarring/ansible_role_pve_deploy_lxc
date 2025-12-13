pve_deploy_lxc
=========

Ansible role to deploy, configure and destroy lxc containers on Proxmox VE

More information on the variable options on the URLs below.

<https://galaxy.ansible.com/ui/repo/published/community/proxmox/content/module/proxmox/>
<https://galaxy.ansible.com/ui/repo/published/community/proxmox/content/module/proxmox_template/>

Requirements
------------

- Proxmox VE api token
- LXC template - feel free to choose the lxc container image from <https://images.linuxcontainers.org/> and make sure you get the cloud one as it has cloud-init package installed.
  You can use the download tag and pve_deploy_lxc_dl_template variable to automate it.
- proxmoxer python package
- community.proxmox collection

Role Variables
--------------

Proxmox VE host connection variables:

```yaml
pve_deploy_lxc_proxmox_api_ip: "10.0.0.10"
pve_deploy_lxc_proxmox_api_token_user: "root@pam"
pve_deploy_lxc_proxmox_api_token_id: "proxmox"
pve_deploy_lxc_proxmox_api_token_secret: dy8293yruqewbfijbaifb389rfbe
pve_deploy_lxc_node: "nodename01"
```

LXC container template download variable:

```yaml
pve_deploy_lxc_dl_template:
  storage: local # Proxmox VE storage ID which has Container template storage option
  url: "https://images.linuxcontainers.org/images/almalinux/9/amd64/cloud/20250729_23:08/rootfs.tar.xz"
  template: "lxc-almalinux9.tar.xz" # the final template filename on the Proxmox VE host
  force: false # set to true if you want to overwrite a template
```

LXC container variables:

```yaml
pve_deploy_lxc_container:
  hostname: "hostname.fqdn.net"
  vmid: 100
  password: "{{ ansible_password }}"
  ostemplate: "local:vztmpl/lxc-almalinux9.tar.xz.tar.xz"
  description: "Example LXC container deployment"
  pool: containers
  searchdomain: "fqdn.net"
  tags: lxc
  nameserver: "1.1.1.1 8.8.8.8"
  netif:
    net0: "name=eth0,gw=192.168.0.1,ip={{ ansible_host }}/24,bridge=vmbr0,tag=69"
  pubkey:
    - "{{ lookup('file', 'files/lxccontainer.pub') }}"
  disk_volume:
    storage: local-lvm
    size: 10
```

Available tags:

- deploy - creates the container
- start - starts the container
- stop - stops the container
- redeploy - stops, destroys and creates the container
- destroy - stops and destroys the container
- download - downloads the template

Example Playbook
----------------

```yaml
---
- name: Deploy LXC containers
  hosts: targetlxccontainer
  become: false
  gather_facts: false

  tasks:
    - name: Import role
      ansible.builtin.include_role:
        name: pve_deploy_lxc
      tags: always
...
```

License
-------

MIT
