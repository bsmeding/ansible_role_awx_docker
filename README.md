


# Dependencies:
Install collection awx.awx for configuration of AWX after installation
`ansible-galaxy collection install awx.awx`


# Ansible 18 and up

Currently not supported yet!



### Create super user
In new versions it is not possible to add super user via environment variables, we need to run a command in the container:
login to Docker host and execute following command to create superuser
`docker exec -ti tools_awx_1 awx-manage createsuperuser`
Answer questions (username, email, password)


# Create Virtual Environments (Ansible AWX till version 17.1)
For AWX versions till 17.1.0 a Python virtual environment is used. Those can be installed with this playbook by setting `custom_venv` variable with a list of virtual environments to create.
Also the path is needed `custom_venv_dir` that will be created on the host and linked to AWX container, this variable is then needed in AWX settings as custom_venv.

Optional a seperate Python version can be used for the venv when setting `python_version` variable. If not set it will use the OS default Python version.

```
custom_venv_delete_before_install: false
custom_venv_dir: /opt/awx/venv
# Custom python dir, need to build different virtual env
custom_python_dir: /opt/python
custom_venvs:
  - name: ansible2_10_3
    python_version: 3.6.8
    ansible_pip_packages:
    - ansible==2.10.3
    - ansible-lint
    - pynautobot
    - jmespath
    - napalm
    - netmiko
    - paramiko
```

Virtual env path will be automatically added to the AWX instance

## LDAP Config
If needed it is possible to add LDAP config, currently the default and _1_ LDAP servers are configured.

```
# awx__ldap_server_uri: ldap://ldap.example.com:3380
# awx__ldap_bind_dn: CN=readonly,OU=people,DC=example,DC=com
# awx__ldap_bind_password: password
# awx__ldap_group_type: NestedMemberDNGroupType
# awx__ldap_user_dn_template: uid=%(user)s,ou=people,dc=example,dc=com
# awx__ldap_require_group: cn=AWX,ou=groups,dc=example,dc=com
# awx__ldap_deny_group: ''
# awx__ldap_user_search: []
# awx__ldap_group_search: []
# awx__ldap_user_attr_map: {}
# awx__ldap_group_type_parameters: {}
# awx__ldap_user_flags_by_group: {}
# awx__ldap_organization_map: {}
# awx__ldap_team_map: {}

```

Example: confif for LDAP support and automatic group config

```
awx__ldap_server_uri: ldap://192.168.71.10:3380
awx__ldap_bind_dn: CN=readonly,OU=people,DC=example,DC=com
awx__ldap_bind_password: password
awx__ldap_group_type: NestedMemberDNGroupType
awx__ldap_user_dn_template: uid=%(user)s,ou=people,dc=example,dc=com
awx__ldap_require_group: cn=AWX,ou=groups,dc=example,dc=com
awx__ldap_deny_group: ''
awx__ldap_user_search: [
  "cn=people,dc=example,dc=com",
  "SCOPE_SUBTREE",
  "(uid=%(user)s)"
  ]
awx__ldap_group_search: [
  "ou=groups,dc=example,dc=com",
  "SCOPE_SUBTREE",
  "(objectClass=posixGroup)"
]
awx__ldap_user_attr_map: {
  "first_name": "givenName",
  "last_name": "sn"
}
awx__ldap_group_type_parameters: {
  "name_attr": "cn",
  "member_attr": "member"
}
awx__ldap_user_flags_by_group: {
  "is_superuser": [
    "cn=lldap_admin,ou=groups,dc=example,dc=com"
  ]
}
awx__ldap_organization_map: {}
awx__ldap_team_map: {
  "Netwerk beheer": {
    "users": [
      "cn=awx,ou=groups,dc=example,dc=com"
    ],
    "organization": "COA",
    "remove": true
  },
  "Netwerk developer": {
    "users": [
      "cn=awx__developer,ou=groups,dc=example,dc=com"
    ],
    "organization": "COA",
    "remove": true
  }
}

``
