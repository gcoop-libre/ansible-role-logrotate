Logrotate
=========

An Ansible Role that installs and configures Logrotate on Debian and Ubuntu.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    logrotate_include_dir: /etc/logrotate.d

Directory where the config files for services log rotation will be created.

    logrotate_global_frequency: weekly
    logrotate_global_rotate: 4
    logrotate_global_create: True
    logrotate_global_create_mode: ''
    logrotate_global_create_owner: ''
    logrotate_global_create_group: ''
    logrotate_global_compress: False
    logrotate_global_extra_config: []

Global settings for log rotation. This properties set the frecuency of the log rotation, the number of logs that will be kept as history, the creation of a new log file after rotation and if the rotated log should be compressed. The `logrotate_global_extra_config` property allow you to set custom configurations. For the available options see `man logrotate`.

    logrotate_wtmp_logs:
      - /var/log/wtmp
    logrotate_wtmp_missingok: True
    logrotate_wtmp_frequency: monthly
    logrotate_wtmp_rotate: 1
    logrotate_wtmp_create: True
    logrotate_wtmp_create_mode: '0664'
    logrotate_wtmp_create_owner: root
    logrotate_wtmp_create_group: utmp
    logrotate_wtmp_extra_config: []

    logrotate_btmp_logs:
      - /var/log/btmp
    logrotate_btmp_missingok: True
    logrotate_btmp_frequency: monthly
    logrotate_btmp_rotate: 1
    logrotate_btmp_create: True
    logrotate_btmp_create_mode: '0660'
    logrotate_btmp_create_owner: root
    logrotate_btmp_create_group: utmp
    logrotate_btmp_extra_config: []

This properties configure the rotation of the wtmp and btmp logs. `logrotate_wtmp_logs` and `logrotate_btmp_logs` define the log file path that should be rotated in each case. The following properties set the same options that the global ones.

    logrotate_service_logs:
      - name: service_name
        logs:
          - /path/to/log/files
        notifempty: False
        missingok: False
        frequency: '{{ logrotate_global_frequency }}'
        create: False
        create_mode: ''
        create_owner: ''
        create_group: ''
        rotate: '{{ logrotate_global_rotate }}'
        compress: False
        delaycompress: False
        sharedscripts: False
        prerotate: ''
        postrotate: ''
        extra_config: []

This property defines a list of services which logs will be rotated. The `name` property will be used as the file name. It is recommended to keep it as the service name. `logs` define the log files that should be rotated for the service. The rest of the properties are optional.

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: servers
      vars_files:
        - vars/main.yml
      roles:
         - gcoop-libre.logrotate

*Inside `vars/main.yml`*:

    logrotate_service_logs:
      - name: apache
        logs:
          - /var/log/apache2/*.log
        notifempty: True
        missingok: True
        frequency: weekly
        create: True
        create_mode: '640'
        create_owner: 'root'
        create_group: 'adm'
        rotate: 52
        compress: True
        delaycompress: True
        sharedscripts: True
        prerotate: |
          if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
            run-parts /etc/logrotate.d/httpd-prerotate; \
          fi; \
        postrotate: |
          /etc/init.d/apache2 reload > /dev/null

License
-------

GPLv2

Author Information
------------------

This role was created in 2016 by [gcoop Cooperativa de Software Libre](https://www.gcoop.coop).
