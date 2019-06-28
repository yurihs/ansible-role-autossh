# Ansible Role: autossh

[![Ansible Galaxy Badge](https://img.shields.io/ansible/role/41602.svg)](https://galaxy.ansible.com/yurihs/autossh)

- Installs autossh and systemd services to run it and keep SSH tunnels open.
- Scans for host keys, to avoid host key checking prompts.

Forked from [memiah/ansible-role-autossh](https://github.com/memiah/ansible-role-autossh) to change targeted systems to Debian instead of RedHat.

We assume that the SSH user exists and has the correct permissions to access
the remote server. A corresponding SSH key must also be present and available
on the local server.


## Role variables (default values)

~~~
autossh_connections:
  - id: "example" # Simple unique connection identifier (characters: "a-z,0-9,-").
    user: "" # Username used to connect to remote server.
    server: "" # IP / hostname of remote server.
    server_key_type: "" # Key type of the remote server, defaults to autossh_default_server_key_type. (Optional)
    local_port: "" # Local port to forward.
    dest_server: "" # IP / hostname to use on the remote server, defaults to autossh_default_dest_server. (Optional)
    dest_port: "" # Port on the remote server to connect to.
    identity_file: "" # Path to SSH private key, defaults to autossh_default_ssh_key_path. (Optional)
~~~

Add a set of SSH connection properties per connection. Multiple connections
can be listed here.

~~~yaml
autossh_path: "/usr/bin/autossh"
~~~

Path to autossh.

~~~yaml
autossh_ssh_dir: "/root/.ssh"
~~~

Directory to store SSH configuration.

~~~yaml
autossh_systemd_dir: "/etc/systemd/system/"
~~~

Path to systemd system dir.

~~~yaml
autossh_known_hosts_file: "{{ autossh_ssh_dir }}/known_hosts"
~~~

Path to known hosts file.

~~~yaml
autossh_default_server_key_type: "ecdsa"
~~~

Default key type of the remote server. Override this value for an single 
connection using the `server_key_type` option in the `autossh_connections` 
list. 

~~~yaml
autossh_default_dest_server: "127.0.0.1"
~~~

Default IP / hostname to use on the remote server, this will most likely 
be localhost / 127.0.0.1. Override this value for an single connection 
using the `dest_server` option in the `autossh_connections` list. 

~~~yaml
autossh_default_identity_file: "/root/.ssh/id_rsa"
~~~

Path to file from which the identity (private key) for public key 
authentication is read. The default is `/root/.ssh/id_rsa` for 
protocol version 2. Override this value for an single connection using 
the `identity_file` option in the `autossh_connections` list. 


## Example

Forward local port `33061` to port `3306` on `db.example.com`, connecting
via SSH as `backup@db.example.com`.

~~~yaml
- hosts: backup
  vars:
    autossh_connections:
      - id: mysql
        user: backup
        server: db.example.com
        local_port: '33061'
        dest_port: '3306'
  roles:
    - role: yurihs.autossh
      become: true
~~~

This will create a new service named `autossh-backup.service`, which can be
managed by systemctl:

~~~sh
systemctl status autossh-backup.service
systemctl restart autossh-backup.service
...
~~~

## Authors

This role was created in 2016 by [Memiah Limited](https://github.com/memiah).

Debian adaptation in 2019 by [yurihs](https://github.com/yurihs).
