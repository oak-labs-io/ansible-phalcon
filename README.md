OakLabs.Phalcon
===============

> PHP Phalcon extension for Debian and Ubuntu.

Requirements
------------

PHP and php-dev need to be already installed, as well as git.

Role Variables
--------------

- phalcon_version: (optional) Specify Phalcon version. If not set, it installs latest version on repository. Example: `3.3.0`.

Example Playbooks
-----------------

**Simple playbook assuming PHP has already been installed**

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

**Playbook which first installs PHP**

```yml
- hosts: all
  remote_user: root
  vars:
      php_enable_webserver: false
      php_packages:
        - php
        - php-cli
        - php-dev

  pre_tasks:
  - name: Update apt cache if needed
    apt: update_cache=yes cache_valid_time=3600

  - name: Remove existing versions of php
    apt:
      name: "{{ item }}"
      state: absent
      update_cache: yes
    with_items:
      - php*

  - name: Install needed base packages
    apt:
      name: "{{ item }}"
      state: present
      update_cache: yes
    with_items:
      - apt-transport-https
      - git

  - name: Add ondrej repository for later versions of PHP
    apt_repository: repo='ppa:ondrej/php' update_cache=yes
    when: ansible_distribution == 'Ubuntu'

  - name: Get https://deb.sury.org/ GPG Key
    get_url:
      url: https://packages.sury.org/php/apt.gpg
      dest: /etc/apt/trusted.gpg.d/php.gpg
      mode: 0444
    when: ansible_distribution == 'Debian'

  - name: Get SO version name
    command: "lsb_release -cs"
    register: version
    changed_when: false
    when: ansible_distribution == 'Debian'

  - name: Install https://deb.sury.org/ repo for later versions of PHP
    apt_repository:
      repo: "{{ item }}"
      state: present
      filename: 'php'
    with_items:
      - deb https://packages.sury.org/php/ {{ version.stdout }} main
    when: ansible_distribution == 'Debian'

  roles:
    - geerlingguy.php
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
