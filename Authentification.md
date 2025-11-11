# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/authentification/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un environnement de test composé de 4 machines et de gérer leur configuration réseau pour permettre un ping en utilisant les noms d’hôte des machines. 

## Environnement 

Voici les différentes machines utilisées pour cet exercice:

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

On vérifie si Ansible est bien installé sur cette machine

```
type ansible
ansible is /usr/bin/ansible
```

Ici, Ansible est bien installé, on peut commencer la configuration.

On commence par modifier le fichier /etc/hosts pour que les machines soient accesibles par leur nom d'hôte.

```
# /etc/hosts
127.0.0.1      localhost
192.168.56.10  controle
192.168.56.20  target01
192.168.56.30  target02
192.168.56.40  target03
```

Pour vérifier si la configuration a marché, on peut tester de pinguer les machines par leur nom d’hôte.

```
for HOST in target01 target02 target03; do ping -c 1 -q $HOST; done
```

Ici les pings fonctionnent mais la commande ping de Ansible ne fonctionne pas encore. 

En effet, la commande ping (Linux) utilise le protocole ICMP, elle teste donc simplement la connectivité réseau avec une machine, tandis que la commande ping (Ansible) utilise le protocole SSH pour se connecter à la machine. Il faut donc mettre en place l’authentification par clé SSH pour que cela fonctionne.

On commence donc par récupérer les clés des différentes machines cibles et on génère une clé SSH sur la machine hôte grâce aux commandes: 

```
ssh-keyscan -t target01 target02 target03 >> .ssh/known_hosts
ssh-keygen
```

On peut ensuite lancer la copie des clés publics vers les machines distantes: 

```
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

Une fois l’authentification SSH mise en place, on peut lancer la commande ping d’Ansible :

```
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
```