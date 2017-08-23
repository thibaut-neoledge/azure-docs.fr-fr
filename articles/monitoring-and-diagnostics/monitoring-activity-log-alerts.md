---
title: "Créer des alertes de journal d’activité | Microsoft Docs"
description: "Recevez des notifications par SMS, webhook et e-mail lors de la survenue d’événements précis dans le journal d’activité."
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
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>Créer des alertes de journal d’activité

## <a name="overview"></a>Vue d'ensemble
Les alertes de journal d’activité sont des alertes qui s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte. Comme il s’agit de ressources Azure, elles peuvent être créées à l’aide d’un modèle Resource Manager. En outre, elles peuvent être créées, mises à jour ou supprimées dans le portail Azure. Cet article présente les concepts associés aux alertes du journal d’activité et vous montre comment utiliser le portail Azure pour configurer une alerte à partir d’événements du journal d’activité.

Les alertes de journal d’activité servent généralement deux objectifs :
1. Recevoir une notification lorsque les ressources de votre abonnement Azure sont modifiées (cela se limite souvent à certains groupes de ressources ou à certaines ressources). Par exemple, vous pouvez être averti lorsqu’une machine virtuelle faisant partie de myProductionResourceGroup est supprimée. Vous pouvez également être averti si de nouveaux rôles sont attribués à un utilisateur de votre abonnement.
2. Recevoir une notification lorsqu’un événement d’état du service se produit. Les événements d’état du service incluent la notification des incidents et des événements de maintenance qui s’appliquent aux ressources de votre abonnement.

Dans les deux cas, l’alerte du journal d’activité surveille uniquement les événements de l’abonnement dans lequel elle a été créée.

