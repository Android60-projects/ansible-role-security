# Basic security Ansible role.

This role installs firewall and fail2ban.  Used for first 5min security. Enables EPEL repo for RedHat.

You can disable package upgrade with this var:
```
upgrade_packages: true
```