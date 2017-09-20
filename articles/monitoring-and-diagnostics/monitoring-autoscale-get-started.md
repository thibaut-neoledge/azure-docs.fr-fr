---
title: "Bien démarrer avec la mise à l’échelle automatique dans Azure | Documents Microsoft"
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
ms.date: 07/07/2017
ms.author: rajram
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 68cb624b3ef4a77e7cfc949979e0b1949c2e5535
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="get-started-with-autoscale-in-azure"></a>Bien démarrer avec la mise à l’échelle automatique dans Azure
Cet article décrit comment configurer vos paramètres de mise à l’échelle automatique pour votre ressource dans le portail Microsoft Azure.

La mise à l’échelle automatique Azure Monitor s’applique uniquement aux jeux de mise à l’échelle de machines virtuelles, services cloud, plans Azure App Service et environnements App Service. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Découvrir les paramètres de mise à l’échelle automatique dans votre abonnement
Vous pouvez découvrir toutes les ressources pour lesquelles la mise à l’échelle automatique est applicable dans Azure Monitor. Pour une procédure pas à pas, procédez comme suit :

1. Ouvrez le [portail Azure][1]
2. Cliquez sur l’icône Azure Monitor dans le volet gauche.
  ![Ouvrez Azure Monitor][2]
3. Cliquez sur **Mise à l’échelle automatique** pour afficher toutes les ressources pour lesquelles la mise à l’échelle automatique est applicable, ainsi que leur état actuel de mise à l’échelle automatique.
  ![Découvrir la mise à l’échelle automatique dans Azure Monitor][3]

Vous pouvez utiliser le volet de filtre en haut pour réduire l’étendue de la liste afin de sélectionner des ressources dans un groupe de ressources spécifique, des types de ressources spécifiques ou une ressource spécifique.

Pour chaque ressource, vous trouverez le nombre d’instances en cours ainsi que son état de mise à l’échelle automatique. L’état de mise à l’échelle automatique peut être :

- **Non configuré** : vous n’avez pas encore activé la mise à l’échelle automatique pour cette ressource.
- **Activé** : vous avez activé la mise à l’échelle automatique pour cette ressource.
- **Désactivé** : vous avez désactivé la mise à l’échelle automatique pour cette ressource.

## <a name="create-your-first-autoscale-setting"></a>Créez votre premier paramètre de mise à l’échelle automatique

Suivons maintenant une procédure simple pour créer votre premier paramètre de mise à l’échelle automatique.

1. Ouvrez la panneau **Mise à l’échelle automatique** dans Azure Monitor et sélectionnez une ressource à mettre à l’échelle. (les étapes ci-dessous utilisent un plan App Service associé à une application Web. Vous pouvez [créer votre première application Web ASP.NET dans Azure en 5 minutes][4])
2. Notez que le nombre d’instances actuel est 1. Cliquez sur **Activer la mise à l’échelle automatique**.
  ![Paramètre d’échelle pour la nouvelle application web][5]
3. Fournissez un nom pour le paramètre de mise à l’échelle, puis cliquez sur **Ajouter une règle**. Notez les options de règle de mise à l’échelle qui s’ouvrent dans un volet contextuel dans la partie droite. Par défaut, l’option de mise à l’échelle du nombre d’instances est définie sur 1 si le pourcentage processeur de la ressource dépasse 70 %. Laissez les valeurs par défaut et cliquez sur **Ajouter**.
  ![Créer le paramètre de mise à l’échelle pour une application web][6]
4. Vous avez créé votre première règle de mise à l’échelle. Notez que l’expérience utilisateur recommande les meilleures pratiques et indique « qu’il est recommandé d’avoir au moins une règle de mise à l’échelle. » Pour ce faire :
  
    a. Cliquez sur **Ajouter une règle**. 

    b. Définissez **Opérateur** sur **moins de**.

    c. Définissez **Seuil** sur **20**.

    d. Définissez **Opération** sur **Diminuer le nombre par**.

   Vous devez maintenant avoir un paramètre de mise à l’échelle qui fait monter/diminuer en puissance en fonction de l’utilisation du processeur.
   ![Mise à l’échelle en fonction du processeur][8]