Vous pouvez configurer une alerte de journal d’activité en fonction de n’importe quelle propriété de niveau supérieur de l’objet JSON d’un événement de journal d’activité. Toutefois, le portail affiche uniquement les options les plus courantes :
- **Catégorie** : les différentes catégories sont Administratif, État du service, Mise à l’échelle automatique et Recommandation. [Consultez cet article pour plus d’informations sur les catégories du journal d’activité](./monitoring-overview-activity-logs.md#categories-in-the-activity-log) et [consultez cet article pour en savoir plus sur les événements d’état du service](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Groupe de ressources**
- **Ressource**
- **Type de ressource**
- **Nom de l’opération** : nom de l’opération RBAC Resource Manager.
- **Niveau** : niveau de gravité de l’événement (détails, information, avertissement, erreur, critique).
- **État** : état de l’événement, généralement « Démarré », « Échec » ou « Réussite »
- **Événement lancé par** : également appelé l’« appelant ». Adresse e-mail ou identificateur d’Active Directory de l’utilisateur qui a effectué l’opération.

>[!NOTE]
>Vous devez spécifier au moins deux des critères précédents dans votre alerte. Le critère de catégorie est obligatoire. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.
>
>

Lorsqu’une alerte du journal d’activité devient active, elle utilise un groupe d’actions pour générer des actions ou des notifications. Un groupe d’actions est un ensemble réutilisable de destinataires de notifications (adresses e-mail, URL Webhook ou numéros de téléphone (SMS)) qui peut être référencé par plusieurs alertes pour centraliser et regrouper vos canaux de notification. Vous pouvez utiliser un groupe d’actions existant dans votre alerte de journal d’activité, ou créer un nouveau groupe d’actions lors de la définition de votre alerte de journal d’activité. Cliquez [ici](monitoring-action-groups.md) pour en savoir plus sur les groupes d’actions

Pour en savoir plus sur les alertes de journal d’activité liées aux notifications d’état du service, [cliquez ici](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Créer une alerte pour un événement du journal d’activité avec un nouveau groupe d’actions dans le portail Azure
1.  Dans le [portail](https://portal.azure.com), accédez au panneau **Surveillance**.

    ![Surveiller](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Le panneau **Journal d’activité** est ouvert en premier.

3.  Cliquez maintenant sur le panneau **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Sélectionnez la commande **Ajouter une alerte de journal d’activité**, puis renseignez les champs.

5.  Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  **L’abonnement** sélectionné est celui dans lequel l’alerte du journal d’activité est enregistrée. L’abonnement en cours d’utilisation est automatiquement renseigné. Il s’agit de l’abonnement dans lequel la ressource d’alerte est déployée et dans lequel les événements du journal d’activité sont surveillés.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  Choisissez le **Groupe de ressources** dans lequel créer cette ressource d’alerte. Le groupe de ressources ne va pas être surveillé *par* l’alerte. En effet, c’est la *ressource d’alerte qui va être déployée dans ce groupe de ressources*.

8.  Si vous le souhaitez, vous pouvez sélectionner une **Catégorie d’événement**, ce qui va modifier le groupe de filtres supplémentaires indiqué. Pour les événements administratifs, cela comprend les options permettant de filtrer par **Groupe de ressources**, **Ressource**, **Type de ressource**, **Nom d’opération**, **Niveau**, **État** et **Événement lancé par** pour identifier les événements que cette alerte doit surveiller.

>[!NOTE]
>Vous devez spécifier au moins l’un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.
>
>

9.  Créez un **nouveau** groupe d’actions en lui attribuant un **Nom** et un **Nom court**. Celui-ci est indiqué dans les notifications envoyées lorsque cette alerte est activée.

10. Définissez ensuite une liste d’actions en indiquant les éléments suivants :

    a. **Nom** : nom, alias ou identificateur de l’action.

    b. **Type d’action :** choisissez le type d’action : Webhook, e-mail ou SMS

    c. **Détails** : selon le type d’action choisi, indiquez un numéro de téléphone, une adresse e-mail ou une URL Webhook.

11. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Quelques minutes sont nécessaires à la propagation complète de l’alerte, à son activation et à son déclenchement lorsque de nouveaux événements répondent aux critères de l’alerte.

[Consultez ce document pour en savoir plus sur le schéma Webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable, en tant que groupe d’actions existant, pour la définition de futures alertes.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Créer une alerte concernant un événement du journal d’activité pour un groupe d’actions existant dans le portail Azure
1.  Dans le [portail](https://portal.azure.com), accédez au panneau **Surveillance**.

    ![Surveiller](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Il ouvre d’abord la section **Journal d’activité** .

3.  Cliquez maintenant sur la section **Alertes**.

    ![Alertes](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Sélectionnez la commande **Ajouter une alerte de journal d’activité**, puis renseignez les champs.

5.  Attribuez un **Nom** à votre alerte de journal d’activité et choisissez une **Description**. Ces éléments apparaissent dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  **L’abonnement** sélectionné est celui dans lequel l’alerte du journal d’activité est enregistrée. L’abonnement en cours d’utilisation est automatiquement renseigné. Il s’agit de l’abonnement dans lequel la ressource d’alerte est déployée et dans lequel les événements du journal d’activité sont surveillés.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  Choisissez le **Groupe de ressources** dans lequel créer cette ressource d’alerte. Le groupe de ressources ne va pas être surveillé *par* l’alerte. En effet, c’est la *ressource d’alerte qui va être déployée dans ce groupe de ressources*.

8.  Si vous le souhaitez, vous pouvez sélectionner une **Catégorie d’événement**, ce qui va modifier le groupe de filtres supplémentaires indiqué. Pour les événements administratifs, cela comprend les options permettant de filtrer par **Groupe de ressources**, **Ressource**, **Type de ressource**, **Nom d’opération**, **Niveau**, **État** et **Événement lancé par** pour identifier les événements que cette alerte doit surveiller.

9.  Choisissez de **Notifier via** un **Groupe d’actions existant**. Sélectionnez un groupe d’actions existant dans la liste.

10. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.

Quelques minutes sont nécessaires à la propagation complète de l’alerte, à son activation et à son déclenchement lorsque de nouveaux événements répondent aux critères de l’alerte.

## <a name="managing-your-alerts"></a>Gestion de vos alertes

Une fois créée, l’alerte apparaît dans la section Alertes du panneau Surveillance. Sélectionnez l’alerte à gérer. Vous pouvez alors effectuer les opérations suivantes :
* La **Modifier**.
* La **Supprimer**.
* La **Désactiver** ou **l’Activer** si vous voulez arrêter temporairement ou reprendre la réception de notifications pour cette alerte.

## <a name="next-steps"></a>Étapes suivantes
- Obtenir une [vue d’ensemble des alertes](monitoring-overview-alerts.md)
- En savoir plus sur la [limitation du débit de notification](monitoring-alerts-rate-limiting.md)
- Consulter le [schéma de webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md)
- En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)  
- En savoir plus sur les [Notifications sur l’état du service](monitoring-service-notifications.md)
- [Créer une alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Créer une alerte de journal d’activité pour surveiller tous les échecs d’opérations de mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

