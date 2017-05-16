---
title: "Recevoir des alertes de journal d’activité sur les notifications de service | Microsoft Docs"
description: "Soyez informé par e-mail, SMS ou webhook en cas de service Azure."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 57e136c1027cfa7c789803409cef354f3d52d222
ms.lasthandoff: 03/31/2017


---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Créer des alertes de journal d’activité sur les notifications de service
## <a name="overview"></a>Vue d'ensemble
Cet article vous montre comment configurer des alertes de journal d’activité pour les notifications sur l’état du service avec le Portail Azure.  

Vous pouvez recevoir une alerte en fonction des notifications sur l’état du service de votre abonnement Azure.  
Vous pouvez configurer l’alerte en fonction des éléments suivants :
- la classe de notification sur l’état du service (incident, maintenance, information, etc.) ;
- l’état de la notification (actif ou résolu) ;
- le niveau des notifications (information, avertissement, erreur).

Vous pouvez également définir qui doit recevoir l’alerte :
- sélectionner un groupe d’actions existant ;
- créer un groupe d’actions (qui peut être utilisé ultérieurement pour les alertes).

Cliquez [ici](monitoring-action-groups.md) pour en savoir plus sur les groupes d’actions.

Vous pouvez configurer des alertes de notification sur l’état du service et obtenir des informations à ce sujet à l’aide des éléments suivants :
- [Portail Azure](monitoring-activity-log-alerts-on-service-notifications.md)
- [Modèles Microsoft Azure Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Créer une alerte sur une notification sur l’état du service pour un nouveau groupe d’actions avec le Portail Azure
1.    Sur le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.    Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Il ouvre d’abord la section **Journal d’activité** .

3.    Cliquez maintenant sur la section **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.    Sélectionnez la commande **Ajouter une alerte de journal d’activité** et renseignez les champs.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.    Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**. Ces éléments apparaissent dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.    **L’abonnement** en cours d’utilisation est automatiquement renseigné.

7.    Choisissez le **Groupe de ressources** de cette alerte.

8.    Sous **Catégorie d’événement**, sélectionnez l’option « État du service ». Choisissez pour quel **Type, État** et **Niveau** de notifications sur l’état du service vous souhaitez être notifié.

9.    Créez un **nouveau** groupe d’actions en lui attribuant un **Nom** et un **Nom court**. Celui-ci est indiqué dans les notifications envoyées lorsque cette alerte est déclenchée.

10.    Définissez ensuite une liste de destinataires en indiquant les éléments suivants :

    a. **Nom :** nom, alias ou identificateur du destinataire.

    b. **Type d’action :** choisissez de contacter le destinataire par SMS, e-mail ou webhook.

    c. **Détails :** selon le type d’action choisi, indiquez un numéro de téléphone, une adresse e-mail ou un URI de webhook.

11.    Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

Pour plus d’informations sur le schéma webhook pour les alertes de journal d’activité, [cliquez ici](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable, en tant que groupe d’actions existant, pour la définition des futures alertes.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-with-the-azure-portal"></a>Créer une alerte sur une notification sur l’état du service pour un groupe d’actions existant avec le Portail Azure
1.    Sur le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.    Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Il ouvre d’abord la section **Journal d’activité** .

3.    Cliquez maintenant sur la section **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.    Sélectionnez la commande **Ajouter une alerte de journal d’activité** et renseignez les champs.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.    Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**. Ces éléments apparaissent dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.    **L’abonnement** en cours d’utilisation est automatiquement renseigné.

7.    Choisissez le **Groupe de ressources** de cette alerte.

8.    Sous **Catégorie d’événement**, sélectionnez l’option « État du service ». Choisissez pour quel **Type, État** et **Niveau** de notifications sur l’état du service vous souhaitez être notifié.

9.    Choisissez de **Notifier via** un **Groupe d’actions existant**. Sélectionnez le groupe d’actions.

10.    Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes

Une fois créée, l’alerte apparaît dans la section Alertes du service Monitor. Sélectionnez l’alerte à gérer. Vous pouvez effectuer les opérations suivantes :
* La **Modifier**.
* La **Supprimer**.
* La **Désactiver** ou **l’Activer** si vous voulez arrêter temporairement ou reprendre la réception de notifications pour cette alerte.

## <a name="next-steps"></a>Étapes suivantes :
En savoir plus sur les [Notifications sur l’état du service](monitoring-service-notifications.md)  
Consulter le [schéma webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md) Obtenir une [vue d’ensemble des alertes de journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes  
En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)

