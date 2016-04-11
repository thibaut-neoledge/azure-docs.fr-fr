<properties
		pageTitle="Ajouter un utilisateur sur une machine virtuelle Linux dans Azure | Microsoft Azure"
		description="Ajouter un utilisateur sur une machine virtuelle Linux dans Azure."
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="03/04/2016"
		ms.author="v-livech"
/>

# Ajouter un utilisateur à une machine virtuelle Azure

Pour n’importe quelle machine virtuelle tout juste créée, l’une des premières tâches consiste à créer un nouvel utilisateur. Dans cet article nous allons vous aider à créer un compte d’utilisateur sudo, à définir le mot de passe, à ajouter des clés publiques SSH et enfin à utiliser `visudo` pour autoriser sudo sans mot de passe.

Les conditions préalables sont : [un compte Azure](https://azure.microsoft.com/pricing/free-trial/), [les clés publiques et privées SSH](virtual-machines-linux-mac-create-ssh-keys.md), un groupe de ressources Azure et l’interface CLI Azure, et passer au mode Azure Resource Manager avec `azure config mode arm`.

## Commandes rapides

```bash
# Add a new user on RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser

# Set a password
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

## Procédure pas à pas

### Introduction

Une des premières tâches à effectuer avec un nouveau serveur consiste à ajouter un compte utilisateur. Les connexions racine doivent toujours être désactivées et même le compte racine lui-même ne doit jamais être utilisé avec votre serveur Linux, mais en sudo uniquement. La création d’un utilisateur, puis l’affectation à ce nouvel utilisateur de privilèges racine à l’aide de sudo est la meilleure méthode pour l’administration et l’utilisation de Linux.

La commande que nous allons utiliser est `useradd`, qui modifie `/etc/passwd`, `/etc/shadow`, `/etc/group` et `/etc/gshadow` pour créer le nouvel utilisateur Linux. Nous allons ajouter un indicateur de ligne de commande pour la commande `useradd` pour également ajouter le nouvel utilisateur au groupe sudo approprié sur Linux. Bien que `useradd` crée une entrée dans `/etc/passwd`, cela ne donne pas de mot de passe au nouveau compte utilisateur. Nous allons créer un mot de passe initial pour le nouvel utilisateur à l’aide de la commande `passwd`. La dernière étape sera de modifier les règles sudo pour permettre à notre utilisateur d’exécuter des commandes avec privilèges sudo sans avoir à entrer de mot de passe pour chaque commande. Étant donné que l’utilisateur s’est connecté à l’aide de la paire de clés publique et privée, nous supposons que ce compte d’utilisateur est à l’abri des attaques peut autoriser un accès sudo sans mot de passe.

### Ajout d’un utilisateur sudo unique à une machine virtuelle Azure

Connectez-vous à la machine virtuelle Azure à l’aide des clés SSH. Si vous ne disposez pas de clés publiques d’accès SSH, reprenez d’abord cet article [Utilisation de l’authentification avec clé publique avec Azure](http://link.to/article).

La commande `useradd` effectue les tâches suivantes :

- créer un compte d’utilisateur
- créer un groupe d’utilisateurs avec le même nom
- ajouter une entrée vierge à `/etc/passwd`
- ajouter une entrée vierge à `/etc/gpasswd`

L’indicateur de ligne de commande `-G` ajoutera le nouveau compte utilisateur au groupe Linux approprié, en donnant au nouveau compte utilisateur des privilèges racine.

#### Ajouter l’utilisateur

```bash
# On RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# On Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser
```

#### Définir le mot de passe

La commande `useradd` crée le nouvel utilisateur et ajoute une entrée à `/etc/passwd` et à `/etc/gpasswd`, mais ne définit ne pas de mot de passe. Nous allons le faire avec la commande `passwd`.

```bash
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Nous disposons désormais d’un utilisateur avec privilèges sudo sur le serveur.

### Ajouter votre clé publique SSH au nouveau compte utilisateur

À partir de votre ordinateur, utilisez la commande `ssh-copy-id` avec le mot de passe que vous venez de définir.

```bash
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### Utilisation de visudo pour autoriser l’utilisation de sudo sans mot de passe

L’utilisation de `visudo` pour modifier le fichier `/etc/sudoers` ajoute plusieurs couches de protection contre toute modification incorrecte de ce fichier crucial. Lors de l’exécution de `visudo`, le fichier `/etc/sudoers` est verrouillé afin que personne d’autre ne puisse apporter des modifications pendant qu’il est modifié. Le fichier `/etc/sudoers` est également analysé par `visudo` pour rechercher les erreurs lorsque vous essayez de l’enregistrer ou de quitter. Cela garantit que vous ne pouvez pas laisser de fichier sudoers mal formé sur le système.

Nous disposons déjà des utilisateurs dans le bon groupe par défaut pour l’accès sudo. Nous allons maintenant activer ces groupes pour qu’ils utilisent sudo sans mot de passe.

```bash
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### Vérifier l’utilisateur, les clés ssh et sudo

```bash
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

<!---HONumber=AcomDC_0330_2016-->