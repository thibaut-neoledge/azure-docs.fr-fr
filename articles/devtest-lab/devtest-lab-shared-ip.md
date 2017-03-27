---
title: "Comprendre les adresses IP partagées dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment Azure DevTest Labs utilise les adresses IP partagées afin de limiter les adresses IP publiques requises pour accéder aux machines virtuelles de votre laboratoire."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b4ae6b16880e4a5e53c8f94dc814e25254c20d73
ms.lasthandoff: 03/10/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendre les adresses IP partagées dans Azure DevTest Labs

Azure DevTest Labs utilise les adresses IP partagées afin de limiter le nombre d’adresses IP publiques requises pour accéder aux machines virtuelles de votre laboratoire individuel.  Cet article décrit le fonctionnement des adresses IP partagées et les options de configuration qui leur sont associées.

## <a name="shared-ip-setting"></a>Paramètre d’adresse IP partagée

Lorsque vous créez un laboratoire, il se trouve dans un sous-réseau de réseau virtuel.  Par défaut, ce sous-réseau est créé avec le paramètre **Enable shared public IP** (Activer l’adresse IP publique partagée) défini sur *Oui*.  Cette configuration crée une adresse IP publique pour le sous-réseau dans son ensemble.  Vous pouvez désactiver cette option après la création d’un laboratoire en [suivant les étapes décrites dans cet article](devtest-lab-configure-vnet.md).

![Nouveau sous-réseau de laboratoire](media/devtest-lab-shared-ip/lab-subnet.png)

Les machines virtuelles créées dans ce laboratoire utilisent par défaut une adresse IP partagée.  Lorsque vous créez la machine virtuelle, ce paramètre peut être observé dans le panneau **Paramètres avancés** sous **Configuration de l’adresse IP**.

![Nouvelle machine virtuelle](media/devtest-lab-shared-ip/new-vm.png)

Chaque fois qu’une machine virtuelle pour laquelle une adresse IP partagée est activée est ajoutée au sous-réseau, un port TCP est affecté à l’adresse IP publique de transfert vers le port RDP sur la machine virtuelle.  

## <a name="using-the-shared-ip"></a>Utilisation de l’adresse IP partagée

Vous vous connectez à un bureau à distance sur la machine virtuelle dans un client RDP à l’aide de l’adresse IP ou du nom de domaine complet, suivis du signe deux-points et du port.  Par exemple, dans l’image ci-dessous, l’adresse RDP pour se connecter à la machine virtuelle est `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.  Vous pouvez également sélectionner dans le portail Azure le bouton **Se connecter** pour télécharger un fichier RDP préconfiguré.

![Exemple de machine virtuelle](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Étapes suivantes

* [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configuration d’un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md)






