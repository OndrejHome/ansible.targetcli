targetcli
=========

Targetcli target iSCSI role which takes care of installing targetcli, enabling it on boot and configuring it based on the provided variable from ansible.

NOTE: Modules used by this module were separated into role `OndrejHome.targetcli-modules`

Requirements
------------

This role is not creating any disks/partitions/LVs. It is expected that they are already present on machine or created by some other role.

Role Variables
--------------

To configure the iSCSI target, the following nested variable is used which defines how the configuration should look like.

```
iscsi_targets:
  - wwn: 'iqn.1994-05.com.redhat:target'
    disks:
      - path: '/dev/c7vg/LV1'
        name: 'test1'
        type: 'block'
      - path: '/dev/c7vg/LV2'
        name: 'test2'
        type: 'block'
    initiators:
      - 'iqn.1994-05.com.redhat:client1'
      - 'iqn.1994-05.com.redhat:client2'
```

Example Playbook
----------------

Install and configure targetcli server with 2 exported luns under one WWN for 2 specified initiators.

    - hosts: servers
      roles:
        - { role: 'OndrejHome.targetcli' }
      vars:
        iscsi_targets:
          - wwn: 'iqn.1994-05.com.redhat:target'
            disks:
              - path: '/dev/c7vg/LV1'
                name: 'test1'
                type: 'block'
              - path: '/dev/c7vg/LV2'
                name: 'test2'
                type: 'block'
            initiators:
              - 'iqn.1994-05.com.redhat:client1'
              - 'iqn.1994-05.com.redhat:client2'

This role can be also used in combination with [OndrejHome.iscsiadm](https://github.com/OndrejHome/ansible.iscsiadm) that from `v2`
can install needed utilities and determine the initiator WWN that can be supplied for this role as shown below. Note that group
containing the initiators in example below is named `cluster` and you should adjust it for your inventory.

    - hosts: cluster
      roles:
        - { role: 'OndrejHome.iscsiadm' }

    - hosts: storage
      roles:
        - { role: 'OndrejHome.targetcli' }
      vars:
        iscsi_targets:
          - wwn: 'iqn.1994-05.com.redhat:target'
            disks:
              - path: '/dev/c7vg/LV1'
                name: 'test1'
                type: 'block'
            initiators: "[ {% for host in groups['cluster'] %} '{{ hostvars[host][\"iscsi_initiator_name\"] }}', {% endfor %} ]"

You can even later instruct the nodes to connect to target created here.

    - hosts: cluster
      roles:
        - { role: 'OndrejHome.iscsiadm', iscsi_target_ip: "{{ hostvars[groups['storage'][0]]['ansible_default_ipv4']['address'] }}" }

Note for SUSE Linux Enterprise Server 15.x / openSUSE Leap 15.x
-------

Please note that SLES 15.x / openSUSE Leap 15.x only ship with python3 installed by default.

To avoid the ansible warning for the discovered python interpreter, add a line like the following to your inventory:
```
192.168.22.53 ansible_python_interpreter=/usr/bin/python3
```

Note for SLES12.x and SuSEfirewall2
------

This role fails on hosts running SuSEfirewall2, as this cannot be configured by ansible. Either stop and disable the service (not recommended), or manually open port 3260/tcp and set the variable `ignore_sles12_firewall_configuration` to True.

Note for SLES11.x
------

This role was tested to not work on SLES 11, if anyone is interested in getting this to work a PR for adding support is welcome - for more details refer to PR #4.


License
-------

GPLv3

Author Information
------------------

To get in touch with author you can use email ondrej-xa2iel8u@famera.cz or create a issue on github when requesting some feature.