5. Cliquez sur **Enregistrer**.

Félicitations ! Vous avez maintenant correctement créé votre premier paramètre de mise à l’échelle pour mettre à l’échelle automatiquement votre application Web en fonction de l’utilisation du processeur.

> [!NOTE] 
> Les mêmes étapes sont applicables pour bien démarrer avec un jeu de mise à l’échelle de machines virtuelles ou un rôle de service cloud.

## <a name="other-considerations"></a>Autres points à considérer
### <a name="scale-based-on-a-schedule"></a>Mise à l'échelle en fonction d’une planification
En plus de la mise à l’échelle en fonction du processeur, vous pouvez aussi définir votre mise à l’échelle différemment pour certains jours de la semaine.

1. Cliquez sur **Ajouter une condition de mise à l’échelle**.
2. La définition du mode de mise à l’échelle et des règles est identique à la condition par défaut.
3. Sélectionnez **Répéter des jours spécifiques** pour la planification.
4. Sélectionnez les jours et les heures de début/fin auxquels la condition de mise à l’échelle doit être appliquée.

![Condition de mise à l’échelle basée sur une planification][9]
### <a name="scale-differently-on-specific-dates"></a>Mettre à l’échelle différemment à des dates spécifiques
En plus de la mise à l’échelle en fonction du processeur, vous pouvez aussi définir votre mise à l’échelle différemment pour certaines dates spécifiques.

1. Cliquez sur **Ajouter une condition de mise à l’échelle**.
2. La définition du mode de mise à l’échelle et des règles est identique à la condition par défaut.
3. Sélectionnez **Spécifier les dates de début et de fin** pour la planification.
4. Sélectionnez les dates et les heures de début/fin auxquelles la condition de mise à l’échelle doit être appliquée.

![Condition de mise à l’échelle en fonction des dates][10]

### <a name="view-the-scale-history-of-your-resource"></a>Afficher l’historique de mise à l’échelle de votre ressource
Chaque fois que votre ressource monte/diminue en puissance, un événement est enregistré dans le journal d’activité. Vous pouvez afficher l’historique de la mise à l’échelle de votre ressource pour les dernières 24 heures en basculant vers l’onglet **Exécuter l’historique**.

![Historique d’exécution][11]

Si vous souhaitez afficher l’historique de la mise à l’échelle complet (jusqu'à 90 jours), sélectionnez **Cliquez ici pour obtenir plus de détails**. Ce journal d’activité s’ouvre, avec la mise à l’échelle automatique présélectionnée pour la ressource et la catégorie.

### <a name="view-the-scale-definition-of-your-resource"></a>Afficher la définition de mise à l’échelle de votre ressource
La mise à l’échelle est une ressource Azure Resource Manager. Vous pouvez afficher la définition de la mise à l’échelle dans JSON en basculant vers l’onglet **JSON**.

![Définition de mise à l’échelle][12]

Vous pouvez apporter des modifications dans le JSON directement, si nécessaire. Ces modifications apparaîtront après que les avoir enregistrées.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Désactiver la mise à l’échelle automatique et mettre à l’échelle manuellement vos instances
Il peut arriver que vous souhaitiez désactiver vos paramètres actuels de mise à l’échelle et mettre à l’échelle manuellement votre ressource.

Cliquez sur le bouton **Désactiver la mise à l’échelle automatique** en haut.
![Désactiver la mise à l’échelle automatique][13]

> [!NOTE] 
> Cette option désactive votre configuration. Toutefois, vous pouvez revenir dessus une fois que vous activez à nouveau la mise à l’échelle automatique. 

Vous pouvez maintenant définir le nombre d’instances à mettre à l’échelle sur manuellement.

![Définir l’échelle manuelle][14]

Vous pouvez toujours revenir à la mise à l’échelle automatique en cliquant sur **Activer la mise à l’échelle automatique** puis sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
- [Créez une alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Créez une alerte de journal d’activité pour surveiller tous les échecs d’opérations de diminution et d’augmentation de la taille des instances de la mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
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


