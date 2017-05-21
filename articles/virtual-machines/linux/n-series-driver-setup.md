---
title: "Configuration du pilote série N Azure pour Linux | Microsoft Docs"
description: "Procédure de configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Linux dans Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Linux

Pour tirer parti des fonctionnalités GPU de machines virtuelles série N Azure exécutant Linux, installez des pilotes graphiques NVIDIA sur chaque machine virtuelle. Cet article vous offre des étapes de configuration de pilote lorsque vous avez déployé une machine virtuelle de série N. Des informations de configuration du pilote sont également disponibles pour [les machines virtuelles Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Pour plus d’informations sur les spécifications, les capacités de stockage et les disques des machines virtuelles série N, consultez l’article [Tailles de machine virtuelle Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="supported-distributions-and-drivers"></a>Distributions et pilotes pris en charge

> [!IMPORTANT]
> Actuellement, la prise en charge du pilote de GPU Linux est uniquement disponible sur les machines virtuelles NC Azure. 

Les distributions suivantes dans la Place de marché Azure sont prises en charge pour exécuter des pilotes graphiques NVIDIA sur des machines virtuelles Linux de série N.

### <a name="nc-vms-tesla-k80-card"></a>Machines virtuelles NC (carte Tesla K80)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* Basé sur CentOS 7.3 

**Pilotes pris en charge** : NVIDIA CUDA 8.0, branche pilote R375. [Procédure d’installation](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> L’installation de logiciels tiers sur des produits Red Hat peut affecter les conditions de prise en charge de Red Hat. Consultez l’[article de la Base de connaissances Red Hat](https://access.redhat.com/articles/1067).
>




## <a name="install-cuda-drivers-for-nc-vms"></a>Installer des pilotes CUDA pour des machines virtuelles NC

Voici les étapes pour installer les pilotes NVIDIA sur des machines virtuelles Linux NC à partir du kit d’outils CUDA NVIDIA 8.0. 

Les développeurs C et C++ peuvent éventuellement installer le kit d’outils complet pour créer des applications avec accélération GPU. Pour plus d’informations, consultez le [Guide d’installation de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Les liens de téléchargement de pilotes CUDA fournis ici sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](http://www.nvidia.com/).
>

Pour installer le kit d’outils CUDA, établissez une connexion SSH à chaque machine virtuelle. Pour vérifier que le système dispose d’un GPU compatible CUDA, exécutez la commande suivante :

```bash
lspci | grep -i NVIDIA
```
Vous verrez une sortie similaire à l’exemple suivant (illustrant une carte K80 Tesla NVIDIA) :

![Sortie de commande Ispci](./media/n-series-driver-setup/lspci.png)

Ensuite, exécutez les commandes spécifiques à votre distribution.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
L’installation peut prendre plusieurs minutes.

Pour éventuellement installer le kit d’outils CUDA complet, saisissez :

```bash
sudo apt-get install cuda
```

Redémarrez la machine virtuelle et vérifiez l’installation.

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 ou Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> En raison d’un problème connu, l’installation du pilote NVIDIA CUDA échoue sur les machines virtuelles NC24r exécutant CentOS 7.3 ou Red Hat Enterprise Linux 7.3.
>

Tout d’abord, obtenez des mises à jour. 

```bash
sudo yum update

sudo reboot
```

Reconnectez-vous à la machine virtuelle et continuez l’installation avec les commandes suivantes :

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

L’installation peut prendre plusieurs minutes. Pour éventuellement installer le kit d’outils CUDA complet, saisissez :

```bash
sudo yum install cuda
```

Redémarrez la machine virtuelle et vérifiez l’installation.


### <a name="verify-driver-installation"></a>Vérification de l’installation du pilote


Pour interroger l’état de l’appareil GPU, connectez-vous par SSH à la machine virtuelle et exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

Une sortie similaire à ce qui suit s’affiche :

![État de l’appareil NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Mises à jour de pilote CUDA

Nous vous recommandons de régulièrement mettre à jour les pilotes CUDA après le déploiement.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Une fois la mise à jour terminée, redémarrez la machine virtuelle.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 ou Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

Une fois la mise à jour terminée, redémarrez la machine virtuelle.



## <a name="troubleshooting"></a>Résolution des problèmes

* Il existe un problème connu avec les pilotes CUDA sur les machines virtuelles Azure de série N exécutant le noyau Linux 4.4.0-75 sur Ubuntu 16.04 LTS. Pour assurer le fonctionnement du pilote lorsque vous mettez à niveau le noyau, effectuez une mise à niveau au minimum vers la version de noyau 4.4.0-77. 



## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les GPU NVIDIA sur les machines virtuelles série N, consultez :
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (pour les machines virtuelles NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (pour les machines virtuelles NV Azure)

* Pour capturer une image Linux VM avec vos pilotes NVIDIA installés, consultez [Généraliser et capturer une machine virtuelle Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
