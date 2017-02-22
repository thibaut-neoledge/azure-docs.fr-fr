---
title: "Configurer les paramètres de réplication pour Azure Site Recovery | Microsoft Docs"
description: "Explique comment déployer Site Recovery pour orchestrer la réplication, le basculement et la récupération des machines virtuelles Hyper-V dans les clouds VMM dans Azure."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Gérer la stratégie de réplication pour VMware dans Azure


## <a name="create-a-new-replication-policy"></a>Créer une stratégie de réplication

1. Dans le menu de gauche, cliquez sur Gérer -> Infrastructure de récupération de site. 
2. Sélectionnez Stratégies de réplication dans la section Pour VMware et les machines physiques.
3. Dans la partie supérieure, cliquez sur +Stratégie de réplication.

    ![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Entrez le nom de la stratégie.

5. Dans le champ Seuil d’objectif de point de récupération, spécifiez la limite de l’objectif de point de récupération. Des alertes sont générées lorsque la réplication continue dépasse cette limite.
6. Dans Rétention des points de récupération, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle. 

    > [!NOTE] 
    > La rétention est prise en charge jusqu’à 24 heures pour les machines virtuelles répliquées dans le Stockage Premium, et 72 heures pour les machines virtuelles répliquées dans le Stockage Standard.
    
    > [!NOTE] 
    > Une stratégie de réplication pour la restauration automatique est automatiquement créée.

7. Dans Fréquence des captures instantanées de cohérence d’application, spécifiez la fréquence de création des points de récupération contenant des captures instantanées cohérentes avec les applications (en minutes).

8. Cliquez sur OK. La création devrait prendre 30 secondes à 1 minute.

![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Associer le serveur de configuration à la stratégie de réplication
1. Cliquez sur la stratégie de réplication à laquelle vous souhaitez associer le serveur de configuration.
2. Dans la partie supérieure, cliquez sur Associer.
![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Sélectionnez le serveur de configuration à partir de la liste des serveurs.
4. Cliquez sur OK. L’association du serveur de configuration devrait prendre 1 à 2 minutes.

![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Modifier la stratégie de réplication
1. Cliquez sur la stratégie de réplication pour laquelle vous souhaitez modifier les paramètres de réplication.
![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Dans la partie supérieure, cliquez sur Modifier les paramètres.
![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Modifiez les paramètres selon vos besoins.
4. Dans la partie supérieure, cliquez sur Enregistrer. L’enregistrement de la stratégie devrait prendre 2 à 5 minutes en fonction du nombre de machines virtuelles utilisant la stratégie de réplication.

![Créer une stratégie de réplication](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Dissocier le serveur de configuration de la stratégie de réplication
1. Cliquez sur la stratégie de réplication à laquelle vous souhaitez associer le serveur de configuration.
2. Dans la partie supérieure, cliquez sur Dissocier.
3. Sélectionnez le serveur de configuration à partir de la liste des serveurs.
4. Cliquez sur OK. La dissociation du serveur de configuration devrait prendre 1 à 2 minutes.
    
    > [!NOTE] 
    > Vous ne pouvez pas dissocier un serveur de configuration si un élément répliqué utilise cette stratégie. Vérifiez qu’aucun élément répliqué n’utilise la stratégie avant de dissocier le serveur de configuration.

## <a name="delete-replication-policy"></a>Supprimer la stratégie de réplication 

1. Cliquez sur la stratégie de réplication que vous souhaitez supprimer.
2. Cliquez sur Supprimer. La suppression devrait prendre 30 secondes à 1 minute.

    > [!NOTE] 
    > Vous ne pouvez pas supprimer une stratégie de réplication si 1 serveur de configuration lui est associé. Assurez-vous qu’aucun élément répliqué n’utilise la stratégie, et supprimez tous les serveurs de configuration avant de supprimer la stratégie.


<!--HONumber=Jan17_HO4-->


