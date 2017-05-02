---
title: "Redémarrage de maintenance pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Redémarrage de maintenance pour les machines virtuelles Linux."
services: virtual-machines-linux
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>Redémarrage de maintenance de machine virtuelle

Dans quelques rares cas, vos machines virtuelles sont redémarrées en raison d’une maintenance prévue sur l’infrastructure sous-jacente. Cela ayant un impact sur la disponibilité de vos machines virtuelles hébergées dans Azure, les éléments suivants peuvent maintenant être utilisés :

-   Notification envoyée au moins 30 minutes avant l’impact.

-   Visibilité sur les fenêtres de maintenance par machine virtuelle.

-   Flexibilité et contrôle dans la définition de l’heure exacte de l’impact de la maintenance sur vos machines virtuelles.

La maintenance dans Microsoft Azure est planifiée en itérations. Les itérations initiales ont une portée moindre dans le but de diminuer le risque impliqué dans le déploiement de nouveaux correctifs et fonctionnalités. Les itérations ultérieures peuvent s’étendre sur plusieurs régions (jamais la même paire de régions). Une machine virtuelle est incluse dans une itération de maintenance unique. Si une itération est abandonnée, les machines virtuelles restantes sont incluses dans une future itération.

L’itération de maintenance planifiée comprend deux phases : une fenêtre de maintenance préemptive et une fenêtre de maintenance planifiée.

La **fenêtre de maintenance préemptive** vous offre la flexibilité de lancer la maintenance sur vos machines virtuelles. De cette manière, vous pouvez déterminer le moment où vos machines virtuelles sont affectées, la séquence de la mise à jour et le délai entre chaque machine virtuelle. Vous pouvez interroger chaque machine virtuelle pour voir si une maintenance est prévue et consulter le résultat de votre dernière demande de maintenance.

La **fenêtre de maintenance planifiée** est utilisée quand Azure a planifié une maintenance sur vos machines virtuelles. Pendant cet intervalle de temps, qui suit la fenêtre de maintenance préemptive, vous pouvez toujours interroger la fenêtre de maintenance, mais vous ne pouvez plus orchestrer la maintenance.

## <a name="availability-considerations-during-planned-maintenance"></a>Considérations relatives à la disponibilité lors de la maintenance planifiée 

### <a name="paired-regions"></a>Régions jumelées

