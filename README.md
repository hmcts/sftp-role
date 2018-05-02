Role Name
=========

Adds a second SSHD systemd service that only allows SFTP connections.

Public keys for users can be authorised but this role is NOT responsible for
user or group creation.

For each user a chroot is created with a group-writable directory located
at '/incoming' within the chroot.

Requirements
------------

SSHD must already be installed.

Role Variables
--------------

* `sftp_authorizedkeysdir` - Location of per-user authorised keys files
* `sftp_chrootdir` - Location of chroot directory. Each SFTP user has a
  directory created within
* `sftp_group` - Only allow access if SFTP user is a member of this group
* `sftp_port` - Listening port of SSHD for SFTP
* `sftp_users:name` - Name of user to add access for
* `sftp_users:state` - (absent|present) - when made absent it will only remove
* `sftp_users:pubkey` - SSH public key to authorise for this user
* `sftp_role_copy_data` - Information about files to copy. See molecule.yml for example
* `sftp_role_sshd_ciphers` - List of ciphers enabled in the SFTP service
* `sftp_role_sshd_macs` - List of MACs enabled in the SFTP service

Example Playbook
----------------

```
    - hosts: sftp
      vars:
        sftp_authorizedkeysdir: /etc/ssh/authorized_keys_sftp
        sftp_chrootdir: /sftp
        sftp_port: 1234
        sftp_groups:
          - sftp-test-group
        sftp_users:
          - name: sftp-test-user
            group: sftp-test-group
            state: present
            pubkey: "ssh-rsa dummy-data testuser@example.com"
        sftp_role_copy_data:
          - workdir: "{{ sftp_chrootdir }}/sftp/incoming/"
            datafiles:
            - pattern: "*.xml"
              targetdir: "{{ sftp_chrootdir }}/sftp-test-user/incoming"
              fileowner: "sftp-test-user"
```

Author Information
------------------

HMCTS Reform Programme
