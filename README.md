Ansible Role: IDM Server (FreeIPA) 
=========

The ansible role is used to install the IDM server on a Centos 8 server.  If you have another OS, you will need
to adapt the role for your OS.

Requirements
------------

1.  Centos 8 Operating System
1.  Python Virtual Environment with the following installed:

    1. **ansible==2.9**
 
1. If you intend to use the **molecule** tests, do the following:

    1. Install the following in your Python virtual environment:
    
        1. **molecule==3.0.4**
        1. **molecule[docker]**
        1. **boto**
        1. **boto3**
    
    1. Create the Ansible Vault secret file in the **role** directory.  The file has to be named
       **vault_secret**.  The secret decrypts the IDM server admin password when tests are run.
       
    1. If you plan on running the **aws** scenario, you must perform thee following in the **roles** directory:
    
        1. Create the **files** directory
        1. cd **files**
        1. Create a file with same name as your AWS private key name.  The file must have no extension.
        1. Add the AWS private key to the file created in the previous step. 

Role Variables
--------------

1. **defaults/main.yml** variables:

    1. **idm\_domain\_name** --> The name of the IDM domain name.
     
     1. **idm\_fqdn** --> The fully qualified domain name for the IDM server.
         
     1. **idm\_admin\_password** --> The IDM server **admin** user password.  This value is encrypted using 
        Ansible Vault.  You will need to encrypt your password for your IDM server via **ansible vault** 
        and replace the contents here.
        
1. **vars/main.yml** variables:

    1. **open\_idm\_ports** --> The list of ports to open for the target server to communicate with the IDM server.
    
Dependencies
------------

None.

Example Playbook
----------------

    ---
    - name: Register server as IDM client
      hosts: idm_server
      become: true
      vars:
        idm_domain_name: example2020.com
        idm_fqdn: "idm.{{ idm_domain_name }}"
        idm_admin_password: !vault |
                              $ANSIBLE_VAULT;1.1;AES256
                              32353038343331663263323034313138613639626632323636646530393164626565613535356132
                              6236656566353566323163626137366535646563636333310a626666636639656339653633646461
                              63396639326130363661666631653861313036343063323433323638653738646639613830396466
                              3937346165346565640a633434366562396566623266353636343138306438626230313136396133
                              3961

      roles:
        - idm-server-install-role



The **vars** should contain the values you are using to override the **default/main.yml**
role variables.  Look above for a description of those variables.  Remember to
substitute your IDM Server **admin** password as an encrypted ansible vault
string for the **idm\_admin\_password** variable.

The playbook runs with a custom **ansible.cfg** with the following values set:

```text
inventory = inventory
remote_user = centos
private_key_file = ./my_keypair
```

The playbook **inventory** file contains the following:

```text
[all]
idm_server ansible_host=100.25.40.101
```
You will need to change the ip address to your target server ip address.

The ansible playbook command was:  `ansible-playbook  create_idm_server.yml --ask-vault-pass`

The **create_idm_server.yml** contains the playbook example code above.

License
-------

BSD

Author Information
------------------

This role was created in 2020 by Bret Mullinix.