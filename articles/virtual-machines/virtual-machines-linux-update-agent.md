---
title: "Mettre à jour l’agent Linux Azure à partir de GitHub | Microsoft Docs"
description: "Découvrez comment mettre à jour l’agent Linux Azure pour votre machine virtuelle Linux dans Azure vers la dernière version de GitHub"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d35e70c1d50bd4cb2d47188585354aa3e3d740d4
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Mise à jour de l’agent Linux Azure sur une machine virtuelle vers la dernière version de Github
Pour mettre à jour votre [agent Linux Azure](https://github.com/Azure/WALinuxAgent) sur une machine virtuelle Linux dans Azure vous devez déjà disposer des éléments suivants :

1. Une machine virtuelle Linux en cours d'exécution dans Azure.
2. Une connexion à cette machine virtuelle Linux à l'aide de SSH.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Si vous exécutez cette tâche à partir d’un ordinateur Windows, vous pouvez utiliser PuTTY pour vous connecter au moyen du protocole SSH à votre machine Linux. Pour plus d’informations, consultez [Connexion à une machine virtuelle sous Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Les versions Linux approuvées dans Azure ont placé le package Azure Linux Agent dans leurs référentiels, c’est pourquoi il convient de vérifier et de commencer par installer la dernière version à partir de ce référentiel de version si possible.  

Pour Ubuntu, il suffit de taper :

```bash
sudo apt-get install walinuxagent
```

Et sur CentOS, tapez :

```bash
sudo yum install waagent
```

Pour Oracle Linux, assurez-vous que le référentiel `Addons` est activé. Modifiez le fichier `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), modifiez la ligne `enabled=0` en `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.

Puis, pour installer la dernière version de l'agent Linux Azure, tapez :

```bash
sudo yum install WALinuxAgent
```

Si vous ne trouvez pas le référentiel de compléments, vous pouvez ajouter ces lignes à la fin de votre fichier .repo en fonction de votre version d’Oracle Linux :

Pour les machines virtuelles Oracle Linux 6 :

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Pour les machines virtuelles Oracle Linux 7 :

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Tapez ensuite :

```bash
sudo yum update WALinuxAgent
```

Généralement, c’est tout ce qu’il vous est demandé de faire. Toutefois, si pour une raison quelconque vous devez l’installer directement à partir de https://github.com, procédez comme suit.

## <a name="install-wget"></a>Installer wget
Connectez-vous à votre machine virtuelle à l’aide du protocole SSH.

Installez wget (certaines versions ne l’installent pas par défaut, telles que Redhat, CentOS, et Oracle Linux versions 6.4 et 6.5) en tapant `#sudo yum install wget` sur la ligne de commande.

## <a name="download-the-latest-version"></a>Téléchargez la dernière version
Ouvrez [la version de l’agent Linux Azure dans Github](https://github.com/Azure/WALinuxAgent/releases) dans une page web et cherchez le dernier numéro de version. (Vous pouvez rechercher votre version actuelle en tapant `#waagent --version`.)

### <a name="for-version-20x-type"></a>Pour la version 2.0.x, tapez :

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

La ligne suivante utilise la version 2.0.14 comme exemple :

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>Pour la version 2.1.x (ou version ultérieure), tapez :
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

La ligne suivante utilise la version 2.1.0 comme exemple :

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Installez l'agent Linux Azure.
### <a name="for-version-20x-use"></a>Pour la version 2.0.x, utilisez :
Rendre waagent exécutable :

```bash
chmod +x waagent
```

Copiez le nouveau fichier exécutable dans /usr/sbin /.

Pour la plupart des versions Linux, utilisez :

```bash
sudo cp waagent /usr/sbin
```

Pour CoreOS, procédez comme suit :

```bash
sudo cp waagent /usr/share/oem/bin/
```

S'il s'agit d'une nouvelle installation de l'agent Linux Azure, exécutez :

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>Pour la version 2.1.x, tapez :
Vous devrez peut-être installer d’abord le package `setuptools`. Consultez [ces informations](https://pypi.python.org/pypi/setuptools). Exécutez ensuite la commande suivante :

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>Redémarrer le service waagent
Pour la plupart des versions de linux :

```bash
sudo service waagent restart
```

Pour Ubuntu, procédez comme suit :

```bash
sudo service walinuxagent restart
```

Pour CoreOS, procédez comme suit :

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmer la version de l'agent Linux Azure
    
```bash
waagent -version
```

Pour CoreOS, la commande ci-dessus peut ne pas fonctionner.

Vous verrez que la version de l'agent Linux Azure a été mise à jour vers la nouvelle version.

Pour plus d’informations sur l’agent Linux Azure, consultez le fichier [Lisezmoi de l’agent Linux Azure](https://github.com/Azure/WALinuxAgent).


