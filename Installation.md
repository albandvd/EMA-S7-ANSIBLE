# Authentification

## Consignes 

Les consignes sont disponibles [ici](<https://formations.microlinux.fr/ansible/configuration/#a-vous-de-jouer>)

## Objectif

L'objectif de cet exercice est de mettre en place un evironnement de test composé de 4 machines et d'installer Ansible sur les machines de différentes manière afin d'avoir un apercu des différentes manières de l'installer

## Environnement 

Voici les différentes machines utilisées pour cet exercice

| Machine virtuelle | Adresse IP     |
|-------------------|----------------|
| controle          | 192.168.56.10  |
| target01          | 192.168.56.20  |
| target02          | 192.168.56.30  |
| target03          | 192.168.56.40  |

## Methodologie

### Challenge 1 

On lance la VM Ubuntu et on s'y connecte

```
vagrant up ubuntu 
vagrant ssh ubuntu
```

On va ici installer Ansible avec le gestionnaire de paquet, ici apt. 
On commence par rafraîchir les informations des paquets puis on chercher s'il y a bien un paquet officiel Ansible par defaut. 

```
sudo apt update
apt-cache search --names-only ansible

ansible - Configuration management, deployment, and task execution system
ansible-core - Configuration management, deployment, and task execution system
ansible-lint - lint tool for Ansible playbooks
```

On trouve bien les paquets Ansible on peut donc lancer l'installation: 

```
sudo apt install -y ansible
```

on peut verifier si l'installation à bien marché en lançant la commande Ansible

```
ansible --version
ansible [core 2.14.18]
  config file = None
```

On peut maintenant quitter la vm et la supprimer 

```
exit 
vagrant destroy -f ubuntu 
```

### Challenge 2 

On lance la VM Ubuntu et on s'y connecte

```
vagrant up ubuntu 
vagrant ssh ubuntu
```

Pour ce challenge on va installer ansible en passant par un dépot PPA (Personal Package Archive)

Comme dans le challenge précedent on commence par rafraîchir les informations des paquets, puis on ajoute le dépôt PPA pour Ansible 

```
sudo apt update
sudo apt-add-repository ppa:ansible/ansible
```

On cherche ensuite le paquet que l'on vient d'ajouter et on installe ce paquet. 

```
apt-cache search --names-only ansible
sudo apt install -y ansible
```

on peut verifier si l'installation à bien marché en lançant la commande Ansible

```
ansible --version
ansible [core 2.14.18]
  config file = None
```

On peut maintenant quitter la vm et la supprimer 

```
exit 
vagrant destroy -f ubuntu 
```

### Challenge 3

On lance la VM Rocky Linux  et on s'y connecte

```
vagrant up rocky 
vagrant ssh rocky
```

Pour ce dernier challenge on va installer Ansible en utilisant le gestionnaire de dépendance de Python "pip" ainsi que Virtualenv

Pour cela on commence par rafraîchir les informations des paquets puis on installe Python3 et pip sur la machine à l'aide du gestionnaire de paquet dnf

```
sudo dnf upgrade
sudo dnf install python3-pip
```

Une fois cela fait on peut créer le Venv qui va nous servir pour l'installation et l'activer. 

```
python3 -m venv ~/.venv/ansible
source ~/.venv/ansible/bin/activate
```

Une fois cela fait on met à jour pip et installe Ansible

```
pip install --upgrade pip
pip install ansible
```

On peut maintenant verifier si Ansible à bien été installé 

```
ansible --version
```

On peut maintenant quitter le Venv et suprimer la machine 

```
deactivate
exit 
vagrant destroy -f ubuntu
```
