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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1701646ce8cb9494561c37d46cd435b4e7608fe8
ms.lasthandoff: 03/14/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Linux

Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure exécutant une distribution Linux prise en charge, vous devez installer des pilotes graphiques NVIDIA sur chaque machine virtuelle après le déploiement. Des informations de configuration du pilote sont également disponibles pour [les machines virtuelles Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> Actuellement, la prise en charge de GPU Linux est uniquement disponible sur les machines virtuelles NC Azure exécutant Ubuntu Server 16.04 LTS.
> 

Pour plus d’informations sur les spécifications, les capacités de stockage et les disques des machines virtuelles série N, consultez l’article [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Consultez aussi [Considérations générales pour les machines virtuelles de série N](#general-considerations-for-n-series-vms).



## <a name="install-nvidia-cuda-drivers"></a>Installation des pilotes CUDA NVIDIA

Voici les étapes pour installer les pilotes NVIDIA sur des machines virtuelles Linux NC à partir du kit d’outils CUDA NVIDIA 8.0. Les développeurs C et C++ peuvent éventuellement installer le kit d’outils complet pour créer des applications avec accélération GPU. Pour plus d’informations, consultez le [Guide d’installation de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Les liens de téléchargement de pilotes fournis ici sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](http://www.nvidia.com/).

Pour installer le kit d’outils CUDA, établissez une connexion SSH à chaque machine virtuelle. Pour vérifier que le système dispose d’un GPU compatible CUDA, exécutez la commande suivante :

```bash
lspci | grep -i NVIDIA
```
Vous verrez une sortie similaire à l’exemple suivant (illustrant une carte K80 Tesla NVIDIA) :

![Sortie de commande Ispci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

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

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote


Pour interroger l’état de l’appareil GPU, connectez-vous par SSH à la machine virtuelle et exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

![État de l’appareil NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>Mises à jour de pilote CUDA

Nous vous recommandons de régulièrement mettre à jour les pilotes CUDA après le déploiement.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Une fois la mise à jour terminée, redémarrez la machine virtuelle.


[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

* Nous ne recommandons pas l’installation d’un serveur spécifique ou d’autres systèmes qui utilisent le pilote nouveau sur les machines virtuelles Ubuntu NC. Avant d’installer les pilotes GPU NVIDIA, vous devez désactiver le pilote nouveau.  

* Si vous souhaitez capturer l’image d’une machine virtuelle Linux sur laquelle vous avez installé des pilotes NVIDIA, consultez [Généraliser et capturer une machine virtuelle Linux](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les GPU NVIDIA sur les machines virtuelles série N, consultez :
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (pour les machines virtuelles NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (pour les machines virtuelles NV Azure)


