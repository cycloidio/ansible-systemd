ansible-systemd
===============

Extend features of https://github.com/tumf/ansible-role-systemd-service.

Give a way to create timer, mount and droppin on systemd

Role Variables
--------------

**Dropin:**

| **Variable**              | **Description**            | **Default**                                                     |
|---------------------------|----------------------------|-----------------------------------------------------------------|
| `systemd_mount_dir`       | Systemd mount dir          | `/etc/systemd/system`                                           |
| `systemd_dropin_dir`      | Systemd dropin dir         | `/etc/systemd/system/{{systemd_dropin_service_name}}.service.d` |
| `systemd_dropin_name`     | Name of the dropin         | `{{systemd_dropin_service_name}}`                               |
| `systemd_dropin_priority` | Dropin priority level      | `40`                                                            |
| `systemd_dropin_content`  | Content list of the dropin | `[]`                                                            |

**Timer:**

| **Variable**                      | **Description**                 | **Default**           | **Required** |
|-----------------------------------|---------------------------------|-----------------------|--------------|
| `systemd_timer_service_name`      | Name of the timer service       |                       | **Yes**      |
| `systemd_timer_onbootsec`         | Define systemd OnBootSec        | `5min`                |              |
| `systemd_timer_onunitactivesec`   | Define systemd OnUnitActiveSec  | `60min`               |              |
| `systemd_timer_dir`               | Systemd directory for timers    | `/etc/systemd/system` |              |
| `systemd_timer_OnCalendar`        | Systemd timer onCalendar        |                       |              |
| `systemd_timer_RemainAfterElapse` | Systemd timer RemainAfterElapse |                       |              |

**Mount:** (https://www.freedesktop.org/software/systemd/man/systemd.mount.html)

| **Variable**               | **Description**             | **Default** | **Required** |
|----------------------------|-----------------------------|-------------|--------------|
| `systemd_mount_device`     | Device to mount             |             | **Yes**      |
| `systemd_mount_mountpoint` | Mountpoint of the device    |             | **Yes**      |
| `systemd_mount_type`       | Type of filesystem to mount | `ext4`      |              |
| `systemd_mount_options`    | Option to pass to mount     | `None`      |              |

Example Playbook
----------------

```
    - hosts: servers
      roles:
        - role: cycloid.systemd
          systemd_type: mount
          systemd_mount_device: "{{etcd_volume_device}}"
          systemd_mount_mountpoint: /var/lib/etcd2

        - role: cycloid.systemd
          systemd_type: dropin
          systemd_dropin_service_name: "{{etcd_service}}"
          systemd_dropin_name: persistent-storage
          systemd_dropin_priority: "01"
          systemd_dropin_content:
            - "[Unit]"
            - "After=var-lib-etcd2.mount"
            - "Requires=var-lib-etcd2.mount"

        - role: cycloid.systemd
          systemd_type: timer
          systemd_timer_service_name: etcd2-backup
          systemd_timer_onbootsec: 5min
          systemd_timer_onunitactivesec: "{{etcd_backup_timer_every}}"
```

License
-------

BSD

Author Information
------------------

Gael Lambert at Cycloid.io
