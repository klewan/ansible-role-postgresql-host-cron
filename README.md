Ansible Role: postgresql-host-cron
==================================

This role is used for PostgreSQL Database Server cron management.

Supported OS:
-------------
* RedHat
* CentOS
* OracleLinux

Requirements
------------

None

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    # whether or not copy scripts enlisted in 'postgresql_host_cron_templates_to_copy' to target hosts
    postgresql_host_cron_copy_scripts: true

    # files to be copied to remote host
    postgresql_host_cron_templates_to_copy:
      - file: vacuum_analyze.sh.j2
        dest: '{{ postgresql_scripts_directory }}/vacuum_analyze.sh'
        owner: '{{ postgresql_user }}'
        group: '{{ postgresql_group }}'
        mode: '0750'
      - file: pg_backup_dump.ksh.j2
        dest: '{{ postgresql_scripts_directory }}/pg_backup_dump.ksh'
        owner: '{{ postgresql_user }}'
        group: '{{ postgresql_group }}'
        mode: '0750'

    # cron config
    postgresql_host_cron_config:
      - name: "trigger VACUUM ANALYZE for each database on the server"
        job: "{{ postgresql_scripts_directory }}/vacuum_analyze.sh >/dev/null 2>&1"
        hour: 20
        minute: 0
        state: present
        disabled: no
      - name: "backup (dump) each database on the server"
        job: "{{ postgresql_scripts_directory }}/pg_backup_dump.ksh 5 >/dev/null 2>&1"
        hour: 22
        minute: 0
        state: present
        disabled: no

    # whether or not manage cron jobs
    postgresql_host_cron_manage_cron_jobs: true

    # whether or not disable cron jobs enlisted in 'postgresql_host_cron_config' (for example: maintenance/patching)
    postgresql_host_cron_disable_jobs: false

    # whether or not to import nsr_backup_role role tasks related to cron jobs
    postgresql_host_cron_import_nsr_backup_role: true


Dependencies
------------

This role uses `postgresql` and `postgresql-nsr-backup` roles.

Example Playbook
----------------

    - name: Configure PostgreSQL database server cron
      hosts: pg-servers
      become: true
      become_user: '{{ postgresql_user }}'

      tasks:

      - import_role:
          name: postgresql-host-cron
        vars:
          postgresql_host_cron_copy_scripts: true
          postgresql_host_cron_disable_jobs: false
          postgresql_host_cron_manage_cron_jobs: true
        tags:
          - postgresql_host_cron


Inside `vars/main.yml` or `group_vars/..` or `host_vars/..`:

    #------------------------------------------------
    # overrides role 'postgresql-host-cron' variables
    #------------------------------------------------


License
-------

GPLv3 - GNU General Public License v3.0

Author Information
------------------

This role was created in 2018 by [Krzysztof Lewandowski](mailto:Krzysztof.Lewandowski@fastmail.fm).


