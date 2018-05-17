# update-fg-urlfilter

This is a simple playbook to update (add, delete items) static URL filter at Fortinet FortiGate UTM devices.
URL filter items should be placed inside `urls.json` file beforehand.

### How to run

Install ansible 2.5+ and fortiosapi. 

Example installation for ubuntu/trusty64:

```
sudo apt update
sudo apt -y install git python-pip python-dev python3-dev libssl-dev
sudo pip install setuptools bcrypt cffi enum cryptography markupsafe pyasn1 pynacl urllib3 requests
sudo pip install ansible fortiosapi
```

Check installation:

```
vagrant@vagrant-ubuntu-trusty-64:~$ ansible --version
ansible 2.5.2
  config file = None
  configured module search path = [u'/home/vagrant/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python2.7/dist-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 2.7.6 (default, Nov 23 2017, 15:49:48) [GCC 4.8.4]
```

Clone Ansible playbook from GitHub to local host:

```
git clone https://github.com/pavelrn/update-fg-urlfilter.git
cd update-fg-urlfilter/
```

In `update-urls.yml' check connection parameters to match your environment:

```
hosts: localhost
  vars:
   host: "<FG IP@>"
   username: "<admin username>"
   password: "<admin password>"
   vdom: "<VDOM name to make changes to>"

```

Paste filtered URLS into `urls.json`.

Run playbook:

```
vagrant@vagrant-ubuntu-trusty-64:~/update-fg-urlfilter$ ansible-playbook update-urls.yml 
 [WARNING]: Unable to parse /etc/ansible/hosts as an inventory source

 [WARNING]: No inventory was parsed, only implicit localhost is available

 [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'


PLAY [localhost] ****************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************
ok: [localhost]

TASK [set url table] ************************************************************************************************************************
 [WARNING]: Module did not set no_log for password

changed: [localhost]

TASK [check current url table] **************************************************************************************************************
ok: [localhost]

TASK [debug] ********************************************************************************************************************************
ok: [localhost] => {
    "msg": "URL loaded/in-file: [3 / 3]"
}

PLAY RECAP **********************************************************************************************************************************
localhost                  : ok=4    changed=1    unreachable=0    failed=0   

vagrant@vagrant-ubuntu-trusty-64:~/update-fg-urlfilter$ 

```
 
Please fill in Ansible inventory file before you begin. The inventory file should contain the devices to be managed. 

Linux- and Python-enabed are natively supported via Ansible and device types will be learned via Ansible setup module. Other devices (like FortiOS, Exreme Networks) need ansible_os to be specified.
Please see sample inventory file below.

```
[managed_devices]
cumulus.local
extreme.local ansible_os=exos
fortigate.local ansible_os=fortios fortios_vdom="global"
```

### Before you begin: model

Please fill in management protocols params inside group_vars/managed_devices file. This data will be used to configure management protocols on network devices.

```
vrf: mgmt

snmp:
  contact: "admin@local"
  location: "datacenter"
  v3:
    - server_ip: 1.1.1.1
      user: monitor
      auth: 123456
      enc: 654321

ntp_servers:
  - 192.168.1.10
  - 192.168.1.20

syslog_servers:
  - 192.168.5.14


ssh:
  - user: monitor
    enc_pass: $6$jaSl0NPh$h9cD1D8OQbGRW99sOl0lMlLabVx5p8eCvZQAYdOqtGkSKlcr/93HRb.O2phIR9FSSI6EOJYLDXUGfVxWyXLEx/
    # 123456
    # mkpasswd --method=SHA-512 <pass>

```
