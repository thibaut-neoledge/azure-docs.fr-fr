---
title: "Prise en main de la mise à l’échelle automatique dans Azure | Microsoft Docs"
description: "Découvrez comment effectuer une mise à l’échelle de votre ressource dans Azure."
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 4faccae708b5407d1eb64e746824a23688bc5834
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Prise en main de la mise à l’échelle automatique dans Azure
Cet article décrit comment configurer votre paramètre de mise à l’échelle automatique pour votre ressource dans le portail Azure.

La mise à l’échelle automatique Azure Monitor s’applique uniquement aux jeux de mise à l’échelle de machine virtuelle, services cloud, plans App Service et environnements App Service. 

# <a name="lets-get-started"></a>Prise en main

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>Découvrir les paramètres de mise à l’échelle automatique dans vos abonnements
Vous pouvez découvrir toutes les ressources pour lesquelles la mise à l’échelle automatique est applicable dans Azure Monitor. Suivez les étapes ci-dessous pour une procédure pas à pas.

- Ouvrez le [portail Azure][1]
- Cliquez sur l’icône Azure Monitor dans le volet de navigation gauche.
  ![Lancer Azure Monitor][2]
- Cliquez sur le paramètre de mise à l’échelle automatique pour afficher toutes les ressources pour lesquelles la mise à l’échelle automatique est applicable, ainsi que son état actuel de mise à l’échelle automatique ![Découvrir la mise à l’échelle automatique dans Azure Monitor][3]

Vous pouvez utiliser le volet de filtre en haut pour réduire l’étendue de la liste pour sélectionner des ressources dans un groupe de ressources spécifique, des types de ressources spécifiques ou une ressource spécifique.

Pour chaque ressource, vous trouverez le nombre d’instances en cours ainsi que son état de mise à l’échelle automatique. L’état de mise à l’échelle automatique peut être

- Non configuré : vous n’avez pas encore activé le paramètre de mise à l’échelle automatique pour cette ressource
- Activé : Vous avez activé le paramètre de mise à l’échelle automatique pour cette ressource
- Désactivé : Vous avez désactivé le paramètre de mise à l’échelle automatique pour cette ressource

## <a name="create-your-first-auto-scale-setting"></a>Créer votre premier paramètre de mise à l’échelle automatique

Suivons maintenant une procédure simple pour créer votre premier paramètre de mise à l’échelle automatique.

- Ouvrez le panneau « Mise à l’échelle automatique » dans Azure Monitor et sélectionnez une ressource à mettre à l’échelle. (les étapes ci-dessous utilisent un plan de service d’application associé à une application web. Vous pouvez [Créer votre première application web ASP.NET dans Azure en 5 minutes][4])
- Dans le panneau de configuration de mise à l’échelle pour la ressource, notez que le nombre d’instances en cours est 1. Cliquez sur « Activer la mise à l’échelle automatique ».
  ![Paramètre d’échelle pour la nouvelle application web][5]
- Fournissez un nom pour le paramètre de mise à l’échelle, puis cliquez sur « Ajouter une règle ». Notez les options de règle de mise à l’échelle qui s’ouvrent dans un volet contextuel dans la partie droite. Par défaut, l’option de mise à l’échelle du nombre d’instances de 1 est définie si le pourcentage processeur de la ressource dépasse 70 %. Laissez les valeurs par défaut et cliquez sur Ajouter.
  ![Créer le paramètre de mise à l’échelle pour une application web][6]
- Vous avez créé votre première règle de mise à l’échelle. Notez que l’expérience utilisateur recommande les meilleures pratiques et indique qu’« il est recommandé d’avoir au moins une règle de mise à l’échelle ». Pour ce faire, cliquez sur « Ajouter une règle » et réglez l’« Opérateur » sur « Inférieur à », « Seuil » sur « 20 » et « Opération » sur « Diminuer le nombre de ». Vous devez maintenant avoir un paramètre de mise à l’échelle qui fait monter/diminuer en puissance en fonction de l’utilisation du processeur.
  ![Mise à l’échelle en fonction du processeur][8]
