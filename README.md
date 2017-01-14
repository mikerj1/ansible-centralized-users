ansible-centralized-users
=========

This role is meant to be a substitute for a centralized store of users and groups in cases where systems such as LDAP don't make sense due to the small size of an environment.

The intent is that all users and all groups are documented in a single file, yet still have the ability to control per host presence.

DANGER: *Listed* users are removed from systems/groups *not* listed in on_systems for that user.  Unlisted users are not modified.
This means that if you have specified no valid inventory name (system or group) in on_systems for a user, that user will be removed from ALL servers!

WARRANTY: None.  Use at your own risk.  Contributions and bug reports welcome.

Requirements
------------

Relies upon the built in Ansible user, group and authorized_keys modules.  Has only been tested by me on Ansible 2.1 and mostly on EL7.

Role Variables
--------------

Default variables are in defaults/main.yml and can be overridden.  The role expects lists "group_list" and "user_list" to be defined in a variables file.
All parameters supported by the Ansible users, groups or authrorized_keys modules supported. An example group_vars/all/ (eg: directory.yml):
```yaml
---
  # Override defaults here.  See defaults/main.yl
  rrs_user_default_shell: /bin/zsh

  # List all groups for all systems here
  group_list:
    - name: admins1
      gid: 3000
    - name: dev1
      gid: 3010
    - name: dba1
      gid: 3020
    - name: badgroup
      gid: 6000
      state: absent
  
  # List all users and group memberships for all systems here.
  # can override defaults for individuals here
  # Default is *absent*.  To remove a user from all systems, list no valid inventory entries in "on_systems:"
  user_list:
    - name: mike
      comment: mike@example.com
      groups: admins1
      uid: 1001
      on_systems: [ 'all' ]
      key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC102vKTgL+5X3Q9uwFgS86zGnCvYQ81T4rNd7i7qUqvN8sT7BFrr4sZLfuUUZ+1kzEvHz2fgBKyzGUfJJcZfR/YMGFhV96PDEMeKTRtORtzXiIQ7vwLcY/+rSUEOJvpQF3p8/vFIoL9pm76fRujRtJEhwMKaIj5TJ1zAzODwyuO1jY8bGxqvlkw2BgB5niuO2XDb+fV0disKpWJks494XawSZLfV7LLB5KHGqKmFfelg1/GlJ+A3IV6F4kUnoWd/iPwBR9vZve5tc6YuTk6M/AfgcR4i+/rNNjK05+ODRoUy/yr/pyDBSfa2DzDVJHx7FEcsDtka9rbRMRbMmIYFqP mike@mj"
    - name: bob
      comment: bob@example.com
      groups: dev1,dba1
      uid: 1002
      shell: /bin/bash
      on_systems: [ 'all' ]
      key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDm4biebtD9Q8cOJ7rY9n1cgVWd5wgnwljd9eQ0Kkfv+sEDmPq76N0r/9fyK2RHySayCElPQ/vFNfFYK8zRKsV420k00r7X0UnBQR5nguLugK6URzWsNBnj6mqpSYvRRQUcw92XLPBCt2WuRpBjimHXVyglDKaNEFmviDdpqxa0P03uMhT5CvftlcS0unQgPRr7GZq0crnenURNhqM0HlU2YasifrCIp0EHAbGa/bDhKafTStWypexgeniXiG962dAW1Tq8Z+DnFqXhQa9lPvEKuKPq+EUYuMR1Rq7q3nLyrPGdWGsR+DVognz2Y/tRiFagyYyIM/ke5HkZ/1IYZ3On me@local"  
    - name: dave
      comment: "Lead Developer"
      groups: dev1
      uid: 1003
      on_systems: [ 'vpn', 'webservers' ]
      key: "ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAuWpqU1S6BbZAeJ6kczycodFDKevUrs3ky1BTwonzeniPpm30j/gM5TUm0ZMIdyH4LLEHdQosK8unWNtnVUSmJX89LUCjfiIO5iT8KQw/R5CIQ7LOC34lnpaSFK0uRqSA5NqOd0SJxLcHt21ynGXEYHiEOOSXLCuWXomz0sTzcShBgvQoCRs3h5JaCXkBFoVnJV190TrFcyGoSL07KOQJ6HL+zoq+qUxMwJdshWqc/hIlyJ24AYFKgaXeqtqJpkamQtJZWi6aug50i69QdElAgHPP2jUp5EpBjDPR0BqUrJUZEovpNWDk6KbVGvPJFMZQG5q79KCSUYlb4JR54fsxMw== cygwin-key
      ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEA2i1kgAzIM6SB7BanqHB5IoN4WyzMUtzau/x7Ctjy79or/Arr5EaDsish4L3U+m/Du81Rw8l4L6XV/Fi7MaeItFwP3jrtg8Lb07fb+dpK6JQ4VdicR8hnIk6ibWvEShh5LBxeSTismvDSsecVQtHDSfGv/e3FqgxPJMBDxZjJ1Y5TM+gQMYlj/ls395QrPkRqqMWRwR1zxXkrBo10IfBT7YmRakSsfBobaJS5Nm8rDy6SrWcJ3VkGPMLPRi8ht1hQ1/3tvaTqnbGAkbpIOgWb7C/03o/4cdcmCVbTk6n9mVW8XN8Ef8H4wu5JUgkzxz4VVK3G/nBd/3ydOVCCJaE8hw== putty/PC"
    - name: chris
      comment: 
      groups: admins1
      uid: 1004
      on_systems: 
        - vpn
        - logmonitor
      key: "ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAi9q+/QR6YJkISyJ19obrjF6gu7eTrY70vU2AnJbMt7Huo0bJVRsnu5cPDdA0wHmCBaImxD4D4XO4O+pAJ889R54fvcQzwTdWOPornyzN5rpccZmPb/tsr+/k7HT5lStl1kHL53AWmkCyS1GEGZNYY4P3GDBBXHjGaPt/YQ5/eME1EQce2j+AxLuHdLBawlylg2lCgkGTOdob4YAuggLPu02/6qm/XevSHoxuTBqUJcRqv7JAG68+vqIEGKc/pUgVuVFDabJmrESjQMTsmWgONHxgi8wpn+m5CqMPzlNBOqn97t2BBjbMjqVIEcBBCI1iyz3tAGkL0tieBSATR9n7/w== rsa-key-20161205"
```

Dependencies
------------

None

Example Playbook
----------------

---

- hosts: all

  roles:
  - rrs.centralized-users



License
-------

GNU LGPLv3

Author Information
------------------

Michael Johnson - mj \[AT\] redravensystems com
