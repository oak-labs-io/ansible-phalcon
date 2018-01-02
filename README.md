OakLabs.Phalcon
===============

> PHP Phalcon extension for Debian and Ubuntu.

Requirements
------------

PHP needs to be already installed, as well as git.

Role Variables
--------------

- phalcon_version: (optional) Specify Phalcon version. If not set, it installs latest version on repository. Example: `3.3.0`.

Example Playbook
----------------

```yml
---
- hosts: all
  remote_user: root

  pre_tasks:
  - name: Update apt cache if needed
    apt: update_cache=yes cache_valid_time=3600

  - name: Install git
    apt:
      name: git
      update_cache: yes

  - name: Install php-config
    apt:
      name: php-dev
      update_cache: yes

  roles:
    - oakLabs.phalcon

  post_tasks:
    - name: SHELL | Ensure Phalcon is installed and enabled
      shell: php -i | grep -i phalcon
      changed_when: false

    - name: COMMAND | Display Phalcon version
      command: php -r 'echo Phalcon\Version::get() . "\n";'
      changed_when: false
```

Testing
-------

Just run `vagrant up`.

License
-------

MIT

Author Information
------------------

[OakLabs GmbH](http://oak-labs.com/)
