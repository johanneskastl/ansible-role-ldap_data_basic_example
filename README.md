![Ansible Lint](https://github.com/johanneskastl/ansible-role-ldap_data_basic_example/workflows/Ansible%20Lint/badge.svg)

ldap_data_basic_example
=========

OpenLDAP basic DIT with some OUs, some users and corresponding ACLs.

- one simpleSecurityObject `ldap-admin` with `manage` permissions on everything
- one simpleSecurityObject `sssd-user` with `read` permissions on everything
- three regular users (`posixAccount` and `inetOrgPerson`) to use for actual logins
- one `posixGroup` that the three regular users have as `gidNumber`

Requirements
------------

This role should be run against/on a LDAP server. Tested against OpenLDAP 2.4 on Debian 8/9/10, CentOS 7/8, openSUSE Leap 15.x and SLES15 currently.

The role will install any dependencies that Ansible's `ldap_attr` or `ldap_entry` modules need.

Role Variables
--------------

*Role logic*
This role has several parts, than can be enabled by setting some variables:
- `apply_ACLs`: (default: true) Whether or not to apply the ACLs
- `add_debugging_script_and_ldif_files`: (default: false) Whether debugging script and LDIF files should be created
- `remove_everything`: If you set this, the role will remove *EVERYTHING* it created, this includes files as well as directory entries. Only exception is the baseDN, as that cannot be deleted (easily).

*Naming details*

- `base_dn`: the desired baseDN, default value is `dc=training,dc=b1-systems,dc=de`
- `users_ou`: desired name of the OU for users, default value is `ou=users`
- `groups_ou`: desired name of the OU for groups, default value is `ou=groups`
- `rootdn_name`: relative DN of the rootDN, the base_dn will be added automatically (settings this to `cn=b1admin` will make it `cn=b1admin,dc=training,dc=b1-systems,dc=de` when using the default value for the baseDN)

*Password Hashes*
- `ldap_admin_password_hash`: (required) hashed password for the ldap-admin user
- `sssd_user_password_hash`: (required) hashed password for the sssd-user user
- `regular_users_password_hash`: (required) hashed password for the regular users

Please note, the regular users will share the same password. This is for testing purposes, obviously.

*Cleartext passwords*
In case you set the `add_debugging_script_and_ldif_files` variable, this role will create a debugging script and lots of LDIF files to help with debugging. You can then delete and manually create users, groups or set/delete the ACLs.

The debugging script needs the *cleartext* passwords of the users. This should obviously *not be used on productive servers with real passwords*!

- `rootdn_password`: cleartext password of the rootDN
- `ldap_admin_password`: cleartext password of the ldap-admin user
- `sssd_user_password`: cleartext password of the sssd-user
- `regular_users_password`: cleartext password of the regular users

Dependencies
------------

None

Example Playbook
----------------

A simple playbook that will only do the minimum will look like this:
```
- hosts: servers
  roles:
    - { role: 'johanneskastl.ldap_data_basic_example' }
```

To delete everything this role created, use a playbook like this:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_basic_example'
      vars:
       remove_everything: 'true' 
```


A playbook that will not only create the groups and users in LDAP but also create the debugging script and LDIF files will be a little more work:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_basic_example'
      vars:
        # create ACL debugging script
        add_debugging_script_and_ldif_files: 'true'
        rootdn_name: 'cn=Manager'
        # Password Hashes
        ldap_admin_password_hash: '{SSHA}0123456789abcdef}'
        sssd_user_password_hash: '{SSHA}567890abcdef12345'
        regular_users_password_hash: '{SSHA}abcdef123456789'
        # Passwords
        rootdn_password: 'totallysupersecret'
        ldap_admin_password: 'alsoreallysecret'
        sssd_user_password: 'shouldalsobesecret'
        regular_users_password: 'secret'
```

License
-------

BSD-3-Clause

Author Information
------------------

I am Johannes Kastl, reachable via kastl@b1-systems.de.
