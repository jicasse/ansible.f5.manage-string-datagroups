ansible.f5.manage-string-datagroups
=========

This role will be used to manage string datagroups content on F5 bigip devices.
This is useful for example if you are writing irules using content in datagroup (for rewriting for example).

The idea at the end is to have a generic irule using datagroup named as follow : 

    < virtual server name >_< suffix >

These datagroups can be used for what you want : 
- defining rewrite_rules
- defining a list of users
- defining flags for specific needs
- ...

Requirements
------------

To use this role you'll need to have :

- ansible >= 2.7
- f5-sdk python library; please refer to the following [link](https://f5-sdk.readthedocs.io/en/latest/) to have further information regarding this sdk
- F5 BIGIP >= 12

Additionally you'll need to **define all required variables** to have all working as expected.

Role Variables
--------------

Some variables are defined as defaults in the roles, so in `defaults/main.yml` file.
It's not necessary to change these variables.
Here are the variables concerned and a brief explanation of what they are for : 

> f5_delegate_to: 'localhost'

> f5_validate_certs: 'no'

Some variables should be host_vars; these are mainly variables used to access BIGIP appliances.

> ansible_connection: local

> f5_user: ansible

This variable contains the user used to access F5 appliance

> f5_passwd: password

This variable contains the password used to access F5 appliance.
Use ansible vault or another vault for security reason.

> f5_host: 1.1.1.1

This variable contains the IP address or hostname of the bigip.

Others variables required in the role are variables used for the main purpose of this role.
Here is the list of these variables : 

> vs_name: 'myvirtualserver'

The virtual server name on which the configuration will be applied.
This virtual server name is used to name the datagroups created or updated.

> datagroups

This variable contains : 
- datagroup suffixes
- datagroup content

As explained in the description the objective is to create datagroups named `<virtual server name>_<suffix>`.
You have first to keep this in mind if you need to use this role.
Another important thing is that the content should be a list of dict with `key` and `value` attributes.

You'll find in the example below an example of `datagroups` variable format.


Dependencies
------------
ansible.f5.get-ha-status

Example Playbook
----------------

Here is a simple example of a playbook using this role and a use case : 

Imagine you have an irule used to do some things but you need to enable or disable logging (local or hsl logging) without modifying the irule.
You can imagine an irule using a datagroup with flags for remote and local logging. 

To do that you can have the following variables for your hosts : 

    vs_name: 'myvirtualserver'
    datagroups:
     - { suffix: "debug-logging", 
         content: [{ key: 'local_logging', value: '1' }, 
                   { key: 'remote_logging', value: '0' }] 
       }
By using the simple playbook example below, at the end you'll have datagroups named `myvirtualserver_debug-logging` with the following content:

    local_logging := '1'
    remote_logging := '0'

Here is the playbook example : 

    - hosts: f5
      roles:
         - ansible.f5.get-ha-status 
         - ansible.f5.manage-string-datagroups

If your irule has been done to retrieve content of a datagroup named `<virtual server name>_debug-logging` to check `remote_logging` and `local_logging` flags, you will be able to enable or disable logging without changing irules content.

This is a simple use case; I let you imagine what you can do by doing generic/standard irules which are using datagroups named `<virtual server name>_<suffix>`

License
-------

BSD

Author Information
------------------

Jimmy Casse
