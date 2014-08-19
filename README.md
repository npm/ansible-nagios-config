Ansible-Nagios-Config
====================

Before going any further, this role is designed to be run on servers that
have been provisioned using the npm [Nagios Role](http://github.com/npm/ansible-nagios).
Provision a new server using this role.

What is This?
------------

Nagios is a pain to configure, Ansible-Nagios-Config alleviates this by:

* providing sane default settings for hosts/services.
* automating the generation of configuration, through a simple DSL.

Usage
-----

* Install [librarian-ansible](https://github.com/bcoe/librarian-ansible) a tool for
managing your third-party Ansible dependencies.
* Provision a new Nagios server using [ansible-nagios](https://github.com/npm/ansible-nagios).
* Set the *nagios_host_groups*, *nagios_commands*, and *nagios_hosts* variables described
in the next section of this document.
* Create a playbook that references the *ansible-nagios-config* role, and use it to deploy
your configuration.

Configuration
-------------

* **nagios_base_dir:** the nagios installation directory. *default: /usr/local/nagios*
* **nagios_object_dir:** the nagios object directory. *default: {{nagios_base_dir}}/etc/objects*
* **nagios_cfg_dir_enabled:** if true, configures nagios_object_dir as a cfg_dir. *default: false*
* **nagios_user:** id of nagios user. *default: nagios*
* **nagios_group:** id of nagios group. *default: nagios*

nagios_hosts
------------

Used to describe the hosts you would like to monitor with Nagios:

* **name:** the name of the host, used to reference it in *host_groups.*
* **address:** the actual address of the host.
* *other variables:* other variables represent *host_groups* that the host
belongs to, e.g., `www`, `aws`.

```yaml
nagios_hosts:
  - {name: 'www', address: 'www.example.com', www: true, aws: true}
  - {name: 'redis', address: 'redis.example.com', redis: true, aws: true}
```

nagios_groups
-------------

Used to organize your hosts into types of servers, e.g., web, redis, MySQL, and
to describe the checks that should be applied to these groups.

```yaml
nagios_host_groups:
  - name: 'www'
    alias: 'Web Servers'
    checks:
      - {command: 'check_http_npmjs_org', description: 'Check HTTP'}
      - {command: 'check_nrpe2!check_disk_nrpe', description: 'Disk Space Left'}
  - name: 'aws'
    alias: 'AWS Servers'
  - name: 'redis'
    alias: 'Redis Server'
    checks:
      - {command: 'check_nrpe2!check_disk_nrpe', description: 'Disk Space Left'}
```

nagios_commands
---------------

Describe custom commands that can be used to perform host checks:

```yaml
nagios_commands:
  - {name: 'check_http_npmjs_org', command: '$USER1$/check_http -H npmjs.org -I $HOSTADDRESS$ $ARG1$'}
  - {name: 'check_nrpe2', command: '$USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$'}
```
