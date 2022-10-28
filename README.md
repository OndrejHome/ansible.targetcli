yeoldegrove.targetcli Ansible Collection
=========

This is a fork of [OndrejHome/ansible.targetcli](https://github.com/OndrejHome/ansible.targetcli) and [OndrejHome/ansible.targetcli-modules](https://github.com/OndrejHome/ansible.targetcli-modules) that will hopefully work again with `ansible-galaxy` as a collection.

Includes a Targetcli target iSCSI role which takes care of installing targetcli, enabling it on boot and configuring it based on the provided variable from ansible.

Requirements
------------

This is not creating any disks/partitions/LVs. It is expected that they are already present on machine or created by some other role.

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

Example Playbooks
----------------

Install and configure targetcli server with 2 exported luns under one WWN for 2 specified initiators.

```
- name: Create iSCSI target server
  hosts: servers

  tasks:
    - name: Create iSCSI target via targetcli
      include_role:
         name: yeoldegrove.iscsi_target_server.targetcli
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
```

This role can be used in combination with [community.general.open_iscsi](https://docs.ansible.com/ansible/latest/collections/community/general/open_iscsi_module.html).

```
- name: Login to iSCSI target server
  hosts: clients

  handlers:
  - name: "restart iscsid"
    service:
      name: iscsid
      state: restarted

  tasks:
    - name: "Start and enable {{ iscsiadmin_service }} service"
      ansible.builtin.service:
        name: iscsid
        enabled: true
        state: started

    - name: Set iSCSI InitiatorName
      ansible.builtin.lineinfile:
        path: /etc/iscsi/initiatorname.iscsi
        regexp: '^InitiatorName='
        line: "InitiatorName={{ iscsi_iqn }}:{{ ansible_hostname }}"
        owner: root
        group: root
        mode: '0644'
      notify: "restart iscsid"

    - name: Set startup to automatic in /etc/iscsi/iscid.conf
      ansible.builtin.lineinfile:
        path: /etc/iscsi/iscsid.conf
        regex: '^node\.startup\ =\ manual'
        line: "node.startup = automatic"
        owner: root
        group: root
        mode: '0600'
      notify: "restart iscsid"

    - name: Discover targets on portal and login to the one available
      community.general.open_iscsi:
        portal: 'iscsi-portal.example.com'
        target: 'iqn.1994-05.com.redhat:target'
        login: true
        discover: true
        auto_node_startup: true
```

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

Kudos to original Author
------------------

To get in touch with the original author you can use email ondrej-xa2iel8u@famera.cz.
