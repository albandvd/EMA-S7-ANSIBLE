# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/authentification/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un evironnement de test composé de 4 machines et de gérer leur configuration réseau pour permettre un ping en utilisant les noms d'hôte des machines. 

## Environnement 

voici les différentes machines utilisé pour cet exercice

| Machine virtuelle | Adresse IP     |
|-------------------|----------------|
| ansible           | 192.168.56.10  |
| rocky             | 192.168.56.20  |
| debian            | 192.168.56.30  |
| suse              | 192.168.56.40  |

## Methodologie

On commence par lancer les machines et se connecter au Control Host 

```
vagrant up
vagrant ssh control
```

On verifie si Ansible est bien installé sur cette machine 

```
type ansible
ansible is /usr/bin/ansible
```

Ici, Ansible est bien installé on peut commencer la configuration. 

On commence par modifier le fichier /etc/hosts pour que les machines soient accesibles par leur nom d'hôte.

```
# /etc/hosts
127.0.0.1      localhost
192.168.56.10  controle
192.168.56.20  target01
192.168.56.30  target02
192.168.56.40  target03
```

Pour verifier si la configuration à marché on peut tester de pinger les machines par leur hostname

```

```


Les ping sont passé on peut donc passer 
ssh-keyscan -t target01 target02 target03 >> .ssh/known_hosts
ssh-keygen

ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03

ansible all -i target01,target02,target03 -m ping

target01 | SUCCESS => {
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
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
