<p><img src="https://icon-library.com/images/postgresql-icon/postgresql-icon-20.jpg" alt="pg-logo" title="pg" align="top" height=220 /></p>

### General informations

This Ansible role is designed to configure PostgreSQL "streaming" replication on target servers.

**Table of Contents**

  - [Roles variables](#role-variables)
  - [Roles tags](#role-variables)
  - [Install and use this role](#install-and-use-this-role)

**Supported Platforms**

  - RHEL/CentOS Stream 8/9
  - Fedora 35/36

**Supported PostgreSQL releases**

  - PostgreSQL 14.x (default)

**References**

  - PostgreSQL : https://www.postgresql.org/

### Role variables

The role variables are documentated [HERE](docs/variables.md)

### Install and use this role

* Install the role using the command-line :

  ```shell
  $ ansible-galaxy collection install git+https://github.com/ruskofd/postgresql-replication-role.git
  ```

* Install the collection in your projects using a `requirements.yml` file and `ansible-galaxy` command-line :

  ```YAML
  $ cat requirements.yml
  ---
  roles:
    - name: postgresql-replication
      src: https://github.com/ruskofd/postgresql-replication-role.git
      scm: git
      version: '1.0.0'

  $ ansible-galaxy install-f -r requirements.yml
  ```

* Once the collection is installed, you can use the roles in your playbooks :

  ```yaml
  - name: Configure PostgreSQL streaming replication
    hosts: postgresql
    roles:
      - role: postgresql-replication
  ```

