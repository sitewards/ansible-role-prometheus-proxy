# Ansible Prometheus exposition role

This is the Ansible Prometheus reverse proxy exposition role. It's designed for consumption by playbooks, not for
consumption by itself. It adds additional configuration that allows the exporters to be exposed on a public interface
with the nginx or apache webservers

## Justification

Sitewards operates across a heterogeneous set of hardware on behalf of it's clients. There is not a centralised discovery
mechanism, nor a "secure network" behind which metrics can be implemented. Unfortunately, metrics must be exposed
to the public web.

This information is somewhat sensitive. Accordingly, it's not desirable to make it universally available. Thus, this role
takes care of proxying the information behind basic auth and HTTPS. This secure tunnel over which to transfer the HTTP
data prevents unauthorized access.

## Requirements

- Internet Access

### Integrations

This has been tested with the following additional roles:

- geerlingguy.nginx
- geerlingguy.apache

It is not guaranteed to work with anything else.

### Apache

The module requires the following extensions to be enabled:

- proxy
- proxy_http
- proxy_http2

## Warnings

The way this works is to bind to a public interface, on the same port that the node exporter is listening to on the
loopback. This works fine, however **IF YOUR IP REGULARLY CHANGES THIS WILL BREAK YOUR WEBSERVER**. So, if you're
behind NAT with DHCP and are continually reallocated addresses, this is a bad idea -- Use another mechanism.


There are two ways to install this role:

## Installation

### Ansible Galaxy (recommended)

```bash
$ cd path/to/playbook/root
$ cat >> requirements.yaml <<EOF
- src: "https://github.com/sitewards/ansible-role-prometheus-proxy"
  version: "master" # <----- Update this to a stable version
  name: "sitewards.prometheus-proxy"
EOF
$ ansible-galaxy install -r requirements.yaml
```

### Git Submodules

```
$ cd path/to/playbook/root
$ mkdir roles/
$ git submodule add https://github.com/sitewards/ansible-role-prometheus-proxy.git  roles/sitewards.prometheus-proxy
```

## Usage

Include this in another ansible playbook. For sample, consider a generic server playbook:

```
---
# $PLAYBOOK_ROOT/server.yaml
- name: "server"
  hosts: all
  become: true
  become_user: "root"
```

Add the reference for the role:

```
# $PLAYBOOK_ROOT/server.yaml
# ...
become_user: "root"
roles
  - "sitewards.prometheus-proxy"
```

This should work!

## Configuration

The variables that are available are defined in defaults/main.yml

## Contact

https://www.sitewards.com
