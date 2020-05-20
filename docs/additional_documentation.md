
## How to host Munin and Nagios on the same machine

Install both the Munin and Nagios roles:  
https://github.com/sdarwin/Ansible-Nagios  
https://github.com/sdarwin/Ansible-Munin  

Set the "home" page to have hyperlinks to both services. Here's an example.

/var/www/html/index.html

```
<html>
<body>
<br>
<a href="/nagios/">nagios</a><br>
<a href="/munin/">munin</a><br>
</body></html>
```

In your Ansible variables, such as group_vars/all, or wherever you set variables:

```
munin_htmlrootdir: /var/www/html
```

When a visitor goes to the main webpage of "monitor.example.com", apache serves the contents from /var/www/html, and from there they are directed to /munin or /nagios.

The vhost is Munin. Nagios is within the same vhost using "Alias /nagios" from the confs.
