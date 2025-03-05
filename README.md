
# Ansible Collection - torie_coding.keepass

These Ansible modules allow you to manage entries and groups in a KeePass (kdbx) database.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Modules](#modules)
  - [entry](#entry)
  - [group](#group)
  - [lookup](#lookup)
- [Installation](#installation)
- [Usage](#usage)
- [Examples](#examples)
- [License](#license)

## Prerequisites

Before using these Ansible modules, you need to ensure the following prerequisites are met:

- Ansible installed on your system.
- Python module "pykeepass" installed. You can install it using `pip install pykeepass`.

## Modules

### entry

This Ansible module allows you to manage entries in a KeePass (kdbx) database.

#### Parameters

- `database` (required): Path to the KeePass database.
- `title` (required): Title of the entry you want to manage.
- `username` (required): Username of the entry.
- `action` (required): The action to perform (create, modify, delete).
- `keyfile`: Path to the KeePass key file. Either this or 'database_password' (or both) are required.
- `database_password`: Database password. Either this or 'keyfile' (or both) are required.
- `password_length`: The length of the password that should be generated (only needed if no password is provided). Default length for action 'create' is 20.
- `password`: The password that will be set for the entry (if on action 'create' no password is given, a password will be generated with given 'password_length').
- `url`: URL that will be set for the KeePass entry.
- `notes`: Notes for the entry. Default is 'Generated by ansible.'
- `group_path`: Group path in which to place the KeePass entry. If no value is given it will be created under the root directory.
- `icon_id`: Icon ID to be associated with the KeePass entry.

#### Example

```yaml
- name: Create a new entry in KeePass
  torie_coding.keepass.entry:
    action: create
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    title: MyNewEntry
    username: myusername
    password: mypassword
    url: https://example.com
    notes: This is a new entry created by Ansible.
    group_path: foo/bar/MyGroup
    icon_id: 48
  register: entry

- name: Create a new entry in KeePass with generated password
  torie_coding.keepass.entry:
    action: create
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    title: MyNewEntry
    username: myusername
    password_length: 16
    url: https://example.com
    group_path: foo/bar/MyGroup
    icon_id: 48
  register: entry

- name: Modify the URL of an entry in KeePass
  torie_coding.keepass.entry:
    action: modify
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    keyfile: /path/to/KeyFile
    title: MyNewEntry
    url: https://example.com
  register: entry

- debug:
    var: entry

- name: Delete an entry in KeePass
  torie_coding.keepass.entry:
    action: delete
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    title: MyNewEntry
  register: entry

- debug:
    var: entry
```

### group

This Ansible module allows you to manage groups in a KeePass (kdbx) database.

#### Parameters

- `database` (required): Path to the KeePass database.
- `name` (required): Name of the group you want to manage.
- `action` (required): The action to perform (create, modify, delete).
- `keyfile`: Path of the KeePass key file. Either this or 'database_password' (or both) are required.
- `database_password`: Database password. Either this or 'keyfile' (or both) are required.
- `icon_id`: Icon ID to be associated with the group. Defaults to '48'.
- `notes`: Notes for the group. Default is 'Generated by ansible.'
- `new_name`: The new name for the group (only for modifications).
- `path`: The path of the Group. Without the Groupname.
- `create_path`: Indicator that specifies whether the path should be created if it doesn't exist.

#### Example

```yaml
- name: Create a new group in KeePass
  torie_coding.keepass.group:
    action: create
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    name: MyNewGroup
    icon_id: 49
    notes: This is a new group created by Ansible.
    path: foo/bar/group
    create_path: true
  register: group

- debug:
    var: group

- name: Modify a group name in KeePass
  torie_coding.keepass.group:
    action: modify
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    keyfile: /path/to/KeyFile
    name: MyNewGroup
    new_name: MyAwesomeNewGroup
    group_path: foo/bar/MyGroup
  register: group

- debug:
    var: group

- name: Delete a group in KeePass
  torie_coding.keepass.group:
    action: delete
    database: /path/to/KeePass_database.kdbx
    database_password: "your_database_password"
    name: MyAwesomeNewGroup
    group_path: foo/bar/MyGroup
  register: group

- debug:
    var: group
```

### lookup

This Ansible lookup plugin allows you to search for entries in a KeePass (kdbx) database.

#### Parameters

- `_terms` (required): For what you are looking for.
- `database` (required): Path to the KeePass database file.
- `database_password`: Password to unlock the KeePass database.
- `keyfile`: Path to the keyfile to unlock the KeePass database.
- `title`: Filter entries by title.
- `group_path`: Filter entries by group path.
- `regex`: Whether to use regular expressions for filtering.
- `recursive`: Whether to search recursively in groups.
- `notes`: Filter entries by notes.
- `url`: Filter entries by URL.
- `tags`: Filter entries by tags.
- `username`: Filter entries by username.

#### Example

```yaml
- name: Find an entry by title in KeePass database
  debug:
    msg: "{{ lookup('torie_coding.keepass.lookup', 'entry', database='/path/to/database.kdbx', database_password='secret', title='My Entry') }}"

- name: Find entries in a specific group
  debug:
    msg: "{{ lookup('torie_coding.keepass.lookup', 'entry', database='/path/to/database.kdbx', database_password='secret', group_path='My Group', recursive=False) }}"

- name: Find an entry by title in a specific group
  debug:
    msg: "{{ lookup('torie_coding.keepass.lookup', 'entry', database='/path/to/database.kdbx', database_password='secret', group_path='My Group', title='My Entry', recursive=False) }}"

- name: Find entries with a specific tag
  debug:
    msg: "{{ lookup('torie_coding.keepass.lookup', 'entry', database='/path/to/database.kdbx', database_password='secret', tags=['important']) }}"
```

## Installation

Before you can use this module, make sure you have Ansible and pykeepass installed on your system. Additionally, you need to install the "torie_coding.keepass" Ansible collection. You can do this using Ansible Galaxy, which is Ansible's official hub for sharing Ansible content.

To install the "torie_coding.keepass" collection, run the following command:

```shell
ansible-galaxy collection install torie_coding.keepass
```

## License

This Ansible collection is open-source and available under The Unlicense, a permissive open-source license. You can find the full text of the license in the [LICENSE](LICENSE) file included in this repository.

You are free to use, modify, and distribute this collection as you see fit. There are no restrictions or obligations imposed by the license, so feel free to adapt it to your needs.

We encourage contributions to this collection from the community. If you find issues, have ideas for improvements, or want to add new features, please consider contributing to make this collection even better.

For any questions or concerns related to the license or usage of this collection, please contact us at [coding@thepatchwork.de](mailto:coding@thepatchwork.de).
