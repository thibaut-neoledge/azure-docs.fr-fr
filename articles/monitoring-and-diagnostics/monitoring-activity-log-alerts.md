---
title: "Créer des alertes de journal d’activité | Microsoft Docs"
description: "Recevez des notifications par SMS, webhook et e-mail lors de la survenue d’événements précis dans le journal d’activité."
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
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>Créer des alertes de journal d’activité

## <a name="overview"></a>Vue d'ensemble
Les alertes de journal d’activité sont des ressources Azure. Elles peuvent donc être gérées à l’aide du portail Azure ou d’Azure Resource Manager (ARM). Cet article vous montre comment utiliser le portail Azure pour configurer des alertes sur les événements de journal d’activité.

Vous pouvez recevoir des alertes sur les opérations exécutées sur les ressources de votre abonnement ou sur les événements relatifs à l’intégrité du service pouvant affecter l’intégrité de vos ressources. Une alerte de journal d’activité surveille des événements de journal d’activité pour l’abonnement particulier vers lequel elle est déployée.

Vous pouvez configurer l’alerte en fonction des éléments suivants :
* Catégorie d’événement (pour les [événements d’intégrité du service, cliquez ici](monitoring-activity-log-alerts-on-service-notifications.md))
- Groupe de ressources
- Ressource
- Type de ressource
- Nom d’opération
- Niveau des notifications (commentaire, information, avertissement, erreur, critique)
- Statut
- Événement lancé par

Vous pouvez également définir qui doit recevoir l’alerte :
* Sélectionner un groupe d’actions existant
- Créer un groupe d’actions (qui peut être utilisé ultérieurement pour les alertes)

Cliquez [ici](monitoring-action-groups.md) pour en savoir plus sur les groupes d’actions

Vous pouvez configurer des alertes de notification d’intégrité du service et obtenir des informations à ce sujet à l’aide des éléments suivants :
* [portail Azure](monitoring-activity-log-alerts.md)
- [Modèles Microsoft Azure Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Créer une alerte sur un événement de journal d’activité avec un nouveau groupe d’actions dans le portail Azure
1.    Dans le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Il ouvre d’abord la section **Journal d’activité** .

3.    Cliquez sur la section **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Sélectionnez la commande **Ajouter une alerte de journal d’activité** et renseignez les champs.

5.    Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**. Ces éléments apparaissent dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    L’**abonnement** en cours d’utilisation est automatiquement renseigné. Il s’agit de l’abonnement vers lequel la ressource d’alerte sera déployée et qu’elle va surveiller.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    Sélectionnez le **groupe de ressources** auquel cette alerte sera associée dans l’**abonnement**.

8.    Indiquez les valeurs **Catégorie d’événement**, **Groupe de ressources**, **Ressource**, **Type de ressource**, **Nom d’opération**, **Niveau**, **État** et **Événement lancé par** pour identifier les événements que cette alerte doit surveiller.

9.    Créez un **nouveau** groupe d’actions en lui attribuant un **Nom** et un **Nom court**. Celui-ci est indiqué dans les notifications envoyées lorsque cette alerte est activée.

10.    Définissez ensuite une liste de destinataires en indiquant les éléments suivants :

    a. **Nom :** nom, alias ou identificateur du destinataire.

    b. **Type d’action :** choisissez de contacter le destinataire par SMS, courrier ou webhook

    c. **Détails :** selon le type d’action choisi, indiquez un numéro de téléphone, une adresse de messagerie ou un URI de webhook.

11.    Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

Pour plus d’informations sur le schéma webhook pour les alertes de journal d’activité, [cliquez ici](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable, en tant que groupe d’actions existant, pour la définition des futures alertes.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Créer une alerte sur un événement de journal d’activité pour un groupe d’actions existant dans le portail Azure
1.    Dans le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Il ouvre d’abord la section **Journal d’activité** .

3.    Cliquez sur la section **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Sélectionnez la commande **Ajouter une alerte de journal d’activité** et renseignez les champs.

5.    Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**. Ces éléments apparaissent dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    L’**abonnement** en cours d’utilisation est automatiquement renseigné.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    Sélectionnez le **Groupe de ressources** pour cette alerte.

8.    Indiquez les valeurs **Catégorie d’événement**, **Groupe de ressources**, **Ressource**, **Type de ressource**, **Nom d’opération**, **Niveau**, **État** et **Événement lancé par** pour déterminer les événements auxquels cette alerte doit s’appliquer.

9.    Choisissez de **Notifier via** un **Groupe d’actions existant**. Sélectionnez le groupe d’actions.

10.    Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes

Une fois que vous avez créé une alerte, celle-ci apparaît dans la section Alertes du service Monitor. Sélectionnez l’alerte à gérer. Vous pouvez effectuer les opérations suivantes :
* La **Modifier**.
* La **Supprimer**.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre la réception de notifications pour cette alerte.

## <a name="next-steps"></a>Étapes suivantes :
Obtenir une [vue d’ensemble des alertes](monitoring-overview-alerts.md)  
Consulter le [schéma de webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md) En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)  
En savoir plus sur les [Notifications sur l’état du service](monitoring-service-notifications.md)

