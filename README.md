# ucf.ansible_role_httpd24_config

Flexible, powerful, templated configuration for Apache 2.4 on RHEL/CentOS/Amazon Linux

## Requirements

This role only creates apache configuration files. Apache will need to be installed prior to running this role. You can use `ucf.ansible_role_httpd24_install` to install apache if needed.

## Role Variables

### Global Settings
```yaml
apache_vhosts_version: "2.4"
apache_listen_ip: "{{ ansible_default_ipv4.address }}"
apache24_listen_port: 9000
apache_listen_port: "{{ apache24_listen_port }}"
apache_serveradmin: root@localhost
apache_allow_override: "All"
apache_options:  "-Indexes -FollowSymLinks -ExecCGI"
apache_x_apache_server: "{{ inventory_hostname|regex_replace('^([0-9a-zA-Z\\-]+)\\..*$','\\1')|upper +'-24'}}"

apache_x_forwarded_proto: yes
apache_install_root: '/opt/rh/httpd24/root/'
apache_conf_root: "{{ apache_install_root }}etc/httpd"
apache_confd_path: "{{ apache_conf_root + '/conf.d'}}"
apache_restart: False
delete_vhosts_conf: False
# Combine vhosts into single file.
apache_combine_vhosts: True
# Fail play when Apache Validation has Warnings
apache_stop_cfg_on_validation_warning: True
```
<br>


### Example of predefined vhost settings that can be merged into the vhost configuration in the `add_settings:` section of a vhost variable structure.<br>
Note: This should be defined in a seperate variable file. You can not reference a variable defined in the same file.
```yaml
swp_default_vhost_settings:
  top_params:
    - 'https-only-redirect.j2'
    - 'php_security_settings.j2'
    - 'blockauthorenum.j2'
  directory_params:
    - 'blockreadmehtml.j2'
  bottom_params:
    - 'swp_default_rewrites.j2'
```

### Example `vhost_list` with vhost template variable structure
```yaml
vhost_list:
  - name: basic-example
    servername: basic.ucf.edu
    serveralias: >
      basic.ucf.edu www.basic.ucf.edu
    documentroot: /var/www/basic/wordpress
    add_settings: "{{ swp_default_vhost_settings }}"

  - name: verbose-example
    ip: 192.168.0.100
    port: 9999
    x_forwarded_proto: no
    servername: www.test.ucf.edu
    serveralias: >
      alias.test.ucf.edu www.alias.test.ucf.edu
      blah.test.ucf.edu www.blah.test.ucf.edu
    serveradmin: webcom@ucf.edu
    documentroot: /var/www/test/wordpress
    options: '-Indexes'
    php_security_settings:
      php_allow_url_fopen: 'on'
      php_tmp: /tmp/
      php_sessions: /tmp/php_sessions/
    top_params:
      - |
        # PHP Optimization for Jupiter Theme
          php_admin_value max_input_vars 4000
    directory_params:
      - blockreadmehtml.j2
    bottom_params:
      - '# Started from the bottom, now we here...'
    add_settings: "{{ swp_default_vhost_settings }}"
```

## Dependencies

None.

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yaml
    - hosts: servers
      roles:
         - { role: ucf.ansible_role_httpd24_config }
```
## License

[MIT License](https://github.com/UCF/ansible-role-lsb-core/blob/master/LICENSE)

## Author Information


Author(s): [Derek J. Bernard](https://github.com/derekjbernard), [Vinnie Vu](https://github.com/vinhtvu2)
