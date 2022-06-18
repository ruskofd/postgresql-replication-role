Role Variables
--------------

#### File locations

| Name                              | Default                      | Description                                                      |
| :-------------------------------- | :--------------------------- | :--------------------------------------------------------------- |
| `postgresql_version`              | `14.4`                       | Define the release version of PostgreSQL to use (binaries, configurations) |
| `postgresql_root_dir`             | `/var/lib/postgresql/{{ postgresql_release }}/data`| Directory where everything related to the PostgreSQL instance will be stored |
| `postgresql_data_dir`             | `{{ postgresql_root_dir }}/data`   | Directory where PostgreSQL will store datas                |
| `postgresql_home_dir`             | `{{ postgresql_root_dir }}`  | Home directory for the `postgres` user                           |

#### Replication

| Name                              | Default                      | Description                                                      |
| :-------------------------------- | :--------------------------- | :--------------------------------------------------------------- |
| `postgresql_replication_user`     | `replication`                | Name of the user to create dedicated to replication              |
| `postgresql_replication_password` | `null`                       | Password of the user dedicated to replication (consider using Ansible Vault or external provider such as Hashicorp Vault) |
| `postgresql_replication_primary_host` | `''`                     | IP address of the primary node                                   |
| `postgresql_replication_primary_port` | `5432`                   | Port of the primary node                                         |
| `postgresql_replication_slot_name`| `standby-{{ ansible_hostname }}` | Name of the replication slot the create on the primary instance |

[Return to main page](../README.md)