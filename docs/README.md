# bedrock.os

Ansible role containing various operating system-related commands 
and handlers.

A prettier version of this documentation is located at 
https://bedrocksolutions.github.io/ansible-role-os

* Commands
  * [harden](#harden)
  * [reboot](#reboot)
  * [reboot_if_required](#reboot_if_required)
  * [shutdown](#shutdown)
  * [swap](#swap)
  * [ufw_install](#ufw_install)
  * [unattended_upgrades_install](#unattended_upgrades_install)
  * [update_packages](#update_packages)
  * [upgrade_packages](#upgrade_packages)

* Handlers

  * [reboot](#reboot \(handler\))
  * [ufw_rules_changed](#ufw_rules_changed)

## Installation

To use `bedrock.os` in another role, create the file 
`<role_root>/meta/main.yml` with the following structure:

```yaml
dependencies:
  - name: bedrock.os
    scm: git
    src: https://github.com/BedrockSolutions/ansible-role-os.git
    vars:
      os:
        command: dependency
    version: master
```

If you just want to use this role in a playbook or task file, then
add an entry to the `requirements.yml` file:

```yaml
- name: bedrock.os
  scm: git
  src: https://github.com/BedrockSolutions/ansible-role-os.git
  version: master
```
>__Note:__ In both examples, the `version` field can be a branch, tag, or commit hash.

The role can now be imported or included as `bedrock.os`.

## Command Invocation

The role is made up of commands that can be invoked using the following 
syntax:

```yaml
- import_role
    name: bedrock.os
  vars:
    os:
      command: <command_name>
      ...command_vars
```

or

```yaml
- include_role
    name: bedrock.os
  vars:
    os:
      command: <command_name>
      ...command_vars
```

## Commands

### __harden__

Hardens the operating system. This command leverages the awesome
[dev-sec/ansible-os-hardening](https://github.com/dev-sec/ansible-os-hardening)
role to perform the bulk of the hardening operations.

#### Arguments

* __`kernel_ip_forwarding`:__ should IP forwarding be enabled
in the kernel
    * type: boolean
    * default: `false`
  
* __`ufw_manage_defaults`:__ should the UFW default policy be
set by this command
    * type: boolean
    * default: `false`
  
#### Example

Harden the operating system and enable IP forwarding:

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: harden
      kernel_ip_forwarding: yes
```

### __reboot__

Reboots the machine. 

* Flushes all handlers beforehand.
* After the reboot, waits for the machine's ssh connection 
to return before proceeding.

#### Example

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: reboot
```

### __reboot_if_required__

Reboots the machine if the `reboot` handler had been previously
called, or if the `/var/run/reboot-required` file exists. 

* Flushes all handlers beforehand.
* After the reboot, waits for the machine's ssh connection 
to return before proceeding.

#### Example

Notify the reboot handler:

```yaml
- some_task:
  notify: reboot
```

At a later point in the play where a conditional reboot is desired:

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: reboot_if_required
```

### __shutdown__

Performs an immediate system shutdown.

#### Example

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: shutdown
```

### __update_packages__

Updates the `apt` package cache.

#### Example

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: update_packages
```

### __upgrade_packages__

Upgrades installed packages

#### Parameters

* __`autoclean`:__ cleans the local repository of retrieved 
package files that can no longer be downloaded
    * type: boolean
    * default: `true`

* __`autoremove`:__ remove unused dependency packages
    * type: boolean
    * default: `true`

* __`force_apt_get`:__ force usage of apt-get instead of aptitude
    * type: boolean
    * default: `true`

* __`type`:__ type of upgrade to perform
    * type: string
    * enum: `['full', 'safe']`
    * default: `'safe'`

* __`update_cache`:__ update the cache before running upgrade
    * type: boolean
    * default: `false`

#### Example

Update the package cache and then upgrade packages:

```yaml
- import_role:
    name: bedrock.os
  vars:
    os:
      command: upgrade_packages
      update_cache: yes
```

## Handlers

### __reboot (handler)__

Causes the `reboot_if_required` command to initiate a reboot
when it is called.
