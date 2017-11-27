ovv.miniflux
============

[![Build Status](https://travis-ci.org/ovv/ansible-role-miniflux.svg?branch=master)](https://travis-ci.org/ovv/ansible-role-miniflux)

Ansible role to install and configure [Miniflux](https://miniflux.net/).

Requirements
------------

A php and nginx installation are required. We recommend using [ovv.php7](https://github.com/ovv/ansible-role-php7)
and [pyslackers.nginx](https://github.com/pyslackers/ansible-role-nginx).

Installation
------------

To install this roles clone it into your roles directory.

```bash
$ git clone https://github.com/ovv/ansible-role-miniflux.git ovv.miniflux
```

If your playbook already reside inside a git repository you can clone it by using git submodules.

```bash
$ git submodule add -b master https://github.com/ovv/ansible-role-miniflux.git ovv.miniflux
```

Role Variables
--------------

* `paperless_user`: Paperless admin user.
* `paperless_email`: Paperless admin email.
* `paperless_password`: Paperless admin password.
* `paperless_encrypt_passphrase`: Paperless encryption passphrase.
* `paperless_secret_key`: Paperless secret key.

* `paperless_allowed_host`: List of hosts allowed to connect (default to `[127.0.0.1]`).
* `paperless_ocr_language`: Default ocr language (default to `eng`).
* `paperless_tz`: Timezone (default to `Etc/UTC`).
* `paperless_list_per_page`: Number of item per page (default to `100`).
* `paperless_consumption_dir`: Consumption directory for paperless (default to `/opt/paperless/consumption`).
* `paperless_custom_packages`: Custom packages to install, like some tesseract languages (default to `[]`).

Some other variable and their defaults are located in [defaults](defaults/main.yml).


Example Playbook
----------------

```yml
- hosts: localhost
  roles:
    - ovv.php7
    - ovv.miniflux
    - pyslackers.nginx
  vars:
    miniflux_user: user
    miniflux_password: password

    # ovv.php7
    custom_php_packages:
      - php7.0-xml
      - php7.0-sqlite3

    php_pools:
      miniflux:
        socket: /var/run/php7.0-fpm-miniflux.sock
        user: miniflux
        working_dir: /opt/miniflux

    # pyslackers.nginx
    nginx_sites:
      miniflux:
        directory: /opt/miniflux
        locations:
          data:
            location: /data
            custom: |
              deny all;
              return 404;
          php:
            location: ~ \.php$
            custom: |
              fastcgi_pass   unix:{{ php_pools['miniflux']['socket'] }};
              fastcgi_index  index.php;
              include        fastcgi.conf;
```

License
-------

MIT
