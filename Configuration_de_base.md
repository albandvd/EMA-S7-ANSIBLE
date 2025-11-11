# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/configuration/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un evironnement de test composé de 4 machines et de tester les différentes configurations possibles sur Ansible

## Environnement 

Voici les différentes machines utilisées pour cet exercice

| Machine virtuelle | Adresse IP     |
|-------------------|----------------|
| controle          | 192.168.56.10  |
| target01          | 192.168.56.20  |
| target02          | 192.168.56.30  |
| target03          | 192.168.56.40  |

## Methodologie

On commence par lancer les machines et à se connecter au Control Host

```
vagrant up
vagrant ssh control
```
On commence par modifier le fichier /etc/hosts pour que les machines soient accessibles par leur nom d’hôte.

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

On installe ensuite Ansible sur le contrôle host grâce au gestionnaire de paquet. On regarde dans le fichier /etc/os-relase le nom de la distribution pour pouvoir utiliser le gestionnaire adapté 

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

on essaie de lancer un ping vers les différentes machines avec la commande ansible

```
ansible all -i target01,target02,target03 -m ping
```

on crée ensuite un nouveau dossier avec un fichier ansible.cfg et on vérifie s'il est pris en compte. 

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

On voit que le fichier de configuration est bien celui que l’on vient de créer, ce qui signifie qu’il est bien pris en compte par Ansible

On rajoute dans le fichier ansible.cfg un inventaire et la journalisation. 

```
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

On crée le dossier journal pour que les logs soient ajoutés automatiquement.

```
mkdir ~/journal
```

On lance maintenant une commande Ansible pour verifier si la journalisation marche

```
ansible all -i target01,target02,target03 -m ping
cat ../journal/ansible.log 
```
On voit dans le fichier ansible.log le résultat de la commande ping, la journalisation est donc bien fonctionnelle

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
On modifie le fichier hosts pour créer un groupe testlab où on rajoute toutes les machines. On rajoute aussi l'instruction permettant de spécifier l'utilisation de l'utilisateur vagrant pour se connecter aux cibles

```
[testlab]
target01
target02
target03
```

On peut lancer un ping pour vérifier si le fichier host est bien pris en compte.

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
```


Les pings sont bien passés, les hôtes configurés sont donc bien pris en compte

On ajoute maintenant l’instruction permettant de spécifier l’utilisation de l’utilisateur vagrant ainsi que le passage en root pour se connecter aux machines cibles.

```
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
ansible_become=yes
```

On lance une commande nous permettant d'avoir la premier ligne du fichier /etc/shadow qui permet de connaitre l'utilisateur actuel, pour vérifier si les modifications ont bien été pris en compte. 

```
ansible all -a "head -n 1 /etc/shadow"
target01 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
```

L’utilisateur est bien connecté en tant que root.