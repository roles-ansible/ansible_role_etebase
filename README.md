[![MIT License](https://raw.githubusercontent.com/roles-ansible/ansible_role_etebase/main/.github/license.svg)](https://github.com/roles-ansible/ansible_role_etebase/blob/main/LICENSE)
[![Galaxy](https://raw.githubusercontent.com/roles-ansible/ansible_role_etebase/main/.github/galaxy.svg)](https://galaxy.ansible.com/do1jlr/etebase)

 Ansible role EteBase - EteSync 2.0 Server Backend
===================================================
Ansible role to Setup and Confugure Etebase - The Backend from [EteSync](https://www.etesync.com/) 2.0 -> [https://github.com/etesync/server](https://github.com/etesync/server.git).

 Details
---------
This Ansible role installs and configures etebase, the backend of etesync. A piece of software to securely sync your contacts, calendars, tasks and notes!
In this Ansible role, a separate user is created for etebase. The latest release of etebase is downloaded to the home of this user. A configuration is created. The specified Python dependencies are installed in a venv. And optionally etebase can be started automatically via a systemd service and uvicorn.

This Ansible role does not create users in Etebase. And the configuration for the web server is not created either. More about this in the [Additional Information](#additional-information) section.

 Default Variables
-----------
| variable | value | description |
| -------- | ----- | ----------- |
| etebase__user | ``etebase`` | The Unix User for etebase |
| etebase__group | ``etebase`` | The Unix Group for etebase |
| etebase__user_home | ``/var/lib/etebase`` | Etebase User Home |
| etebase__shell | ``/bin/false`` | Default Shell of Etebase User |
| etebase__venv_path | ``{{ etebase__user_home }}/venv`` | Etebase venv path |
| etebase__socket | ``/tmp/etebase_server.sock`` | Etebase Socket path *(only if ``etebase__systemd_setup`` is set to ``true``)* |
| etebase__package_state | ``present`` | Set to ``latest`` to upgrade all etebase required system and pip packages to the latest version |
| etebase__version | ``latest`` | Etebase Release Tag |
| etebase__secrets_dir | ``{{ etebase__user_home }}/secrets`` | Path to store etebase secrets |
| etebase__collectstatic | ``true`` | Generate static files for etebase |
| etebase__restart_webserver | ``false`` | Set to ``true`` to restart the webserver on config change *(etebase__systemd_setup needed)*|
| etebase__webserver_service | ``nginx.service`` | Which systemd unit should be restartet for the webserver |
| etebase__systemd_setup | ``false`` | Set to ``true`` to start etebase as systemd unit with the systemd socket configured above |
| submodules_versioncheck | ``false`` | should we do a simple version check for this ansible role |


 Options for etebase-server.ini
------------------------------

| variable | value | description |
| -------- | ----- | ----------- |
| etebase__global_secret_file | ``{{ etebase__secrets_dir }}/secret.txt`` | path of secret.txt
| etebase__global_debug | ``false`` | Set debug to true |
| etebase__global_static_root | ``{{ etebase__user_home }}/static_root`` | Path of static root |
| etebase__global_media_root | ``{{ etebase__user_home }}/media_root`` | Path for media |
| etebase__global_extra | | Variable for aditional parameter in the ``[global]`` section of the config file |
| etebase__allowed_hosts_allowed_host1 | ``*`` | The allowed Host for this etebase server | 
| etebase__allowed_hosts_extra | |Variable for aditional parameter in the ``[allowed_hosts]`` section of the config file |
| etebase__database_engine | ``django.db.backends.sqlite3`` | Databse Engine |
| etebase__database_name | ``{{ etebase__secrets_dir }}/etebase.db.sqlite3`` | Path of the sqlite3 database |
| etebase__database_extra | | Variable for aditional parametet in the ``[database]`` section of the config file |
| etebase__database_options_extra | | Variable for aditional parameter in the ``[database_options]`` section of the config file |
| etebase__ldap_extra | | Variable for aditional parameter in the ``[ldap]`` section of the config file |
| etebase__config_extra | |Variable for aditional parameter at the end of the config file |

 Additional Information
------------------------
You find more information about the webserver config at [github.com/etesync/server/wiki/Production-setup-using-Nginx](https://github.com/etesync/server/wiki/Production-setup-using-Nginx). Please remember the value you used for the ``etebase__socket`` variable, if you used this role to start the [uvicorn](https://www.uvicorn.org/) ASGI server via systemd. For this you have to set ``etebase__systemd_setup`` to ``true``.

You have to create a admin User by yourself. To do this, log in manually as priviledged user, change to the ``etebase__user_home``. Enter the downloaded etebase code direcotory and run the ``python3 ./manage.py createsuperuser`` command in the venv:
```bash
# go to etebase home
cd /var/lib/etebase/

# change to latest etebase version
ls -l etebase_*
cd etebase_v0.10.0  # example versiom

# enable latest venv
ls -l /var/lib/etebase/venv
source /var/lib/etebase/venv/v0.10.0/bin/activate  # example version

# create new superuser
python3 ./manage.py createsuperuser
```

By the way, this role requires that the Ansible user be allowed to execute commands with sudo privileges.

 Example Playbook
------------------
```yml
---
- name: Install etebase server at example.com
  hosts: example.com
  roles:
    - {role: do1jlr.etebase, tags: etebase}
  vars:
    etebase__allowed_hosts_allowed_host1: 'example.com'
    etebase__systemd_setup: true
    submodules_versioncheck: true
```

 Contributing
--------------
Don't hesitate to open a issue or *(even better)* create a pull request.
