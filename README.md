



# Ansible a8 and up





### Create super user
In new versions it is not possible to add super user via environment variables, we need to run a command in the container:
login to Docker host and execute following command to create superuser
`docker exec -ti tools_awx_1 awx-manage createsuperuser`
Answer questions (username, email, password)


# Create Virtual Environments (Ansible AWEX till version 17.1)
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

## install en build VenV from AWX it self!
Because the AWX versions 17.1 and below are quite old by now, building the Python versions from a newer OS can cause problems with dependencies like `GLIB` for example: `/lib64/libc.so.6: version "GLIBC_2.34" not found (required by /opt/awx/venv/ansible4/bin/python)`
Therefor better to run this part of the role from the AWX itself (add repo to AWX, download and install on localhost)