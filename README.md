Patching module
=========
A module for patching (apllying security updates) Windows and Linux machines


Role Variables
--------------

| Variable Name | Default value | Possible values   | Documentation reference  | Description  |
| ------------- | ------------- | ----------------- | ------------------------ | ------------ |
| package_manager_pre_delay   | 0  | >=1   || How many secods to wait before installing the packages/updates<br/>(Linux only)|
| force_reboot   | false | true/false   || If the server NEEDS to be rebooted (will reboot the server anyway)<br/>`server_reboot_allowed` needs to be true since it takes precedente|
| package_manager_post_delay  | 0  | >=1   || How many secods to wait after the installation of the packages/updates<br/>(Linux only)   |
| server_post_reboot_delay    | 0  | >=1   || How many seconds to wait after the server is back up    |
| package_manager_apt_do_autoremove | No | Yes<br/>No  | [[apt/autoremove](https://docs.ansible.com/ansible/latest/modules/apt_module.html#parameter-autoremove)]   | Autoremove the unneeded packages (a version of the kernel is kept anyway)<br/>(Debian-based distros only)  |
| server_reboot_allowed  | Yes| Yes<br/>No  || Can the server reboot?|
| package_manager_excluded_packages | [] |  | [[yum/exclude](https://docs.ansible.com/ansible/latest/modules/yum_module.html#parameter-exclude)]<br/>[[dpkg_selections/name](https://docs.ansible.com/ansible/latest/modules/dpkg_selections_module.html#parameter-name)]<br/>[windows/blacklist](https://docs.ansible.com/ansible/latest/modules/win_updates_module.html#parameter-blacklist) | Packages to exclude from the update  |
| package_manager_needed_packages   | [] |  | [[yum/name](https://docs.ansible.com/ansible/latest/modules/yum_module.html#parameter-name)]<br/>[[apt/name](https://docs.ansible.com/ansible/latest/modules/apt_module.html#parameter-name)]   | Packages that needs to be installed on the server<br/>(Linux only) |
| windows_updates_source | "default"     | default<br/>managed_server<br/>windows_update | [[win_updates/server_selection]([win_updates/category_names](https://docs.ansible.com/ansible/latest/modules/win_updates_module.html#parameter-category_names)]    | Server to use when searching and downloading the updates<br/>(Windows only)    |
| windows_category_names | <undefined>   |  | [[win_updates/category_names](https://docs.ansible.com/ansible/latest/modules/win_updates_module.html#parameter-category_names)] | Windows udpate categories that needs to be installed.<br/>Defaults to any update found.<br/>(Windows only) |
| server_timeout_seconds_value | 3600 | >=1 | [[win_updates/reboot_timeout](https://docs.ansible.com/ansible/latest/modules/win_updates_module.html#parameter-reboot_timeout)]<br/>[[reboot/reboot_timeout](https://docs.ansible.com/ansible/latest/modules/reboot_module.html#parameter-reboot_timeout)] | How much time to wait for the server to come back up in case of a reboot |
| ram_test_enable              | true  | True<br/>False | | Weather to enable the check to make sure there is enough free RAM             |
| ram_test_noreboot            | false | True<br/>False | | If there is not enough RAM (and value is true), DO NOT reboot the server but rather fail the task |
| ram_test_min_free_mb         | 400   | >= 1           | | The needed MBs of RAM to check for (if there is not enough RAM, the server will be rebooted) |




Example Playbook
----------------
Here you can find an example playbook to test the role
```
---
- name: patching
  max_fail_percentage: 0
  hosts: all
  # On Windows the become can result in issues
  become: "{{ not ansible_env.OS is defined and 'windows' in ansible_env.OS|lower }}"
  roles:
  - patching
```
Just remember to set the right variables for the Windows machines:
```
# Port 5985 for HTTP, Port 5986 for HTTPS
ansible_port=5985
# Windows should implement SSH in their 2019 version of Windows server, for the moment we have WinRM
#   To enable it, open Powershell and type: `Enable-PSRemoting`
ansible_connection=winrm

# Refer to doc: https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html
ansible_winrm_transport=kerberos
#ansible_winrm_transport: ntlm
#ansible_winrm_transport: basic

ansible_user=Administrator@contoso.com
ansible_password=V3ryS3cr3tP4ssw0rD

# Used for servers with no valid certificate
ansible_winrm_server_cert_validation=ignore
```

Dependencies
-----------
None :)

License
-------

BSD

Author Information
------------------
Pandry <Pandry@protonmail.com> github.com/Pandry
