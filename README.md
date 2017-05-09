Ansible Role: ProFTPd
--------------------

This role will setup and config proFTPd

It's part of the Manala <a href="http://www.manala.io" target="_blank">Ansible stack</a> but can be used as a stand alone component.

## Requirements

None.

## Dependencies

None.

## Installation

Ansible 2+
----------

Using ansible galaxy cli:

```bash
ansible-galaxy install manala.proftpd
```

Using ansible galaxy requirements file:

```yaml
- src: manala.proftpd
```

Role Handlers
-------------

| Name              | Type    | Description            |
| ----------------- | ------- | ---------------------- |
| `proftpd restart` | Service | Restart proftpd server |

Role Variables
--------------

| Name                                | Default             | Type    | Description                                 |
| ----------------------------------- | ------------------- | ------- | ------------------------------------------- |
| `proftpd_configs`            | []                  | Array   | Configs                                     |
| `proftpd_configs_template`   | configs/empty.j2    | String  | Template to use to define a config set      |
| `proftpd_configs_exclusive`  | false               | Boolean | Exclusion of existings files                |
| `proftpd_configs_dir`        | /etc/proftpd/conf.d | String  | Path to the main configuration directory    |
| `proftpd_users_template`     | users/default.j2    | String  | Main user config template                   |
| `proftpd_users_file`         | /etc/ftpd.passwd    | String  | proFTPd user accounts definition file       |
| `proftpd_users`              | []                  | Array   | Array of proFTPd user accounts              |

### ProFTPd configuration

The `proftpd_configs_template` key will allow you to use differents main configuration templates. The role is shipped with basic templates :

- empty (Simple template with no default configuration)
- module (This configuration is used to handle modules definition (mod_ssl.c, mod_rewrite.c ...))

#### Example:
```yaml
proftpd_configs_template: configs/module.j2
```

The `proftpd_configs` key is made to allow you to define configuration based on choosen template format.

#### Example:

```yaml
proftpd_configs:
  - file:                   proftpd.conf
    config:
      - ServerName:         "Manala"
      - PassivePorts:       10000 10030
      - DefaultRoot:        "~"
      - AuthOrder:          mod_auth_file.c
      - AuthUserFile:       "/etc/ftpd.passwd"
      - RequireValidShell:  false
  - file:                   tls.conf
    template:               configs/module.j2
    name:                   mod_tls.c
    config:
      - TLSEngine:                  true
      - TLSLog:                     /var/log/proftpd/tls.log
      - TLSProtocol:                TLSv1
      - TLSCipherSuite:             AES256+EECDH:AES256+EDH
      - TLSOptions:                 NoCertRequest AllowClientRenegotiations
      - TLSRSACertificateFile:      /etc/ssl/private/certificates/*.elao.com.pem
      - TLSRSACertificateKeyFile:   /etc/ssl/private/certificates/*.elao.com.pem
      - TLSVerifyClient:            false
      - TLSRequired:                true
      - RequireValidShell:          "No"
```

### Exclusivity

`proftpd_configs_exclusive` allow you to clean up existing proFTPd configuration files into directory defined by the `proftpd_configs_dir` key. Made to be sure no old or manualy created files will alter current configuration.

```yaml
proftpd_configs_exclusive: true
```

### User account configuration

The `proftpd_users_template` key is made to define users allow to acces to FTP storage.

```yaml
proftpd_users:
    - name:             manala
      password:         "$1$KBijsXOEr4"b$9HEyZDLPnSe3SXq0n66oE3y/"
      home:             "/srv/my_dir"
      shell:            "/bin/false"
    - name:             toto
      password:         "$1$9f19dba0ce5ece883b53275dcc1721b9"
      home:             "/home/toto"
      shell:            "/bin/false"
```

Example playbook
----------------

```yaml
- hosts: servers
  roles:
    - { role: manala.nginx }
```

Tests
-----

Test suite require the following tools:

- Docker
- Manala test suite [**(Docker image)**](https://github.com/manala/docker-image-ansible-debian)

Licence
-------
MIT

Author information
------------------

Manala [**(http://www.manala.io/)**](http://www.manala.io) is an open source project supported by the french web agency [**(ELAO)**](http://www.elao.com)
