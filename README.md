targetcli
=========

Targetcli target iSCSI role which takes care of installing targetcli, enabling it on boot and configuring it based on the provided variable from ansible.

Additionaly this role includes own python mudules to interact with targetcli command which can be used separately for more advanced stuff. Modules implement checking, creating and deleting.

Requirements
------------

This role is not creating any disks/partitions/LVs. It is expected that they are already present on machine or created by some other role.

Role Variables
--------------

To configure the iSCSI target, the following nested variable is used which defines how the configuration should look like.

```
iscsi_targets:
  - target1:
    wwn: "iqn.1994-05.com.redhat:target"
    disks:
      - disk1:
        path: /dev/c7vg/LV1
        name: test1
        type: block
      - disk2:
        path: /dev/c7vg/LV2
        name: test2
        type: block
    initiators:
      - client1:
        wwn: iqn.1994-05.com.redhat:client1
      - client2:
        wwn: iqn.1994-05.com.redhat:client2
```

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
        - { role: OndrejHome.targetcli }
      vars:
        iscsi_targets:
          - target1:
            wwn: "iqn.1994-05.com.redhat:target"
            disks:
              - disk1:
                path: /dev/c7vg/LV1
                name: test1
                type: block
              - disk2:
                path: /dev/c7vg/LV2
                name: test2
                type: block
            initiators:
              - client1:
                wwn: iqn.1994-05.com.redhat:client1
              - client2:
                wwn: iqn.1994-05.com.redhat:client2

License
-------

GPLv3

Author Information
------------------

To get in touch with author you can use email ondrej-xa2iel8u@famera.cz or create a issue on github when requesting some feature.
