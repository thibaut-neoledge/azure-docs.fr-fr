---
title: Ouvrir une session sur une machine virtuelle Azure Classic | Microsoft Docs
description: "Utilisez le portail Azure Classic pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f8fb116f14f771947f321f59d9880df4cdfe6859


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Ouvrir une session sur une machine virtuelle Windows à l’aide du portail Azure Classic
Sur le portail Azure Classic, vous utilisez le bouton **Connecter** pour démarrer une session Bureau à distance et ouvrir une session sur une machine virtuelle Windows.

Vous souhaitez vous connecter à une machine virtuelle Linux ? Consultez [Connexion à une machine virtuelle sous Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Découvrez comment [effectuer ces étapes à l’aide du nouveau Portail Azure](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur la connexion à une machine virtuelle avec le modèle Resource Manager, suivez [ce lien](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas
Voici une vidéo de procédure pas à pas de ce didacticiel. Elle couvre également les points de terminaison et les ports publics et privés utilisés pour la connexion à une machine virtuelle Windows dans Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.
1. Connectez-vous à la version classique du portail Azure.
2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle.
3. Sur la barre de commandes en bas de la page, cliquez sur **Connexion**.
   
    ![Connexion à la machine virtuelle](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Si le bouton **Connexion** n’est pas disponible, consultez les conseils de dépannage à la fin de cet article.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>Connexion à la machine virtuelle
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Étapes suivantes
* Si le bouton **Connexion** est inactif ou que vous rencontrez d’autres problèmes avec la connexion Bureau à distance, essayez de réinitialiser la configuration. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser la configuration à distance**.
* Si votre mot de passe pose problème, essayez de le réinitialiser. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser le mot de passe**.

Si ces conseils ne donnent aucun résultat ou ne vous sont pas utiles, consultez [Résolution des problèmes de connexion du Bureau à distance à une machine virtuelle Azure sous Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.




<!--HONumber=Dec16_HO1-->


