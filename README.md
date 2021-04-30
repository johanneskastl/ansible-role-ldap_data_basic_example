ldap_data_basic_example
=========

OpenLDAP basic DIT with some OUs, some users and corresponding ACLs.

- one simpleSecurityObject `ldap-admin` with `manage` permissions on everything
- one simpleSecurityObject `sssd-user` with `read` permissions on everything
- three regular users (`posixAccount` and `inetOrgPerson`) to use for actual logins

Requirements
------------

None.

Role Variables
--------------

- `ldap_admin_password_hash`: (required) hashed password for the ldap-admin user
- `regular_users_password_hash`: (required) hashed password for the regular users

Dependencies
------------

None

Example Playbook
----------------

    - hosts: servers
      roles:
        - { role: 'johanneskastl.ldap_data_basic_example' }

License
-------

BSD-3-Clause

Author Information
------------------

I am Johannes Kastl, reachable via kastl@b1-systems.de.
