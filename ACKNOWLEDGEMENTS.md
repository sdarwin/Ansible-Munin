
Acknowledgements:

This code was based on the excellent work of Jeff Geerling:  
https://galaxy.ansible.com/geerlingguy/munin-node/   
https://galaxy.ansible.com/geerlingguy/munin/   

as well as the Chef Munin Cookbook:    
https://github.com/jesseadams/munin

The initial project goals were to add a number of Munin-Chef features into the Munin-Ansible role.  

- auto-discovery of the hosts/clients, rather than being manually configured.
- implement apache and nginx vhost sites, where the web configuration is managed by ansible
- support both nginx and apache 
- incorporate htpasswd into the web config to automatically configure auth.
- rather than simply having user/pass variables defaults/main.yml, parse the users variable, looking for sysadmins.
- add an ignore list for hosts/clients.
- combine server and client into a single ansible role, as Chef does for Munin.
- in /etc/munin/plugin.conf.d "If the same behaviour is defined in more than one file, the directives from the last file read takes precedence." Change plugin-conf.d/ansible.conf to nansible.conf so that it takes precedence over munin.conf
- on the client side, munin_node_allowed_ips should auto-detect the munin servers from inventory.
- If EPEL is required on RedHat, install it automatically.