Chaque région Azure est associée à une autre région au sein de la même région géographique, constituant ainsi des paires régionales. Lors de l’exécution de maintenance, Azure met à jour uniquement les instances de machine virtuelle d’une seule région de la paire. Par exemple, lors de la mise à jour des ordinateurs virtuels de l’Amérique du Nord central, Azure ne met à jour dans le même temps aucune machine virtuelle de l’Amérique du Nord méridionale. La mise à jour est planifiée à un autre moment, en activant le basculement ou l’équilibrage de charge entre les régions. En revanche, les autres régions, l’Europe du Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région Est des États-Unis.
En savoir plus sur les [paires de régions Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>Machines virtuelles à instance unique et groupe à haute disponibilité ou groupe de machines virtuelles identiques

Lorsque vous déployez une charge de travail à l’aide de machines virtuelles dans Azure, vous pouvez créer des machines virtuelles dans un groupe à haute disponibilité afin de fournir une haute disponibilité pour votre application. Dans une telle configuration, vous avez la garantie qu’au moins une des machines virtuelles sera disponible pendant une panne ou un événement de maintenance.

Au sein d’un groupe à haute disponibilité, les machines virtuelles individuelles sont réparties sur un maximum de 20 domaines de mise à jour. Lors de la maintenance planifiée, un seul domaine de mise à jour est affecté à un moment donné. L’ordre des domaines de mise à jour affectés peut ne pas être consécutif pendant la maintenance planifiée. Pour les machines virtuelles uniques (qui ne font pas partie d’un groupe à haute disponibilité), il n’existe aucun moyen de prédire ou de déterminer les machines virtuelles qui seront affectées en même temps, ni leur nombre.

Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques en tant que ressource unique.
Le groupe identique offre des garanties similaires à un groupe à haute disponibilité sous la forme de domaines de mise à jour. 

Pour plus d’informations sur la configuration des machines virtuelles pour une haute disponibilité, consultez [*Gérer la disponibilité de vos machines virtuelles Windows*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="scheduled-events"></a>Événements planifiés

Azure Metadata Service vous permet d'obtenir des informations relatives à votre machine virtuelle hébergée dans Azure. Événements planifiés, une des catégories présentées, affiche des informations concernant les événements à venir (par exemple, un redémarrage) afin que votre application puisse s’y préparer et limiter ainsi l’interruption.

Pour plus d’informations sur les événements planifiés, consultez [Azure Metadata Service - Événements planifiés](../virtual-machines-scheduled-events.md).

## <a name="maintenance-discovery-and-notifications"></a>Notifications et détection de maintenance

La planification de la maintenance est visible par les clients au niveau des machines virtuelles individuelles. Vous pouvez utiliser le portail Azure, l’API, PowerShell ou l’interface de ligne de commande pour interroger les fenêtres de maintenance préemptive et planifiée. Attendez-vous en outre à recevoir une notification (par e-mail) dans l’éventualité où une (ou plusieurs) de vos machines virtuelles serait affectée au cours du processus.

Les phases de maintenance préemptive et planifiée commencent par une notification. Attendez-vous à recevoir une seule notification par abonnement Azure. La notification est envoyée à l’administrateur de l’abonnement et au co-administrateur par défaut. Vous pouvez également configurer le public concerné par la notification de maintenance.

### <a name="view-the-maintenance-window-in-the-portal"></a>Afficher la fenêtre de maintenance dans le portail 

Vous pouvez utiliser le portail Azure et rechercher les machines virtuelles sur lesquelles une maintenance est planifiée.

1.  Connectez-vous au portail Azure.

2.  Cliquez sur le panneau **Machines virtuelles** pour l’ouvrir.

3.  Cliquez sur le bouton **Colonnes** pour ouvrir la liste des colonnes disponibles

4.  Sélectionnez et ajoutez les colonnes **Fenêtre de maintenance**. La fenêtre de maintenance apparaît pour les machines virtuelles sur lesquelles une maintenance est planifiée. Une fois la maintenance terminée ou abandonnée, la fenêtre de maintenance n’est plus présentée.

### <a name="query-maintenance-details-using-the-azure-api"></a>Interroger les détails de la maintenance à l’aide de l’API Azure

Utilisez l’[API Obtenir les informations de la machine virtuelle](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) et recherchez le mode instance pour découvrir les détails de la maintenance sur une machine virtuelle individuelle. La réponse inclut les éléments suivants :

  - isCustomerInitiatedMaintenanceAllowed : indique si vous pouvez lancer un redéploiement préemptif sur la machine virtuelle.

  - preMaintenanceWindowStartTime : heure de début de la fenêtre de maintenance préemptive.

  - preMaintenanceWindowEndTime : heure de fin de la fenêtre de maintenance préemptive. Une fois ce délai passé, vous ne pourrez plus lancer de maintenance sur cette machine virtuelle.
    
  - maintenanceWindowStartTime : heure de début de la fenêtre de maintenance planifiée lorsque vos machines virtuelles sont affectées.

  - maintenanceWindowEndTime : heure de fin de la fenêtre de maintenance planifiée.
  
  - lastOperationResultCode : résultat de votre dernière opération Maintenance-Redeploy.
 
  - lastOperationMessage : message décrivant le résultat de votre dernière opération Maintenance-Redeploy.


## <a name="pre-emptive-redeploy"></a>Redéploiement préemptif

L’action de redéploiement préemptif offre la possibilité de contrôler le moment où la maintenance est appliquée à vos machines virtuelles dans Azure. La maintenance planifiée commence par une fenêtre de maintenance préemptive où vous pouvez déterminer l’heure exacte de redémarrage de chacune de vos machines virtuelles. Cette fonctionnalité est notamment utile dans les cas d’utilisation suivants :

-   La maintenance doit être coordonnée avec le client final.

-   La fenêtre de maintenance planifiée proposée par Azure n’est pas suffisante.
    Il est possible que la fenêtre tombe sur la période la plus chargée de la semaine ou qu’elle soit trop longue.

-   Pour les applications à plusieurs instances ou plusieurs niveaux, un délai suffisant est nécessaire entre deux machines virtuelles ou un ordre précis doit être suivi.

Lorsque vous appelez un redéploiement préemptif sur une machine virtuelle, celle-ci est déplacée vers un nœud déjà mis à jour, puis remise sous tension, conservant toutes vos options de configuration et ressources associées. Ce faisant, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

Le redéploiement préemptif est activé une fois par machine virtuelle. Si une erreur survient au cours du processus, l’opération est abandonnée, la machine virtuelle n’est pas affectée et elle est exclue de l’itération de maintenance planifiée. Un nouveau planning vous sera fourni ultérieurement et une nouvelle occasion de planifier et d’organiser l’impact sur vos machines virtuelles vous sera proposée.