- Cliquez sur « Enregistrer »

Félicitations ! Vous avez maintenant correctement créé votre premier paramètre de mise à l’échelle pour faire évoluer automatiquement votre application web en fonction de l’utilisation du processeur.

> Remarque : Les mêmes étapes sont applicables pour commencer avec un rôle de service cloud ou un jeu de mise à l’échelle de machine virtuelle.

# <a name="other-considerations"></a>Autres points à considérer
## <a name="scale-based-on-a-schedule"></a>Mise à l'échelle en fonction d’une planification
En plus de toujours mettre à l’échelle en fonction du processeur, vous pouvez aussi définir votre mise à l’échelle différemment certains jours de la semaine.

- Cliquez sur « Ajouter une condition de mise à l’échelle »
- La définition du mode de mise à l’échelle et des règles est identique à la condition par défaut
- Sélectionnez « Répéter à des jours spécifiques » pour la planification
- Sélectionnez les jours, puis les heures de début/fin auxquelles la condition de mise à l’échelle doit être appliquée pour les jours sélectionnés

![Condition de mise à l’échelle basée sur une planification][9]
## <a name="scale-differently-on-specific-dates"></a>Mettre à l’échelle différemment à des dates spécifiques
En plus de toujours mettre à l’échelle en fonction du processeur, vous pouvez aussi définir votre mise à l’échelle différemment lors de certaines dates.

- Cliquez sur « Ajouter une condition de mise à l’échelle »
- La définition du mode de mise à l’échelle et des règles est identique à la condition par défaut
- Sélectionnez « Spécifier les dates de début et de fin » pour la planification
- Sélectionnez les dates de début/fin, ainsi que les heures de début/fin auxquelles la condition de mise à l’échelle doit être appliquée pour les dates sélectionnées

![Condition de mise à l’échelle en fonction des dates][10]

## <a name="view-the-scale-history-of-your-resource"></a>Afficher l’historique de mise à l’échelle de votre ressource
Chaque fois que votre ressource monte/diminue en puissance, un événement est consigné dans le journal d’activité. Vous pouvez afficher l’historique de mise à l’échelle de votre ressource pour les dernières 24 heures en basculant vers l’onglet « Historique d’exécution ».

![Historique d’exécution][11]

Si vous souhaitez afficher l’historique de mise à l’échelle complet (pendant jusqu'à 90 jours), vous pouvez cliquer sur « Cliquez ici pour plus d’informations ». Cette action lance le journal d’activité avec votre ressource et la catégorie « mise à l’échelle » présélectionnée.

## <a name="view-the-scale-definition-of-the-resource"></a>Afficher la définition de mise à l’échelle de la ressource
Le paramètre de mise à l’échelle automatique est une ressource ARM. Vous pouvez afficher la définition de la mise à l’échelle dans JSON en basculant vers l’onglet « JSON ».

![Définition de mise à l’échelle][12]

Vous pouvez apporter des modifications dans le JSON directement, si nécessaire. Ces modifications seront reflétées après enregistrement.

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>Désactiver la mise à l’échelle automatique et mettre à l’échelle manuellement vos instances
Il peut arriver que vous souhaitiez désactiver vos paramètres actuels de mise à l’échelle et configurer manuellement votre ressource.

Cliquez sur le bouton « Désactiver la mise à l’échelle automatique » en haut.
![Désactiver la mise à l’échelle automatique][13]

Notez que cette option désactive votre configuration, et que vous pouvez toujours y revenir une fois que vous activez à nouveau à la mise à l’échelle automatique. Vous pouvez maintenant définir le nombre d’instances à mettre à l’échelle manuellement.

![Définir l’échelle manuelle][14]

Vous pouvez toujours revenir à la mise à l’échelle automatique en cliquant sur « Activer la mise à l’échelle automatique », puis sur « Enregistrer ».

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

