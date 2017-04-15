# iscsi-target

iSCSI target role that installs and uses targetcli.

## Requirements

An apt- or yum-based package manager and systemd. 
Also, this role is not creating any disks/partitions/LVs. Therefore it is expected that they are already present on machine or created by some other role.

## Role Variables

one parent variable `iscsi_targets` which contains a list of entries containing the following mandatory entries:

| Name                  | Description                                                                                 |
|-----------------------|---------------------------------------------------------------------------------------------|
| `wwn`                 | `wwn` identifier of this target                                                             |
| `disks`               | disk configuration, see [below](#disks)                                                     |
| `initiators`          | list of `initiator`-`wwn`s                                                                  |

### disks

a list of dicts with the following mandatory entries:

| Name                  | Description                                                                                 |
|-----------------------|---------------------------------------------------------------------------------------------|
| `path`                | existing path to the disk that should be used as backstore.                                 |
| `name`                | name that is used for the backstore                                                         |
| `type`                | one out of {`fileio`, `iblock`, `pscsi`, `rd_mcp`}                                          |

## Dependencies

This role depends on the ansible [targetcli modules](https://github.com/OndrejHome/ansible.targetcli-modules) written by [Ondrej Faměra (OndrejHome)](https://github.com/OndrejHome/).

## Example Playbook
```yml
- hosts: storage
  roles:
    - role: iscsi-target
      iscsi_targets:
        - wwn: "iqn.1994-05.com.redhat:target"
          disks:
            - name: vm1
              path: /dev/zvol/tank/vms/testing/vm1
              type: iblock
            - name: vm2
              path: /dev/zvol/tank/vms/testing/vm2
              type: iblock
          initiators:
            - iqn.1994-05.com.redhat:client1
            - iqn.1994-05.com.redhat:client2
```

## License

GPLv3

## Author Information
* original author: [Ondrej Faměra (OndrejHome)](https://github.com/OndrejHome/) _ondrej-xa2iel8u@famera.cz_
* modified by [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
