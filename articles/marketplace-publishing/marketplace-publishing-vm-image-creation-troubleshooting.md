---
title: "Comment résoudre les problèmes courants lors de la création du disque dur virtuel | Microsoft Docs"
description: "Réponses aux questions courantes de dépannage et aux problèmes rencontrés lors de la création du disque dur virtuel."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3acef7399d5e8757ebad3d6178b22eb2c5c68660


---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Comment résoudre les problèmes courants rencontrés lors de la création du disque dur virtuel
Cet article aide les éditeurs et/ou coadministrateurs Azure Marketplace qui peuvent rencontrer un problème ou avoir des questions courantes lors de la publication ou de la gestion de leurs solutions de machine virtuelle.

1. Comment modifier le nom de l’hôte ?
   
    Une fois que la machine virtuelle est créée, il est impossible pour les utilisateurs de mettre à jour le nom de l’hôte.
2. Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion ?
   
   * [Référence pour les machines virtuelles Windows](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Référence pour les machines virtuelles Linux](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Comment générer de nouveaux certificats ssh ?
   
   Cliquez sur le lien : [https://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Comment configurer un certificat VPN ouvert ?
   
   Cliquez sur le lien : [https://azure.microsoft.com/fr-fr/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-point-to-site-create/)
5. Quelle est la stratégie de prise en charge pour l’exécution de logiciels serveur Microsoft dans un environnement de machine virtuelle Microsoft Azure (infrastructure en tant que service) ?
   
   Cliquez sur le lien : [https://support.microsoft.com/fr-fr/kb/2721672](https://support.microsoft.com/en-us/kb/2721672)
6. Les machines virtuelles ont-elles un identificateur unique ?
   
   Azure encode l’ID unique de machine virtuelle Azure dans chaque machine virtuelle. Pour plus d’informations, consultez le blog et la documentation.
7. Dans une machine virtuelle, comment puis-je gérer l’extension de script personnalisé lors de la tâche de démarrage ?
   
   Cliquez sur le lien : [https://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Comment créer une machine virtuelle à partir du portail Azure à l’aide du disque dur virtuel qui est téléchargé vers le stockage Premium ?
   
   Nous ne prenons pas encore en charge cette fonctionnalité.
9. Les applications 32 bits sont-elles prises en charge dans Azure Marketplace ?
   
   Cliquez sur le lien suivant pour plus d’informations sur la stratégie de prise en charge : [https://support.microsoft.com/fr-fr/kb/2721672](https://support.microsoft.com/en-us/kb/2721672)
10. Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur « .VHD is already registered with image repository as the resource » (Le disque dur virtuel est déjà enregistré avec le référentiel d’image en tant que ressource) dans PowerShell. Je n’ai pas créé d’image au préalable et je n’ai trouvé aucune image portant ce nom dans Azure. Comment résoudre ce problème ?
    
    Cela se produit généralement si l’utilisateur a provisionné une machine virtuelle à partir de ce disque dur virtuel et si le disque dur virtuel est verrouillé. Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel. Si l’erreur persiste, ouvrez un ticket de support à l’aide de ce lien ou du portail de publication (toutes les informations sont fournies dans la réponse à la question 11).
11. Comment ouvrir un ticket de support concernant mon problème ?
    
    Procédez comme suit.
    
    * Vérifiez que vous êtes bien un partenaire Azure Certified avant d’ouvrir un ticket de support. Dans le cas contraire, inscrivez-vous au programme Certified Azure en cliquant sur ce [lien](https://azure.microsoft.com/en-us/documentation/articles/marketplace-publishing-azure-certification/).
    * Si vous êtes un partenaire Azure Certified approuvé, connectez-vous au [portail de publication](https://publish.windowsazure.com/) à l’aide d’un compte Microsoft. Si vous n’avez pas de compte Microsoft, vous pouvez en créer un en cliquant sur ce [lien](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
    * Cliquez sur le lien Obtenir de l’aide dans le menu sous le nom d’utilisateur (voir la capture d’écran ci-dessous).
      
      ![dessin](media/marketplace-publishing-vm-image-creation-troubleshooting/img11_01.png)
    * Sélectionnez le type de problème « VM image Certification » (Certification d’image de machine virtuelle) et la catégorie « Preparing VHDs for the Azure VM Marketplace » (Préparation des disques durs virtuels pour le Marketplace de machines virtuelles Azure) et cliquez sur la bouton « Début de la requête ».
    * Fournissez les informations requises et envoyez le ticket. L’équipe du support technique vous aidera à résoudre ce problème.




<!--HONumber=Nov16_HO3-->


