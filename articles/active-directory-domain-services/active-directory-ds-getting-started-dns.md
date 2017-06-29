---
title: "Azure Active Directory Domain Services : mettre à jour les paramètres DNS pour le réseau virtuel Azure | Microsoft Docs"
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: dacd689576dce65bbf1a975409ea7d7f2c3ada90
ms.contentlocale: fr-fr
ms.lasthandoff: 06/14/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Mettre à jour les paramètres DNS pour le réseau virtuel Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : mettre à jour les paramètres DNS pour le réseau virtuel Azure
Dans les tâches de configuration précédentes, vous avez activé Azure Active Directory Domain Services pour votre répertoire. La tâche suivante consiste à s’assurer que les ordinateurs du réseau virtuel peuvent se connecter et utiliser ces services. Dans cet article, vous mettez à jour les paramètres de serveur DNS de votre réseau virtuel afin qu’il pointe vers les deux adresses IP pour lesquelles Azure Active Directory Domain Services est disponible sur le réseau virtuel.

> [!NOTE]
> Après avoir activé Azure Active Directory Domain Services pour le répertoire, notez les adresses IP d’Azure Active Directory Domain Services affichées dans l’onglet **Configurer** de votre répertoire.
>
>

Pour mettre à jour le paramètre de serveur DNS pour le réseau virtuel sur lequel vous avez activé Azure Active Directory Domain Services, procédez comme suit :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans le volet gauche, sélectionnez **Réseaux**.  
    La fenêtre **Réseaux** s’ouvre.

    ![Fenêtre Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. Dans l’onglet **Réseaux virtuels**, sélectionnez le réseau virtuel sur lequel vous avez activé Azure Active Directory Domain Services afin d’en afficher les propriétés.
4. Cliquez sur l'onglet **Configurer** .

    ![Fenêtre Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Dans la section **Serveurs DNS**, entrez les deux adresses IP qui étaient affichées dans la section **Services de domaine** de l’onglet **Configurer** de votre répertoire.
6. Pour enregistrer les paramètres de serveur DNS pour ce réseau virtuel, cliquez sur **Enregistrer** dans le volet des tâches au bas de la fenêtre.

   ![Mettre à jour les paramètres de serveur DNS pour le réseau virtuel](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Les machines virtuelles du réseau n’obtiendront les nouveaux paramètres DNS que lors d’un redémarrage. Si vous avez besoin qu’elles obtiennent les paramètres DNS mis à jour immédiatement, déclenchez un redémarrage via le portail, PowerShell ou l’interface CLI.
>
>

## <a name="next-steps"></a>Étapes suivantes
Tâche 5 : [activer la synchronisation du mot de passe pour Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

