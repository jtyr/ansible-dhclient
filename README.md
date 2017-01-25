dhclient
========

Role which helps to install and configure `dhclient`. It allows to create
configuration files as well as hooks and scripts.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Example
-------

```
---

- name: Example of how to use the dhclient role
  hosts: myhost1
  vars:
    # Create default dhclient.conf file
    dhclient_configs:
      # This is the actual file name (.conf is appended automatically)
      dhclient:
        - timeout 60
        - retry 60
        - reboot 10
        - select-timeout 5
        - initial-interval 2
        - reject 192.33.137.209
        - interface "ep0":
            - send host-name "andare.fugue.com"
            - send dhcp-client-identifier 1:0:a0:24:ab:fb:9c
            - send dhcp-lease-time 3600
            - supersede domain-search "fugue.com", "rc.vix.com", "home.vix.com"
            - prepend domain-name-servers 127.0.0.1
            - request subnet-mask, broadcast-address, time-offset, routers, domain-search, domain-name, domain-name-servers, host-name
            - require subnet-mask, domain-name-servers
            - script "/sbin/dhclient-script"
            - media "media 10baseT/UTP", "media 10base2/BNC"
        - alias:
            - interface "ep0"
            - fixed-address 192.5.5.213
            - option subnet-mask 255.255.255.255
      # This is a NIC-specific config file
      dhclient-eth0:
        - timeout 120

    # Create dhclient-enter-hooks hook
    dhclient_hooks:
      # This is a file name followed by the file content
      dhclient-enter-hooks: |
        PEERDNS=no

    # Create a dhclient-script.d script
    dhclient_scripts:
      # This is a file name (.sh is appended automatically) followed by the file content
      test: |
        echo "This is the content of the test.sh file"
```


Role variables
--------------

List of variables used by the role:

```
# Package to be installed (explicit version can be specified here)
dhclient_pkg: dhclient

# Root path to the config files
dhclient_configs_root: /etc/dhcp

# Configuration
dhclient_configs: {}

# Root path to the hooks config files
dhclient_hooks_root: "{{ dhclient_config_root }}"

# Hooks configuration
dhclient_hooks: {}

# Path to the scripts directory
dhclient_scripts_root: "{{ dhclient_config_root }}/dhclient.d"

# Scripts configuration
dhclient_scripts: {}
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
