# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/configuration/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un evironnement de test composé de 4 machines et de tester les différentes configurations possibles sur Ansible

## Environnement 

voici les différentes machines utilisé pour cet exercice

| Machine virtuelle | Adresse IP     |
|-------------------|----------------|
| controle          | 192.168.56.10  |
| target01          | 192.168.56.20  |
| target02          | 192.168.56.30  |
| target03          | 192.168.56.40  |

## Methodologie

On commence par lancer les machines et se connecter au Control Host  

```
vagrant up
vagrant ssh control
```
On commence par modifier le fichier /etc/hosts pour que les machines soient accesibles par leur nom d'hôte.

```
# /etc/hosts
127.0.0.1 localhost
192.168.56.10 controle
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
```

On configure ensuite l'authentification par clé ssh pour cela on commence par ajouter les machines distantes au fichier known_hosts puis on génére une clé sur la machine hote et on la copie sur les 3 machines cibles

```
ssh-keyscan -t target01 target02 target03 >> .ssh/known_hosts

ssh-keygen

ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

On installe ensuite Ansible sur le contrôle host grâce au gestionnaire de paquet. On la regarde dans le fichier /etc/os-relase le nom de la distribution pour pouvoir utiliser le gestionnaire adapté 

```
cat /etc/os-release 
PRETTY_NAME="Ubuntu 22.04.5 LTS"
```

Dans notre cas la machine est une ubuntu on peut donc utiliser le gestionnaire de paquet apt

```
sudo apt update
sudo apt install -y ansible
ansible --version
```

on essaye de lancer un ping vers les différentes machines avec la commande ansible

```
ansible all -i target01,target02,target03 -m ping
```

on créer ensuite un nouveau dossier avec un fichier ansible.cfg  on verifie ensuite si il est prit en compte. 

```
mkdir monprojet
cd monprojet/
touch ansible.cfg

ansible --version | head -n 2
```
La commande renvoi le résultat suivant
```
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```

on voit que le config file est bien le fichier que l'on vient de créer cela veut dire qu'il est bien pris en compte par Ansible

On rajoute dans le fichier ansible.cfg un inventaire et la journalisation. 

```
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

On créer le dossier journal pour quel les logs soient ajoutés automatiquement. 

```
mkdir ~/journal
```

On lance maintenant une commande Ansible pour verifier si la journalisation marche

```
ansible all -i target01,target02,target03 -m ping
cat ../journal/ansible.log 
```
on voit dans le fichier ansible.log le résultat de la commande ping, la journalisation est donc bien fonctionnelle

```
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
```
On modifi le fichier hosts pour créer un groupe testlab ou on rajoute toutes les machines

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
