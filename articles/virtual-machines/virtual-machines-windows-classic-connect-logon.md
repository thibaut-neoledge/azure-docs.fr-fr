---
title: Ouvrir une session sur une machine virtuelle Azure Classic | Microsoft Docs
description: Utilisez le portail Azure Classic pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement classique.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Ouvrir une session sur une machine virtuelle Windows à l’aide du portail Azure Classic
Sur le portail Azure Classic, vous utilisez le bouton **Connecter** pour démarrer une session Bureau à distance et ouvrir une session sur une machine virtuelle Windows.

Vous souhaitez vous connecter à une machine virtuelle Linux ? Consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-mac-create-ssh-keys.md)

Découvrez comment [effectuer ces étapes à l’aide du nouveau portail Azure](virtual-machines-windows-connect-logon.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Vidéo de procédure pas à pas
Voici une vidéo de procédure pas à pas de ce didacticiel. Elle couvre également les points de terminaison et les ports publics et privés utilisés pour la connexion à une machine virtuelle Windows dans Azure.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## Connectez-vous à la machine virtuelle.
1. Connectez-vous à la version classique du portail Azure.
2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle.
3. Sur la barre de commandes en bas de la page, cliquez sur **Connexion**.
   
    ![Connexion à la machine virtuelle](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Si le bouton **Connexion** n’est pas disponible, consultez les conseils de dépannage à la fin de cet article.
> 
> 

## Connexion à la machine virtuelle
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Étapes suivantes
* Si le bouton **Connexion** est inactif ou que vous rencontrez d’autres problèmes avec la connexion Bureau à distance, essayez de réinitialiser la configuration. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser la configuration à distance**.
* Si votre mot de passe pose problème, essayez de le réinitialiser. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser le mot de passe**.

Si ces conseils ne donnent aucun résultat ou ne vous sont pas utiles, consultez la rubrique [Résolution des problèmes de connexion du Bureau à distance à une machine virtuelle Azure sous Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.

<!---HONumber=AcomDC_0824_2016-->