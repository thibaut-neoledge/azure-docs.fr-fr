---
title: "Créer et gérer des groupes d’actions dans le portail Azure | Microsoft Docs"
description: 
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
ms.openlocfilehash: 9a290422bf897397942fccf2e3733d0709701e91
ms.lasthandoff: 03/31/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Créer et gérer des groupes d’actions dans le portail Azure
## <a name="overview"></a>Vue d'ensemble ##
Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Les groupes d’actions vous permettent de configurer une liste de destinataires. Ces groupes peuvent être utilisés lors de la définition d’alertes de journal d’activité, ce qui permet de s’assurer qu’un groupe d’actions particulier est notifié au déclenchement de l’alerte de journal d’activité.

Un groupe d’actions peut contenir jusqu’à 10 actions de chaque type. Une action est définie par une combinaison des éléments suivants :

**Nom :** identificateur unique dans le groupe d’actions.  
**Type d’action :** définit l’action à effectuer. Les options sont Envoyer un SMS, Envoyer un e-mail ou Appeler un webhook.  
**Détails :** selon le type d’action, le numéro de téléphone, l’adresse e-mail ou l’URI du webhook correspondant doit être fourni.

Pour configurer des alertes de notification d’intégrité du service et obtenir des informations à ce sujet, vous pouvez utiliser :
* le [portail Azure](monitoring-action-groups.md)
- les [Modèles Microsoft Azure Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>Création d’un groupe d’actions pour le portail Azure ##
1.    Dans le [portail](https://portal.azure.com), accédez au service **Monitor**.

    ![Surveiller](./media/monitoring-action-groups/home-monitor.png)
2.    Cliquez sur l’option **Monitor** pour ouvrir le panneau Monitor. Ce panneau rassemble tous vos paramètres de surveillance et données dans une vue consolidée. Il ouvre d’abord la section **Journal d’activité** .

3.    Cliquez sur la section **Groupes d’actions**.

    ![Action-Group](./media/monitoring-action-groups/action-groups-blade.png)
4.    Cliquez sur la commande de groupe d’actions **Add** et remplissez les champs.

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.    Indiquez un **Nom** et un **Nom court** pour le groupe d’actions. Le nom court sera indiqué dans les notifications envoyées à ce groupe.

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.    L’**Abonnement** est celui dans lequel le groupe d’actions sera enregistré. L’abonnement en cours d’utilisation est automatiquement renseigné.

7.    Choisissez le **Groupe de ressources** pour ce groupe d’actions.

8.    Définissez ensuite une liste d’actions à l’aide d’une combinaison des éléments suivants :
  1. **Nom :** identificateur unique dans le groupe d’actions.
  2. **Type d’action :** définit l’action à effectuer. Les options sont Envoyer un SMS, Envoyer un e-mail ou Appeler un webhook.
  3. **Détails :** selon le type d’action, le numéro de téléphone, l’adresse e-mail ou l’URI du webhook correspondant doit être fourni.

9.    Quand vous avez terminé, sélectionnez **OK** pour créer le groupe d’actions.

## <a name="managing-your-action-groups"></a>Gestion des groupes d’actions ##
Une fois que vous avez créé un groupe d’actions, il apparaît dans la section Groupes d’actions du service Monitor. Sélectionnez le groupe d’actions à gérer. Vous pouvez effectuer les opérations suivantes :
* Ajouter, modifier ou supprimer des destinataires.
-    Supprimer le groupe d’actions.

## <a name="next-steps"></a>Étapes suivantes : ##
En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md)  
[Comprendre le schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md)  
En savoir plus sur la [restriction](monitoring-alerts-rate-limiting.md) des alertes  
Obtenir une [vue d’ensemble des alertes de journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes  
[Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md)

