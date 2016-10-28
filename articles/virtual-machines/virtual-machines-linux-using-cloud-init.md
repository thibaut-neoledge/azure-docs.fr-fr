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
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création

Cet article montre comment créer un script cloud-init pour définir le nom d'hôte, mettre à jour les packages installés et gérer les comptes d'utilisateur. Les scripts cloud-init sont appelés lors de la création de la machine virtuelle à partir de l’interface de commande Azure.

## Composants requis

Les conditions préalables sont : [un compte Azure](https://azure.microsoft.com/pricing/free-trial/), [des clés publiques et privées SSH](virtual-machines-linux-mac-create-ssh-keys.md) et [l’interface de ligne de commande Azure](../xplat-cli-install.md) en mode Azure Resource Manager avec `azure config mode arm`.

## Commandes rapides

Créez un script cloud-init.txt qui définit le nom d’hôte, met à jour tous les packages et ajoute un utilisateur sudo à Linux.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Créez une machine virtuelle Linux à configurer au cours de démarrage à l’aide de cloud-init.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## Introduction

Lorsque vous lancez une nouvelle machine virtuelle Linux, vous obtenez une machine virtuelle Linux standard, non personnalisée ni adaptée à vos besoins. [Cloud-init](https://cloudinit.readthedocs.org) est un moyen classique d'injecter les paramètres d'un script ou d’une configuration dans cette machine virtuelle Linux lorsqu’elle démarre pour la première fois.

Dans Azure, il existe trois façons différentes d’apporter des modifications à une machine virtuelle Linux pendant son déploiement ou son démarrage.

- Injectez des scripts à l’aide de cloud-init.
- Injectez des scripts à l’aide de [l’extension Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md).
- Un modèle Azure utilisant cloud-init.
- Un modèle Azure utilisant [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Pour injecter des scripts à tout moment après le démarrage :

- SSH pour exécuter directement des commandes.
- Injectez des scripts à l’aide de [l’extension Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md) de manière impérative ou dans un modèle Azure.
- Des outils de gestion de la configuration tels qu’Ansible, Salt, Chef et Puppet.

>[AZURE.NOTE]l’extension VMAccess exécute un script comme racine de la même manière à l’aide de SSH. Cependant, l’utilisation de l’extension de machine virtuelle active plusieurs fonctionnalités qu’Azure offre qui peuvent être utiles selon votre scénario.

### Disponibilité de cloud-init lors de la création d’alias d’images de machine virtuelle Azure :

| Alias | Éditeur | Offer | SKU | Version | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | Centos | 7,2 | le plus récent | no |
| CoreOS | CoreOS | CoreOS | Stable | le plus récent | yes |
| Debian | credativ | Debian | 8 | le plus récent | no |
| openSUSE | SUSE | openSUSE | 13\.2 | le plus récent | no |
| RHEL | Redhat | RHEL | 7,2 | le plus récent | no |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | le plus récent | yes |

Microsoft collabore avec ses partenaires pour que cloud-init soit inclus et fonctionne dans les images qu’ils fournissent à Azure.


## Procédure pas à pas

### Ajout d'un script cloud-init à la création d’une machine virtuelle avec l’interface CLI Azure

Pour lancer un script cloud-init lors de la création d'une machine virtuelle dans Azure, spécifiez le fichier cloud-init à l'aide du commutateur d’interface de ligne de commande Azure `--custom-data`.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

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
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

Connectez-vous et vérifiez le nom d'hôte de la nouvelle machine virtuelle.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Création d'un script cloud-init pour mettre à jour Linux

Pour des questions de sécurité, configurez votre machine virtuelle Ubuntu de manière à ce qu’elle se mette à jour au premier démarrage. À l'aide de cloud-init, nous pouvons le faire avec le script suivant, en fonction de la distribution Linux que vous utilisez.

#### Exemple de script cloud-init `cloud_config_apt_upgrade.txt` pour la famille Debian

```bash
#cloud-config
apt_upgrade: true
```

Une fois Linux démarré, tous les packages installés sont mis à jour par le biais d’`apt-get`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

Connectez-vous et vérifiez que tous les packages sont mis à jour.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Création d'un script cloud-init pour ajouter un utilisateur à Linux

L’une des premières tâches liées à n'importe quelle nouvelle machine virtuelle Linux consiste à ajouter un utilisateur pour vous-même ou à éviter d'utiliser `root`. Les clés SSH sont la meilleure pratique en matière de sécurité et de facilité d’utilisation. Elles sont ajoutées au fichier `~/.ssh/authorized_keys` avec ce script cloud-init.

#### Exemple de script cloud-init `cloud_config_add_users.txt` pour la famille Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Une fois Linux démarré, tous les utilisateurs répertoriés sont créés et ajoutés au groupe sudo.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

Connectez-vous et vérifiez l’utilisateur qui vient d’être créé.

```bash
cat /etc/group
```

Sortie

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## Étapes suivantes

Cloud-init est devenu une méthode standard pour modifier votre machine virtuelle Linux au démarrage. Azure propose également des extensions de machine virtuelle, ce qui vous permet de modifier votre machine virtuelle Linux au démarrage ou pendant son exécution. Par exemple, vous pouvez utiliser la VMAccessExtension Azure pour réinitialiser les informations de SSH ou de l’utilisateur pendant l’exécution de la machine virtuelle. Avec cloud-init, vous devez effectuer un redémarrage pour réinitialiser le mot de passe.

[À propos des extensions et des fonctionnalités des machines virtuelles](virtual-machines-linux-extensions-features.md)

[Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->