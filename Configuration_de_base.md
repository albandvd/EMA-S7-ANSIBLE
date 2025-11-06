/etc/hosts

```
127.0.0.1 localhost
127.0.1.1 vagrant
192.168.56.10 controle
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
```

ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03

vagrant@control:~$ cat /etc/os-release 
PRETTY_NAME="Ubuntu 22.04.5 LTS"

sudo apt update
sudo apt install -y ansible
ansible --version
ansible 2.10.8

ansible all -i target01,target02,target03 -m ping

mkdir monprojet
cd monprojet/
touch ansible.cfg

ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg

vi ansible.cfg
```
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

ansible all -i target01,target02,target03 -m ping
cat ../journal/ansible.log 
2025-11-06 08:39:44,347 p=3440 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-11-06 08:39:44,357 p=3440 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-11-06 08:39:44,362 p=3440 u=vagrant n=ansible | target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

vi hosts
```
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
```

ansible all -m ping
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

ansible_become=yes

ansible all -a "head -n 1 /etc/shadow"
target01 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::

exit
logout
Connection to 127.0.0.1 closed.
[ema@testlab:atelier-06] $ vagrant destroy -f
