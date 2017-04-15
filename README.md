# targetcli

Targetcli target iSCSI role which takes care of installing targetcli, enabling it on boot and configuring it based on the provided variable from ansible.

NOTE: targetcli modules used by this role are in a seperate repo were separated into role [targetcli-modules](https://github.com/stuvusIT/targetcli-modules)

## Requirements

An apt or yum-based package manager and systemd. This role is not creating any disks/partitions/LVs. It is expected that they are already present on machine or created by some other role.

## Role Variables

one parent variable `iscsi_targets`, a list of entries containing the following mandatory entries:

| Name                  | Description                                                                                 |
|-----------------------|---------------------------------------------------------------------------------------------|
| `wwn`                 | `wwn` identifier of this target                                                             |
| `disks`               | list of disks, see [below](#disks)                                                          |
| `initiators`          | list of `initiator`-`wwn`s                                                                  |

### disks

a list of dicts with the following mandatory entries:

| Name                  | Description                                                                                 |
|-----------------------|---------------------------------------------------------------------------------------------|
| `path`                | existing path to the disk that should be used as backstore.                                 |
| `name`                | name that is used for the backstore                                                         |
| `type`                | one out of {`fileio`, `iblock`, `pscsi`, `rd_mcp`}                                          |

## Dependencies

This role depends on the [targetcli modules](https://github.com/OndrejHome/ansible.targetcli-modules) written by [Ondrej Faměra (OndrejHome)](https://github.com/OndrejHome/).

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
        - { role: stuvus.targetcli }
      vars:
        iscsi_targets:
          - wwn: "iqn.1994-05.com.redhat:target"
            disks:
              - disk1:
                path: /dev/zvol/tank/vms/testing/vm1
                name: vm1
                type: iblock
              - disk2:
                path: /dev/zvol/tank/vms/testing/vm2
                name: vm2
                type: iblock
            initiators:
              - iqn.1994-05.com.redhat:client1
              - iqn.1994-05.com.redhat:client2

## License

GPLv3

## Author Information
* original author: [Ondrej Faměra (OndrejHome)](https://github.com/OndrejHome/) _ondrej-xa2iel8u@famera.cz_
* modified by [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
