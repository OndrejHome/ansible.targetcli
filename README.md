# targetcli

Targetcli target iSCSI role which takes care of installing targetcli, enabling it on boot and configuring it based on the provided variable from ansible.

NOTE: targetcli modules used by this role are in a seperate repo were separated into role [targetcli-modules](https://github.com/stuvusIT/targetcli-modules)

## Requirements

An apt-based package manager and systemd. This role is not creating any disks/partitions/LVs. It is expected that they are already present on machine or created by some other role.

## Role Variables

To configure the iSCSI target, the following nested variable is used, which defines how the configuration should look like.

```yml
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

## Example Playbook

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

## License

GPLv3

## Author Information
* original author: [Ondrej Faměra](https://github.com/OndrejHome/) _ondrej-xa2iel8u@famera.cz_
* modified by [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
