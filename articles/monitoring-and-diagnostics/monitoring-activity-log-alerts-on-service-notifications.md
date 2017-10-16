---
title: "Recevoir des alertes de journal d’activité sur les notifications de service | Documents Microsoft"
description: "Soyez informé par SMS, e-mail ou webhook en cas de service Azure."
author: johnkemnetz
manager: orenr
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
ms.author: johnkem
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Créer des alertes de journal d’activité sur les notifications de service
## <a name="overview"></a>Vue d'ensemble
Cet article vous indique comment configurer des alertes de journal d’activité pour les notifications sur l’intégrité du service à l’aide du Portail Azure.  

Vous pouvez recevoir une alerte lorsqu’Azure envoie des notifications sur l’état du service sur votre abonnement Azure. Vous pouvez configurer l’alerte en fonction des éléments suivants :

- la classe de notification sur l’intégrité du service (incident, maintenance, informations, etc.) ;
- le ou les services affectés ;
- la ou les régions affectées ;
- l’état de la notification (actif ou résolu) ;
- le niveau des notifications (informationnelle, avertissement, erreur).

Vous pouvez également configurer à qui l’alerte doit être envoyée :

- sélectionner un groupe d’actions existant ;
- créer un nouveau groupe d’actions (qui peut être utilisé pour les alertes futures).

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](monitoring-action-groups.md).

Pour plus d’informations sur le mode de configuration des alertes de notification sur l’intégrité du service à l’aide de modèles Azure Resource Manager, consultez [Modèles Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Créer une alerte sur une notification sur l’intégrité du service pour un nouveau groupe d’actions à l’aide du portail Azure
1. Dans le [Portail](https://portal.azure.com), sélectionnez **Moniteur**.

    ![Le service « Moniteur »](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. Dans la section **Journal d’activité**, sélectionnez **Alertes**.

    ![L’onglet « Alertes »](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. Sélectionnez **Ajouter une alerte de journal d’activité**, puis renseignez les champs.

    ![La commande « Ajouter une alerte activité journal »](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. Entrez un nom dans la boîte **Nom de l’alerte activité journal** et fournissez une **Description**.

    ![La boîte de dialogue « Ajouter une alerte activité journal »](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. La boîte **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est utilisé pour enregistrer l’alerte de journal d’activité. La ressource d’alerte est déployée pour cet abonnement et surveille les événements du journal d’activité.

6. Sélectionnez le **Groupe de ressources** dans lequel la ressource d’alerte est créée. Il ne s’agit pas du groupe de ressources surveillé par l’alerte. Au lieu de cela, il s’agit du groupe de ressources où se trouve la ressource d’alerte.

7. Dans la boîte **Catégorie d’événement**, sélectionnez **Intégrité du service**. En option, sélectionnez **Service**, **Région**, **Type**, **État** et **Niveau** des notifications sur l’intégrité du service que vous souhaitez recevoir.

8. Sous **Alerte via**, sélectionnez le bouton du groupe d’actions **Nouveau**. Entrez un nom dans la boîte **Nom du groupe d’actions** et entrez un nom dans la boîte **Nom court**. Le nom court est référencé dans les notifications envoyées lorsque cette alerte se déclenche.

9. Définissez ensuite une liste de destinataires en indiquant les éléments suivants :

    a. **Nom** : entrez le nom, l’alias ou l’identificateur du destinataire.

    b. **Type d’action** : sélectionnez SMS, e-mail ou webhook.

    c. **Détails** : selon le type d’action choisi, indiquez un numéro de téléphone, une adresse e-mail ou une URI Webhook.

10. Sélectionnez **OK** pour créer l’alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

Pour plus d’informations sur le schéma de webhook sur les alertes de journal d’activité, consultez [Webhooks pour les alertes du journal d’activité Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable en tant que groupe d’actions existant, pour la définition de toutes les futures alertes.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Créer une alerte sur une notification sur l’intégrité du service pour un groupe d’actions existant à l’aide du portail Azure

1. Suivez les étapes 1 à 7 dans la section précédente pour créer votre notification d’intégrité du service. 

2. Sous **Alerte via**, sélectionnez le bouton du groupe d’actions **Existant**. Sélectionnez le groupe d’actions approprié.

3. Sélectionnez **OK** pour créer l’alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

## <a name="manage-your-alerts"></a>Gérez vos alertes

Une fois l’alerte créée, elle apparaît dans la section **Alertes** du panneau **Moniteur**. Sélectionnez l’alerte que vous souhaitez gérer pour :

* la modifier
* la supprimer
* la désactiver ou l’activer si vous voulez arrêter temporairement ou reprendre la réception de notifications pour l’alerte.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [notifications sur l’intégrité du service](monitoring-service-notifications.md).
- En savoir plus sur la [limitation du débit des notifications](monitoring-alerts-rate-limiting.md).
- Consultez le [schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).
- Obtenez une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrez comment recevoir des alertes. 
- En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).
