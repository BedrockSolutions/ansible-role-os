# OS
Ansible role containing various operating system-related commands and handlers.

## Handlers

### `reboot`

Causes the `reboot_if_required` command to initiate a reboot.

## Tasks/Commands

### `reboot`

Reboots the machine. 

* Flushes all handlers beforehand.
* After the reboot, waits for the machine's ssh connection 
to return before proceeding.

#### Example

```yaml
- import_role:
    name: jcheroske.common
  vars:
    common:
      command: reboot
```

### `reboot_if_required`

Reboots the machine if the `reboot` handler had been previously
called, or if the `/var/run/reboot-required` file exists. 

* Flushes all handlers beforehand.
* After the reboot, waits for the machine's ssh connection 
to return before proceeding.

#### Example, using the handler

Notify the reboot handler:

```yaml
- some_task:
  notify: reboot
```

At a later point in the play where a conditional reboot is desired:

```yaml
- import_role:
    name: jcheroske.common
  vars:
    common:
      command: reboot_if_required
```

### `shutdown`

Performs an immediate system shutdown.

#### Example

```yaml
- import_role:
    name: jcheroske.common
  vars:
    common:
      command: shutdown
```

### `update_packages`

Updates the `apt` package cache.

#### Example

```yaml
- import_role:
    name: jcheroske.common
  vars:
    common:
      command: update_packages
```

### `upgrade_packages`

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

```yaml
- import_role:
    name: jcheroske.common
  vars:
    common:
      command: upgrade_packages
      update_cache: yes
```
