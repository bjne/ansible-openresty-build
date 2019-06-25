ansible-openresty-build
=====

An [Ansible][ansible] role for building [openresty][openresty] binary.
Currently links both libraries and lua modules statically into nginx binary,
but this will be made configurable

[ansible]: http://www.ansible.com/
[openresty]: https://github.com/openresty/openresty

Status
------

This is work in progress, but should be working

Requirements
------------

Ansible, git and build essentials

Role Variables
--------------

Various defaults can be modified, look at `defaults/main.yml`
External modules can be included by adding putting them in either `openresty_modules`
or `openresty_dynamic_modules` array, example:

group_vars/all.yml
```
openresty_modules:
- some_module
```

The modules in the array must have a ansible role available in path, and must also
add its own path to openresty_module_path dict, example:

some_module/tasks/main.yml
```
- set_fact:
    openresty_module_path: >-
      {{ openresty_module_path|default({})|combine({ module_name: '/usr/src/some_module' })}}
```

Usage
-----

```
mkdir -p openresty-build/{roles,group_vars} && cd openresty-build
cat >ansible.cfg <<EOF
[defaults]
inventory = hosts.ini
remote_user = root
EOF

cat >hosts.ini <<EOF
[build]
localhost
EOF

cat >site.yml <<EOF
- hosts: build
  gather_facts: false
  roles:
    ansible-openresty-build
EOF

git clone https://github.com/bjne/ansible-openresty-build roles/ansible-openresty-build

# eventually add modules and change defaults in group_vars/build.yml

ansible-playbook site.yml
```

Adding custom patches
-----------------------

Patches are stored in patches/{module}/{version}/{patch}.patch
Patches that are defined in openresty_patches are applied pre openresty
configure.

patches have to be defined for the exact version of the module that is packed
with the openresty version to be build.

Author Information
------------------

[Bjørnar Ness](https://github.com/bjne)  
