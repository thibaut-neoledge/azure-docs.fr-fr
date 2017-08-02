---
title: "Créer et gérer des groupes d’action dans le portail Azure | Documents Microsoft"
description: 
author: anirudhcavale
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
ms.date: 05/15/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 2b2fa2126b9c3f8598ec8fe686846920a4b7c422
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Créer et gérer des groupes d’actions dans le portail Azure
## <a name="overview"></a>Vue d'ensemble ##
Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Les groupes d’actions vous permettent de configurer une liste d’actions. Ces groupes peuvent être utilisés lors de la définition d’alertes de journal d’activité, dans l’optique de s’assurer qu’un groupe d’actions particulier est invoqué au déclenchement de l’alerte de journal d’activité.

Un groupe d’actions peut contenir jusqu’à 10 actions de chaque type. Une action est définie par une combinaison des éléments suivants :

**Nom :** identificateur unique dans le groupe d’actions.  
**Type d’action :** définit l’action à effectuer. Les options sont Envoyer un SMS, Envoyer un e-mail ou Appeler un webhook.  
**Détails :** selon le type d’action, le numéro de téléphone, l’adresse e-mail ou l’URI du webhook correspondant doit être fourni.

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager concernant la configuration des groupes d’actions : [Action Group Resource Manager templates](monitoring-create-action-group-with-resource-manager-template.md) (Modèles Resource Manager de groupes d’actions)

## <a name="creating-an-action-group-using-the-azure-portal"></a>Création d’un groupe d’actions sur le portail Azure ##
1.  Dans le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-action-groups/home-monitor.png)
2.  Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Ce panneau rassemble tous vos paramètres de surveillance et données dans une vue consolidée. Il ouvre d’abord la section **Journal d’activité** .

3.  Cliquez sur la section **Groupes d’actions**.

    ![Action-Group](./media/monitoring-action-groups/action-groups-blade.png)
4.  Cliquez sur la commande de groupe d’actions **Add** et remplissez les champs.

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.  Indiquez un **Nom** et un **Nom court** pour le groupe d’actions. Le nom court sera indiqué dans les notifications envoyées à ce groupe.

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.  L’**Abonnement** est celui dans lequel le groupe d’actions sera enregistré. L’abonnement en cours d’utilisation est automatiquement renseigné.

7.  Sélectionnez le **groupe de ressources** auquel cette alerte sera associée dans l’**abonnement**.

8.  Définissez ensuite une liste d’actions à l’aide d’une combinaison des éléments suivants :
  1. **Nom :** identificateur unique dans le groupe d’actions.
  2. **Type d’action :** définit l’action à effectuer. Les options sont Envoyer un SMS, Envoyer un e-mail ou Appeler un webhook.
  3. **Détails :** selon le type d’action, le numéro de téléphone, l’adresse e-mail ou l’URI du webhook correspondant doit être fourni.

9.  Quand vous avez terminé, sélectionnez **OK** pour créer le groupe d’actions.

## <a name="managing-your-action-groups"></a>Gestion des groupes d’actions ##
Une fois que vous avez créé un groupe d’actions, il apparaît dans la section Groupes d’actions du service Monitor. Sélectionnez le groupe d’actions à gérer. Vous pouvez effectuer les opérations suivantes :
* Ajouter, modifier ou supprimer des actions.
-   Supprimer le groupe d’actions.

## <a name="next-steps"></a>Étapes suivantes : ##
En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md)  
[Comprendre le schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md)  
En savoir plus sur la [restriction](monitoring-alerts-rate-limiting.md) des alertes  
Obtenir une [vue d’ensemble des alertes de journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes  
[Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md)

