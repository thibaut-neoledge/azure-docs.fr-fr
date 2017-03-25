---
title: "Vue d’ensemble de la mise à l’échelle automatique sur les machines virtuelles Microsoft Azure, les services cloud et les applications web | Microsoft Docs"
description: "Vue d’ensemble de la mise à l’échelle automatique dans Microsoft Azure. S’applique aux machines virtuelles, aux services cloud et aux applications web."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 74bf03be-e658-4239-a214-c12424b53e4c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 99c972b4adf1dedbc556b20cdc7587edeb7f50e0
ms.lasthandoff: 03/09/2017


---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Vue d’ensemble de la mise à l’échelle automatique sur les machines virtuelles Microsoft Azure, les services cloud et les applications web
Cet article décrit la mise à l’échelle automatique Microsoft Azure ainsi que ses avantages, et comment commencer à l’utiliser.  

La mise à l’échelle automatique Azure Monitor s’applique uniquement aux [jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [services cloud](https://azure.microsoft.com/services/cloud-services/) et à [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> Azure dispose de deux méthodes de mise à l’échelle automatique. L’ancienne version de la mise à l’échelle automatique s’applique aux machines virtuelles (groupes à haute disponibilité). Cette fonctionnalité assure une prise en charge limitée et nous vous recommandons de migrer vers les jeux de mise à l’échelle de machine virtuelle pour une prise en charge plus rapide et plus fiable de la mise à l’échelle automatique. Un lien sur la façon d’utiliser l’ancienne technologie est inclus dans cet article.  
>
>

## <a name="what-is-autoscale"></a>Qu’est-ce que la mise à l’échelle automatique ?
La mise à l’échelle automatique vous permet de disposer de la bonne quantité de ressources en cours d’exécution pour gérer la charge sur votre application. Elle vous permet d’ajouter des ressources pour gérer les augmentations de charge et d’économiser de l’argent en supprimant les ressources qui sont inactives. Vous spécifiez un nombre minimal et maximal d’instances à exécuter et ajoutez ou supprimez des machines virtuelles automatiquement en fonction d’un ensemble de règles. Avoir un nombre minimal d’instances permet de vous assurer que votre application est toujours en cours d’exécution même en l’absence de charge. Avoir un nombre maximal d’instances limite votre coût total possible par heure. Vous effectuez une mise à l’échelle automatique entre ces deux extrêmes, à l’aide de règles que vous créez.

 ![Description de la mise à l’échelle automatique. Ajouter et supprimer des machines virtuelles](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Lorsque les conditions relatives aux règles sont remplies, une ou plusieurs actions de mise à l’échelle automatique sont déclenchées. Vous pouvez ajouter et supprimer des machines virtuelles ou effectuer d’autres actions. Le schéma conceptuel suivant illustre ce processus.  

 ![Diagramme de flux de la mise à l’échelle automatique](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

La description suivante s’applique aux éléments du schéma précédent.   

## <a name="resource-metrics"></a>Mesures de ressources
Les ressources génèrent des mesures, qui sont ensuite traitées par des règles. Les mesures sont fournies via différentes méthodes.
Les jeux de mise à l’échelle de machine virtuelle utilisent les données de télémétrie des agents de diagnostics Azure, tandis que les données de télémétrie pour les applications web et les services cloud proviennent directement de l’infrastructure Azure. Parmi les statistiques couramment utilisées figurent l’utilisation du processeur, l’utilisation de la mémoire, le nombre de threads, la longueur de la file d’attente et l’utilisation du disque. Pour obtenir une liste des données de télémétrie que vous pouvez utiliser, voir [Mesures courantes pour la mise à l’échelle automatique](insights-autoscale-common-metrics.md).

## <a name="time"></a>Time
Les règles basées sur la planification s’appuient sur l’heure UTC. Vous devez définir votre fuseau horaire correctement lorsque vous configurez vos règles.  

## <a name="rules"></a>Règles
Le diagramme montre une seule règle de mise à l’échelle automatique, mais vous pouvez en avoir plusieurs. Vous pouvez créer des règles complexes qui se chevauchent en fonction de vos besoins.  Les types de règles incluent  

* **Basées sur des mesures** : par exemple, effectuer cette action lorsque l’utilisation du processeur est supérieure à 50 %.
* **Basées sur l’heure** : par exemple, déclencher un webhook chaque samedi à 8h dans un fuseau horaire donné.

Les règles basées sur les mesures mesurent la charge de l’application et ajoutent ou suppriment des machines virtuelles en fonction de cette charge. Les règles basées sur la planification vous permettent d’effectuer une mise à l’échelle lorsque vous voyez des modèles horaires dans votre charge et que vous souhaitez effectuer la mise à l’échelle avant qu’une augmentation ou diminution de charge possible ne se produise.  

## <a name="actions-and-automation"></a>Actions et automatisation
Les règles peuvent déclencher un ou plusieurs types d’actions.

* **Mise à l’échelle** : mettre les machines virtuelles à l’échelle
* **E-mail** : envoyer un e-mail aux administrateurs et coadministrateurs d’un abonnement, et/ou à toute adresse e-mail supplémentaire que vous spécifiez
* **Automatisation via webhooks** : appeler des webhooks, qui peuvent déclencher plusieurs actions complexes à l’intérieur ou en dehors d’Azure. Dans Azure, vous pouvez démarrer un runbook Azure Automation, une fonction Azure ou une application logique Azure. Parmi les URL tierces en dehors d’Azure figurent des services comme Slack et Twilio.

## <a name="autoscale-settings"></a>Paramètres de mise à l’échelle automatique
La mise à l’échelle automatique utilise la terminologie et la structure suivantes.

- Un **paramètre de mise à l’échelle automatique** est lu par le moteur de mise à l’échelle automatique afin de déterminer si une montée ou une descente en puissance est nécessaire. Il contient un ou plusieurs profils, des informations sur la ressource cible et des paramètres de notification.

    - Un **profil de mise à l’échelle automatique** est la combinaison des éléments suivants :

        - Un **paramètre de capacité** indiquant les valeurs minimum, maximum et par défaut pour le nombre d’instances.
        - Un **ensemble de règles**, comprenant chacun un déclencheur (heure ou métrique) et une action de mise à l’échelle (augmentation ou réduction).
        - Une **périodicité** indiquant le moment auquel une mise à l’échelle automatique doit appliquer ce profil.

        Vous pouvez avoir plusieurs profils, ce qui vous permet de prendre en charge des exigences différentes qui se chevauchent. Vous pouvez avoir des profils de mise à l’échelle automatique différents pour différentes heures de la journée ou jours de la semaine, par exemple.

    - Un **paramètre de notification** définit quelles notifications doivent se produire lorsqu’un événement de mise à l’échelle automatique a lieu en fonction de la satisfaction des critères de l’un des profils de paramètre de mise à l’échelle automatique. La mise à l’échelle automatique peut notifier une ou plusieurs adresses e-mail ou appeler un ou plusieurs webhooks.


![Structure de règle, de profil et de paramètre de mise à l’échelle automatique Azure](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

La liste complète des descriptions et champs configurables est disponible dans [l’API REST de mise à l’échelle automatique](https://msdn.microsoft.com/library/dn931928.aspx).

Pour plus d’exemples de code, consultez

* [Configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les groupes de machines virtuelles identiques](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Paramètres de mise à l’échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Mise à l’échelle horizontale/verticale
La mise à l’échelle automatique s’effectue uniquement horizontalement, ce qui correspond à une augmentation (« out ») ou à une diminution (« in ») du nombre d’instances de machine virtuelle.  La mise à l’échelle horizontale est plus flexible dans un environnement cloud, car elle vous permet d’exécuter des milliers de machines virtuelles pour gérer la charge.

La mise à l’échelle verticale est différente. Elle conserve le même nombre de machines virtuelles, mais rend la machine virtuelle plus (« up ») ou moins (« down ») puissante. La puissance se mesure en termes de mémoire, de vitesse du processeur, d’espace disque, etc.  La mise à l’échelle verticale a davantage de limites. Elle dépend de la disponibilité d’un matériel plus puissant, qui peut atteindre rapidement sa limite et varier d’une région à l’autre. La mise à l’échelle verticale nécessite généralement un arrêt et un redémarrage de la machine virtuelle.

Pour plus d’informations, voir [Évolution verticale des machines virtuelles Azure avec Azure Automation](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Méthodes d’accès
Vous pouvez configurer la mise à l’échelle automatique via

* [Portail Azure](insights-how-to-scale.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Interface de ligne de commande interplateforme (CLI)](insights-cli-samples.md#autoscale)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Services pris en charge pour la mise à l’échelle automatique
| de diffusion en continu | Schéma et documentation |
| --- | --- |
| Applications Web |[Mise à l’échelle des applications web](insights-how-to-scale.md) |
| Services cloud |[Mise à l’échelle automatique d’un service cloud](../cloud-services/cloud-services-how-to-scale.md) |
| Machines virtuelles : classique |[Mise à l’échelle de groupes à haute disponibilité de machines virtuelles classiques](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Machines virtuelles : jeux de mise à l’échelle Windows |[Mise à l’échelle des jeux de mise à l’échelle de machine virtuelle dans Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Machines virtuelles : jeux de mise à l’échelle Linux |[Mise à l’échelle des jeux de mise à l’échelle de machine virtuelle dans Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Machines virtuelles : exemple Windows |[Configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les groupes de machines virtuelles identiques](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise à l’échelle automatique, utilisez les guides sur la mise à l’échelle automatique répertoriés précédemment ou consultez les ressources suivantes :

* [Mesures courantes pour la mise à l’échelle automatique dans Azure Monitor](insights-autoscale-common-metrics.md)
* [Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-best-practices.md)
* [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique](insights-autoscale-to-webhook-email.md)
* [Paramètres de mise à l’échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)
* [Dépannage de la mise à l’échelle automatique avec des jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

