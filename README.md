rabbitmq
========

This role installs and configures RabbitMQ on EL (RHEL/CentOS) 7 and 8.

Please note that if SSL is configured, AMQPS and HTTPS for the management
interface will be used. It is not possible to have both AMQP and AMQPS or
both HTTP and HTTPS using this role.

This role also doesn't allow AMQP connections from both IPv4 and IPv6. If
you need to use IPv6, set `rabbitmq_amqp_listen_address` to `::`.

Role Variables
--------------

Facts set by the role:
* `rabbitmq_enabled_plugins` - the list of enabled RabbitMQ plugins (excludes the implicitly
  enabled ones).
* `rabbitmq_version` - the RabbitMQ version derived from the installed RPM.

Role variables:
* `rabbitmq_amqp_listen_address` - the listen address for AMQP(S). If you want RabbitMQ
  to only accept connections from localhost, set this to `127.0.0.1`. This defaults to
  `0.0.0.0`, which means that RabbitMQ accepts connections on all network interfaces.
* `rabbitmq_amqp_listen_port` - the port that RabbitMQ listens for AMQP(S) connections. If
  SSL is configured, this will default to `5671`, otherwise, `5672`. There is no way to
  enable both AMQP and AMQPS using this role.
* `rabbitmq_configure_firewalld` - configure firewalld to open the AMQP(S) and management ports
  when applicable. To use this on RHEL 7, you need to install and turn on firewalld. This
  defaults to `no`.
* `rabbitmq_custom_config` - a dictionary of additional custom configuration to add to
  `/etc/rabbitmq.conf`. This defaults to `{}`.
* `rabbitmq_download_plugins` - the list of URLs to additional RabbitMQ plugins to download.
  This defaults to `[]`.
* `rabbitmq_plugins` - the list of RabbitMQ plugins that should be enabled. Not specifying a plugin
  in this list will cause it to be disabled. This defaults to `['rabbitmq_management']`.
* `rabbitmq_management_listen_port` - the HTTP(S) port that RabbitMQ listens on for the management
  interface. This is only configured if the `rabbitmq_management` plugin is enabled. This defaults
  to `15672`.
* `rabbitmq_remove_guest_user` - remove the `guest` user. This defaults to `yes`.
* `rabbitmq_ssl_cert_file` - the local path to the SSL certificate to copy to the remote system
  and to use for AMQPS and HTTPS on the management interface. If this is not set, AMQP and HTTP
  will be used instead. This is not defined by default.
* `rabbitmq_ssl_key_file` - the local path to the private key to copy to the remote system
  and to use for AMQPS and HTTPS on the management interface. If this is not set, AMQP and HTTP
  will be used instead. This is not defined by default.
* `rabbitmq_users` - the list of RabbitMQ users to create. This uses the same syntax as the
  [rabbitmq_user_module](https://docs.ansible.com/ansible/latest/modules/rabbitmq_user_module.html).
  This defaults to `[]`.
* `rabbitmq_version` - the version of RabbitMQ to install. If this is not set, the latest
  rabbitmq-server package will be picked during the first installation.
* `rabbitmq_version_lock` - determines if the `rabbitmq-server` and `erlang` RPMs should be locked
  to prevent accidental upgrades. This defaults to `no`.

Example Playbook
----------------

```yaml
  - name: Install RabbitMQ
    hosts: el8_host
    become: yes
    roles:
    - mprahl.rabbitmq
    vars:
      rabbitmq_configure_firewalld: yes
      rabbitmq_ssl_cert_file: "../../files/rabbitmq.pem"
      rabbitmq_ssl_key_file: "../../vaults/rabbitmq.key"
      rabbitmq_users:
      - user: matt
        configure_priv: .*
        password: changeme
        read_priv: .*
        update_password: always
        vhost: /
        write_priv: .*
```

License
-------

GPLv3+
