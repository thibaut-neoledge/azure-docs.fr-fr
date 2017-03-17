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
ms.date: 12/07/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 0d7eba02757fb1b2263cf11c561b374eab837f21
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-linux-vms"></a>Configuration de pilotes GPU pour les machines virtuelles Linux série N
Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure exécutant une distribution Linux prise en charge, vous devez installer des pilotes graphiques NVIDIA sur chaque machine virtuelle après le déploiement. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur les spécifications, les capacités de stockage et les disques des machines virtuelles série N, consultez l’article [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



## <a name="supported-gpu-drivers"></a>Pilotes GPU pris en charge


> [!NOTE]
> Actuellement, la prise en charge de GPU Linux est uniquement disponible sur les machines virtuelles NC Azure exécutant Ubuntu Server 16.04 LTS.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Pilotes Tesla NVIDIA pour les machines virtuelles NC

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (programme d’installation .run à extraction automatique)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Installation du pilote Tesla sur Ubuntu 16.04 LTS

1. Établissez une connexion SSH à la machine virtuelle série N Azure.

2. Pour vérifier que le système dispose d’un GPU compatible CUDA, exécutez la commande suivante :

    ```bash
    lspci | grep -i NVIDIA
    ```
    Vous verrez une sortie similaire à l’exemple suivant (illustrant une carte K80 Tesla NVIDIA) :

    ![Sortie de commande Ispci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. Téléchargez le fichier .run pour le pilote de votre distribution. L’exemple de commande suivant télécharge le pilote Tesla Ubuntu 16.04 LTS dans le répertoire /tmp :

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. Si vous devez installer `gcc` et `make` sur votre système (requis pour les pilotes Tesla), tapez la commande suivante :

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. Accédez au répertoire contenant le programme d’installation du pilote et exécutez des commandes similaires à ce qui suit :

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote


Pour interroger l’état de l’appareil GPU, exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

![État de l’appareil NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Installation facultative du kit d’outils CUDA NVIDIA sur Ubuntu 16.04 LTS

Vous pouvez éventuellement installer le kit d’outils CUDA NVIDIA 8.0 sur les machines virtuelles CN exécutant Ubuntu 16.04 LTS. Outre les pilotes GPU, le kit d’outils fournit un environnement de développement complet pour les développeurs C et C++ qui créent des applications avec accélération GPU.

Exécutez des commandes similaires à ce qui suit pour installer le kit d’outils CUDA :

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

L’installation peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les GPU NVIDIA sur les machines virtuelles série N, consultez :
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (pour les machines virtuelles NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (pour les machines virtuelles NV Azure)


