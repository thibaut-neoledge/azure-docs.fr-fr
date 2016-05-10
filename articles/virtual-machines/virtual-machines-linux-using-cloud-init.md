<properties
    pageTitle="Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création | Microsoft Azure"
    description="Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création."
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
    ms.date="04/22/2016"
    ms.author="v-livech"
/>

# Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création

Pour cet article, nous allons créer des scripts cloud-init pour définir le nom d'hôte, mettre à jour les packages installés et gérer les comptes d'utilisateur. Nous allons ensuite lancer ces scripts cloud-init lors de la création de la machine virtuelle Linux à l'aide de [l’interface de ligne de commande Azure](../xplat-cli-install.md).

## Composants requis

Les conditions préalables sont : [un compte Azure](https://azure.microsoft.com/pricing/free-trial/), les [clés publiques et privées SSH](virtual-machines-linux-mac-create-ssh-keys.md), un groupe de ressources Azure pour lancer les machines virtuelles Linux, l’interface CLI Azure installée et au mode ARM avec `azure config mode arm`.

## Introduction

Lorsque vous lancez une nouvelle machine virtuelle Linux, vous obtenez une machine virtuelle Linux standard, non personnalisée ni adaptée à vos besoins. [Cloud-init](https://cloudinit.readthedocs.org) est un moyen classique d'injecter les paramètres d'un script ou d’une configuration dans cette machine virtuelle Linux lorsqu’elle démarre pour la première fois.

Dans Azure, il existe trois façons différentes d’apporter des modifications à une machine virtuelle Linux qui démarre.

- Vous pouvez injecter des scripts avec cloud-init.
- Vous pouvez ajouter des scripts à l'aide de l’instruction Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) .
- Vous pouvez spécifier des paramètres personnalisés dans un modèle Azure et utiliser ce modèle pour lancer et personnaliser votre machine virtuelle, qui inclut la prise en charge de cloud-init, l'extension CustomScript VM et bien d'autres éléments.

Pour injecter des scripts à tout moment, vous pouvez :

- utiliser SSH pour exécuter des commandes directement, utiliser Azure l’instruction [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) de façon impérative ou dans un modèle Azure, ou utiliser les outils courants de gestion de la configuration comme Ansible, Salt, Chef et Puppet, qui fonctionnent via SSH après le démarrage complet de la machine virtuelle.

Remarque : si une instruction [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) exécute simplement un script comme racine de la même manière que SSH, l’extension VM active plusieurs fonctionnalités Azure qui peuvent être utiles en fonction de votre scénario.

## Commandes rapides

```bash
# Create a hostname cloud-init script
#cloud-config
hostname: exampleServerName

# Create an update Linux on first boot cloud-init script for Debian Family
#cloud-config
apt_upgrade: true

# Create an add a user cloud-init script
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop

```

## Procédure pas à pas

### Ajout d'un script cloud-init à la création d’une machine virtuelle avec l’interface CLI Azure

Pour lancer un script cloud-init lors de la création d'une machine virtuelle dans Azure, spécifiez le fichier cloud-init à l'aide du commutateur CLI Azure `--custom-data`.

Remarque : bien que cet article décrit l'utilisation du commutateur `--custom-data` pour les fichiers cloud-init, vous pouvez également passer un code arbitraire ou des fichiers à l'aide de ce commutateur. Si la machine virtuelle Linux sait déjà comment traiter ces fichiers, ces derniers s'exécutent automatiquement.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### Création d'un script cloud-init pour définir le nom d'hôte d'une machine virtuelle Linux

Le nom d’hôte est l’un des paramètres les plus simples et les plus importants pour une machine virtuelle Linux. Nous pouvons facilement définir ce paramètre en utilisant cloud-init avec ce script.

#### Exemple de script cloud-init nommé `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Lors du démarrage initial de la machine virtuelle, ce script cloud-init définit le nom d'hôte sur `exampleServerName`.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

Connectez-vous et vérifiez le nom d'hôte de la nouvelle machine virtuelle.

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ hostname
bill@ubuntu$ exampleServerName
```

### Création d'un script cloud-init pour mettre à jour Linux

Pour des questions de sécurité, configurez votre machine virtuelle Ubuntu pour qu’elle se mette à jour au premier démarrage. À l'aide de cloud-init, nous pouvons le faire avec le script suivant, en fonction de la distribution Linux que vous utilisez.

#### Exemple de script cloud-init `cloud_config_apt_upgrade.txt` pour la famille Debian

```bash
#cloud-config
apt_upgrade: true
```

Une fois que la nouvelle machine virtuelle Linux a démarré, elle met immédiatement à jour tous les packages installés via `apt-get`.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_apt_upgrade.txt
```

Connectez-vous et vérifiez que tous les packages sont mis à jour.

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Création d'un script cloud-init pour ajouter un utilisateur à Linux

Une des premières tâches sur n'importe quelle nouvelle machine virtuelle Linux consiste à ajouter un utilisateur pour vous-même ou pour éviter d'utiliser `root`. Cette procédure est idéale pour garantir la sécurité et améliorer la facilité d'utilisation lorsque vous ajoutez votre clé publique SSH au fichier `~/.ssh/authorized_keys` de cet utilisateur dans le cadre de connexions sans mot de passe et sécurisées.

#### Exemple de script cloud-init `cloud_config_add_users.txt` pour la famille Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop
```

Une fois que la nouvelle machine virtuelle Linux a démarré, elle va créer l’utilisateur et l’ajouter au groupe sudo.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_add_users.txt
```

Connectez-vous et vérifiez l’utilisateur qui vient d’être créé.

```bash
bill@slackware$ cat /etc/group
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

<!---HONumber=AcomDC_0427_2016-->