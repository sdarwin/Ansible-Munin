# Ansible Role: Munin

Installs munin, a monitoring system, on RedHat/CentOS or Debian/Ubuntu Linux servers.  

This role will install both munin and munin-node (client and server), as well as configure Munin automatically based on Ansible's existing inventory of hosts. The goal is to have a complete Munin monitoring system in a single Ansible role, and without too much manual configuration required.

## Requirements

Make sure that DNS or /etc/hosts name resolution is working. For example that you can ping web3 at web3.example.com.  Also, that all machines have their hostname set correctly and the same as the ansible inventory.

## Role Variables

All the variables in defaults/main.yml may be overridden or changed. 

### Server Configuration 

In the ansible inventory, place the munin server (or even multiple munin servers) in the group monitoring-servers.  Then run this role on those servers by specifying "- hosts: monitoring-servers" and the variable "run_munin_server: true" in the playbook.  See the example playbook default.yml in this directory. 

### Munin Monitored Hosts:

Ansible inventory hosts in the 'all' group are converted to munin monitored hosts. No configuration required. Any hosts in the optional munin_hosts_ignore variable will be skipped.  

Additional monitored hosts may be added to the munin_hosts variable. See defaults/main.yml for examples. Extra data is supported in this array.   See documentation at [Munin Node Definitions](http://munin.readthedocs.org/en/latest/reference/munin.conf.html#node-definitions) for more details as to what values to use here.
 
### Contacts/Users:

Add users who should be allowed to login to the Munin web interface in the munin_users variable:  

munin_users:  
  - user: muninadmin  
    pass: Password1change  

Another and better way is the users variable, compatible with https://github.com/mivok/ansible-users

This playbook will look for users in the sysadmin group and those will become munin admins.

Create the users var, a good place is group_vars/all. Here is an example.

users:  
  - username: foo  
    name: Foo Barrington  
    groups: ['sysadmin']  
    uid: 1001  
    ssh_key:  
      - "ssh-rsa AAAAA.... foo@machine"  
      - "ssh-rsa AAAAB.... foo2@machine"  
    htpasswd: $apr1$SheSL4Et$xry6RljdWWvUVrh42s7OA0  

Where did that htpasswd value come from?  Generate the htpasswds manually, and then paste them into users.  
htpasswd -n mario  
mario:$apr1$SheSL4Et$xry6RljdWWvUVrh42s7OA0  

Interesting variable:

munin_cron_job: present

Determines whether the munin cron job (which runs every 5 minutes) should be active. By setting this to `absent`, you can leave munin installed and configured on your server but effectively disable it. This allows quick enabling or disabling for munin monitoring.

You can enable mail alerts by adding :

    munin_alerts:
      - {
        name: "JohnDoe",
        email: "johndoe@example.com",
        subject: "Munin-notification for ${var:group} :: ${var:host}",
        level: "warning critical"
      }

### Client Configuration:

The client's allowed hosts are auto-configured, based on the Munin Servers.

The munin_node_allowed_ips variable may also optionally be configured for additional allowed hosts, although it's not necessary.  The variable contains a list of IP addresses formatted as a python-style regular expression. Must use single quotes to allow the proper regex escaping to pass through to the configuration file. Hosts with these IP addresses will be allowed to connect to the server and get detailed system stats via munin-node.

### Munin Plugin Configuration

You can enable additional plugins using the `munin_node_plugins` list, like so:

    munin_node_plugins:
      - name: uptime

If the name of the resulting plugin does not match the name of the munin plugin from which it is generated (as is the case, say, with the `if_` plugin), you need to add a `plugin` field to the list item, like so:

    munin_node_plugins:
      - name: if_eth0
        plugin: if_

#### Plugin settings

If you need to add plugin configuration for plugins you've added via `munin_node_plugins`, you can do so with a simple hashmap that has the plugin name (which will be the `[plugin]` section in the resulting configuration file), and a list of variable names and values. For example:

    munin_node_config: {
      "ps_test": {
        "env.regex": "bash",
        "env.name": "bash"
      }
    }

This configuration will generate a configuration file at `/etc/munin/plugin-conf.d/nansible.conf` with the following contents:

    [ps_test]
    env.regex bash
    env.name bash

## Dependencies

None.

## Example Playbook

    - hosts: all
      remote_user: ansible
      become: true
      roles:
        - { role: 'sdarwin.munin', run_munin_client: true }

    - hosts: monitoring-servers
      remote_user: ansible
      become: true
      roles:
        - { role: 'sdarwin.munin', run_munin_server: true }

## License

MIT / BSD

## Author Information

By Sam Darwin, 2016. Based on pre-existing roles, see ACKNOWLEDGEMENTS.md file.  Feedback and bug reports welcome.
