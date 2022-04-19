Role Variables
--------------

#### File locations

| Name                              | Default                      | Description                                                      |
| :-------------------------------- | :--------------------------- | :--------------------------------------------------------------- |
| `postgresql_version`              | `14.2`                       | Define the release version of PostgreSQL to use (binaries, configurations) |
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


Configure Streaming Replication
-------------------------------

Here a basic example of variables to configure in order to setup a streaming replication between a primary instance and multiple standby instances :

```YAML
postgresql:
  hosts:
    pg01:
      ansible_host: 10.0.122.208
      postgresql_listen_addresses: '0.0.0.0'
      postgresql_role: primary
      postgresql_hba_entries:
        - { type: host, db: replication, user: replication, address: '10.0.122.207/32', method: scram-sha-256 }
        - { type: host, db: replication, user: replication, address: '10.0.122.206/32', method: scram-sha-256 }
      postgresql_replication_user: "replication"
      postgresql_replication_password: "replication"
    pg02:
      ansible_host: 10.0.122.207
      postgresql_role: standby
      postgresql_replication_user: "replication"
      postgresql_replication_password: "replication"
      postgresql_replication_primary_host: "10.0.122.208"
      postgresql_replication_primary_port: 5432
    pg03:
      ansible_host: 10.0.122.206
      postgresql_role: standby
      postgresql_replication_user: "replication"
      postgresql_replication_password: "replication"
      postgresql_replication_primary_host: "10.0.122.208"
      postgresql_replication_primary_port: 5432
```

Once the instances are deployed, you can check the replication status on the primary instance :

```
[postgres@pg01 ~]$ psql

postgres=# select * from pg_stat_replication;
-[ RECORD 1 ]----+------------------------------
pid              | 17734
usesysid         | 16384
usename          | replication
application_name | walreceiver
client_addr      | 10.0.122.207
client_hostname  |
client_port      | 56734
backend_start    | 2022-02-05 15:00:54.500164+00
backend_xmin     |
state            | streaming
sent_lsn         | 0/50009E8
write_lsn        | 0/50009E8
flush_lsn        | 0/50009E8
replay_lsn       | 0/50009E8
write_lag        |
flush_lag        |
replay_lag       |
sync_priority    | 0
sync_state       | async
reply_time       | 2022-02-05 15:24:06.337127+00
-[ RECORD 2 ]----+------------------------------
pid              | 17735
usesysid         | 16384
usename          | replication
application_name | walreceiver
client_addr      | 10.0.122.206
client_hostname  |
client_port      | 57948
backend_start    | 2022-02-05 15:00:54.511108+00
backend_xmin     |
state            | streaming
sent_lsn         | 0/50009E8
write_lsn        | 0/50009E8
flush_lsn        | 0/50009E8
replay_lsn       | 0/50009E8
write_lag        |
flush_lag        |
replay_lag       |
sync_priority    | 0
sync_state       | async
reply_time       | 2022-02-05 15:24:06.360323+00
```

But also the replication slots status (enabled by default) :

```
[postgres@pg01 ~]$ psql

postgres=# select * from pg_replication_slots ;
-[ RECORD 1 ]-------+----------------
slot_name           | standby_pg02
plugin              |
slot_type           | physical
datoid              |
database            |
temporary           | f
active              | t
active_pid          | 17734
xmin                |
catalog_xmin        |
restart_lsn         | 0/50009E8
confirmed_flush_lsn |
wal_status          | reserved
safe_wal_size       |
two_phase           | f
-[ RECORD 2 ]-------+----------------
slot_name           | standby_pg03
plugin              |
slot_type           | physical
datoid              |
database            |
temporary           | f
active              | t
active_pid          | 17735
xmin                |
catalog_xmin        |
restart_lsn         | 0/50009E8
confirmed_flush_lsn |
wal_status          | reserved
safe_wal_size       |
two_phase           | f
```

[Return to main page](../README.md)
