# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/idempotence/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un evironnement de test composé de 4 machines et de tester les différentes configurations possibles sur Ansible

## Environnement 

Voici les différentes machines utilisées pour cet exercice

| Machine virtuelle | Adresse IP     |
|-------------------|----------------|
| controle          | 192.168.56.10  |
| rocky             | 192.168.56.20  |
| debian            | 192.168.56.30  |
| suse              | 192.168.56.40  |

## Methodologie

On commence par lancer les machines et à se connecter au Control Host

```
vagrant up
vagrant ssh control
```

On commence par installer les paquets tree, git et nmap pour cela on execute la commande: 

```
ansible all -m package -a "name=<nom du package> state=present"
```

Pour installer, une fois cela fait on peut reverifier pour voir si les packages ont bien été installé. 

```
ansible all -m package -a "name=tree state=present"
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=git state=present"
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "git"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=nmap state=present"
suse | SUCCESS => {
    "changed": false,
    "name": [
        "nmap"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}
```

On remarque que tous les "changed" sont à false cela veut dire que les paquets sont déjà installé et qu'aucune action est à faire. 

Pour désinstaller les paquets on réutilise la même commande en passant le "state" à "false". 

```
ansible all -m package -a "name=<nom du package> state=false"
```

De la même façon que pour l'installation, une fois que les commandes ont été lancées une premier fois on peut la rélancer pour voir si il n'y à pas eu d'erreur. 

```
ansible all -m package -a "name=git state=absent"
debian | SUCCESS => {
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "git"
    ],
    "rc": 0,
    "state": "absent",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=nmap state=absent"
debian | SUCCESS => {
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "nmap"
    ],
    "rc": 0,
    "state": "absent",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=tree state=absent"
debian | SUCCESS => {
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree"
    ],
    "rc": 0,
    "state": "absent",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}
```

Comme pour l'instalation, le changed est à false cela veut dire que les paquets ne sont plus installés sur la machine ils ont donc bien été supprimés. 

Pour copier le fichier /etc/fstab du Controle Host vers tous les Target Hosts on utilise la commande 

```
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
```

comme les commandes précédentes on les lance 2 fois, une première une première pour faire la copie une deuxième pour verifier si la copie à marchée 

```
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
debian | SUCCESS => {
    "changed": false,
    "checksum": "0fe1d6fcaf1695fb3ef9d8a42d45d04e5e0c11c2",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/tmp/test3.txt",
    "size": 679,
    "state": "file",
    "uid": 0
}
rocky | SUCCESS => {
    "changed": false,
    "checksum": "0fe1d6fcaf1695fb3ef9d8a42d45d04e5e0c11c2",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/tmp/test3.txt",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 679,
    "state": "file",
    "uid": 0
}
suse | SUCCESS => {
    "changed": false,
    "checksum": "0fe1d6fcaf1695fb3ef9d8a42d45d04e5e0c11c2",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/tmp/test3.txt",
    "size": 679,
    "state": "file",
    "uid": 0
}
```

Pour suprimer le fichier que l'on vient d'ajouter on lance la commande:

```
ansible all -m file -a "path=/tmp/test3.txt state=absent"
debian | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
rocky | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
suse | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
```

On lance ensuite la commande "df -h" sur les 3 machines pour voir l'espace disque utilisé, on remarque que les 3 ont un espace disque différent alors qu'ils ont exactement la même configuration. Cela est normal étant donnée que chaque machine à un OS différent. 

```
ansible all -m command -a "df -h /"
debian | CHANGED | rc=0 >>
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/debian--12--vg-root   62G  1.7G   57G   3% /
rocky | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        61G  2.0G   59G   4% /
suse | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        64G  2.4G   58G   4% /
```

(J'avoue ne pas comprendre l'intéret de cette question etant donnée que l'on utilise 3 machines différentes et que l'on ne fait pas de df -h avant pour avoir l'avant après pour montrer que l'idempotence de ansible)