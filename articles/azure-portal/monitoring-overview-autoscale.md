<properties
	pageTitle="Vue d’ensemble de la mise à l’échelle automatique sur les machines virtuelles Microsoft Azure, les services cloud et les applications web | Microsoft Azure"
	description="Vue d’ensemble de la mise à l’échelle automatique dans Microsoft Azure. S’applique aux machines virtuelles, aux services cloud et aux applications web."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="robb"/>

# Vue d’ensemble de la mise à l’échelle automatique sur les machines virtuelles Microsoft Azure, les services cloud et les applications web

Cet article décrit la mise à l’échelle automatique Microsoft Azure ainsi que ses avantages, et vous permet de commencer à l’utiliser.

La mise à l’échelle automatique d’Azure Insights s’applique uniquement aux éléments suivants

* [Jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [Cloud Services](https://azure.microsoft.com/services/cloud-services/)
* [App Service - applications web](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure dispose de deux méthodes de mise à l’échelle automatique. L’ancienne version de la mise à l’échelle automatique s’applique aux machines virtuelles (groupes à haute disponibilité). Cette fonctionnalité assure une prise en charge limitée et nous vous recommandons de migrer vers les jeux de mise à l’échelle de machine virtuelle pour une prise en charge plus rapide et plus fiable de la mise à l’échelle automatique. Un lien sur la façon d’utiliser l’ancienne technologie est inclus dans cet article.


## Qu’est-ce que la mise à l’échelle automatique 

La mise à l’échelle automatique vous permet de disposer de la bonne quantité de ressources en cours d’exécution pour gérer la charge sur votre application sans gaspiller de l’argent pour des ressources inactives. Elle permet d’ajouter ou de supprimer automatiquement des ressources de calcul en suivant un ensemble de règles. La figure 1 illustre ce concept.

![Explication du concept de mise à l’échelle automatique](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**Figure 1 : explication du concept de mise à l’échelle automatique**

La mise à l’échelle automatique s’effectue uniquement horizontalement, ce qui correspond à une augmentation (« out ») ou à une diminution (« in ») du nombre d’instances de machine virtuelle. La mise à l’échelle horizontale est plus flexible dans un environnement cloud, car elle vous permet d’exécuter des milliers de machines virtuelles pour gérer la charge. L’autre type de mise à l’échelle est la mise à l’échelle verticale. La mise à l’échelle verticale conserve le même nombre de machines virtuelles, mais rend la machine virtuelle plus (« up ») ou moins (« down ») puissante. La puissance se mesure en termes de mémoire, de vitesse du processeur, d’espace disque, etc. La mise à l’échelle verticale a davantage de limites. Elle est dépendante de la disponibilité d’un matériel plus puissant, ce qui peut varier d’une région à l’autre, et peut atteindre rapidement sa limite. La mise à l’échelle verticale nécessite généralement un début et une fin de machine virtuelle. Pour plus d’informations, voir [Évolution verticale des machines virtuelles Azure avec Azure Automation](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


Dans le cadre de la mise à l’échelle automatique, vous devez créer des règles pour définir quand mettre à l’échelle les ressources. Pour contrôler l’action de mise à l’échelle, vous pouvez utiliser les critères suivants

* Nombre **minimum** et **maximum** d’instances à exécuter. Le nombre minimum permet de vous assurer que votre application est toujours en cours d’exécution et le nombre maximum permet de contrôler les coûts.
* **Une règle ou une condition** pour la mise à l’échelle. Il peut s’agir d’une mise à l’échelle en fonction des mesures ou de la planification.
* **Un temps de recharge**, qui correspond au temps d’attente requis après un événement de mise à l’échelle automatique avant d’autoriser un autre événement de mise à l’échelle automatique. Cette durée sert à éviter une « oscillation », qui se produit lorsque des machines virtuelles sont rapidement ajoutées et supprimées. La mise en route et l’arrêt des machines virtuelles ont un coût. L’oscillation ne permet pas de faire des économies et le fait de démarrer et d’arrêter la machine virtuelle ne permet aucun traitement utile. C’est donc pire que de laisser la machine virtuelle s’exécuter.

   
La liste complète des valeurs configurables est disponible dans l’[API REST de mise à l’échelle](https://msdn.microsoft.com/library/dn931928.aspx).


## Diagramme conceptuel  
La figure 2 montre une vue d’ensemble conceptuelle de la mise à l’échelle automatique, suivie d’une explication du diagramme.

![Ajouter une alerte](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**Figure 2 : vue d’ensemble du processus de mise à l’échelle automatique**

## Mesures de ressources 
Les ressources génèrent des mesures, qui sont ensuite traitées par des règles. Les mesures sont fournies via différentes méthodes. Les jeux de mise à l’échelle de machine virtuelle utilisent les données de télémétrie des agents de diagnostics Azure, tandis que les données de télémétrie pour les applications web et les services cloud proviennent directement de l’infrastructure Azure. Parmi les statistiques couramment utilisées figurent l’utilisation du processeur, l’utilisation de la mémoire, le nombre de threads, la longueur de la file d’attente et l’utilisation du disque. Pour obtenir une liste des données de télémétrie que vous pouvez utiliser, voir [Mesures courantes pour la mise à l’échelle automatique](insights-autoscale-common-metrics.md).

 
## Time
Les règles basées sur la planification s’appuient sur l’heure UTC. Vous devez définir votre fuseau horaire correctement lorsque vous configurez vos règles.

## Règles de mise à l’échelle automatique
Le diagramme montre une seule règle de mise à l’échelle automatique, mais vous pouvez en avoir plusieurs. Vous pouvez créer des règles complexes qui se chevauchent en fonction de vos besoins. Les types de règles incluent
 
 - **Basées sur des mesures** : par exemple, effectuer cette action lorsque l’utilisation du processeur est supérieure à 50 %.
 - **Basées sur l’heure** : par exemple, déclencher un webhook chaque samedi à 8h dans un fuseau horaire donné.

 
## Actions de mise à l’échelle automatique et automatisation

Les règles peuvent déclencher un ou plusieurs types d’actions.

- **Mise à l’échelle** : mettre les machines virtuelles à l’échelle
- **E-mail** : envoyer un courrier électronique aux administrateurs et coadministrateurs d’un abonnement, et à toute adresse électronique supplémentaire que vous spécifiez
- **Automatisation via webhooks** : appeler des webhooks, qui peuvent déclencher plusieurs actions complexes à l’intérieur ou en dehors d’Azure. Dans Azure, vous pouvez démarrer un runbook Azure Automation, une fonction Azure ou une application logique Azure. Parmi les URL tierces en dehors d’Azure figurent des services comme Slack et Twilio.


## Détails des règles Resource Manager

Les règles de mise à l’échelle automatique ont la structure suivante dans un modèle Azure Resource Manager.

![Structure de règle de mise à l’échelle automatique d’Azure Resource Manager](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

Vous pouvez répertorier plusieurs profils. Chaque profil peut avoir plusieurs règles. Les méthodes de notification et les emplacements sont inclus après vos profils. Parmi les notifications figurent par exemple un webhook avec l’URI ou des messages électroniques avec les adresses de messagerie.

Pour plus d’exemples de code, consultez

* [Configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les groupes de machines virtuelles identiques](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [API REST de mise à l’échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)

## Méthodes d’accès 
Vous pouvez configurer des règles de mise à l’échelle automatique via

- Portail Azure
- PowerShell
- Interface de bibliothèque commune (Common Library Interface, CLI)
- API REST Insights

## Guides sur la mise à l’échelle automatique

- [Mise à l’échelle des services cloud](../cloud-services/cloud-services-how-to-scale-portal.md)
- [Mise à l’échelle des applications web](insights-how-to-scale.md)
- [Mise à l’échelle de groupes à haute disponibilité de machines virtuelles classiques](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [Mise à l’échelle de jeux de mise à l’échelle de machine virtuelle sous Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [Mise à l’échelle de jeux de mise à l’échelle de machine virtuelle sous Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [Configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les groupes de machines virtuelles identiques](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## Étapes suivantes

Pour en savoir plus sur la mise à l’échelle automatique, utilisez les guides sur la mise à l’échelle automatique répertoriés précédemment ou consultez les ressources suivantes :

- [Métriques courantes pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-common-metrics.md)
- [Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-best-practices.md)
- [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique](insights-autoscale-to-webhook-email.md)
- [API REST de mise à l’échelle](https://msdn.microsoft.com/library/dn931953.aspx) : voir l’intégralité de l’API et la signification de chacun des champs et des valeurs
- [Dépannage de la mise à l’échelle automatique des jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->