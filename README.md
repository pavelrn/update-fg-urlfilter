# update-fg-urlfilter

This is a simple playbook to update static URL filter (add, delete filter entries) inside Fortinet FortiGate UTM devices.
URL filter entries are taken from `urls.json` file that should be created beforehand.

### How to install

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

### How to configure


Open `update-urls.yml` and check connection parameters to match your environment:

```
hosts: localhost
  vars:
   host: "<set FG IP@>"
   username: "<ser admin username>"
   password: "<set admin password>"
   vdom: "<VDOM name to make changes to>"

```

Enter filtered URLs into `urls.json`.

### How to run

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

### FortiOS 6.0

Please note that this playbook is tested for FortiOS 5.6.4. Seems like FortiOS 6.0 has a better way of pushing URLs into FortiGate via external threat feeds that are auto-fethed by FortiGate from a web server. Please check release notes for details.
