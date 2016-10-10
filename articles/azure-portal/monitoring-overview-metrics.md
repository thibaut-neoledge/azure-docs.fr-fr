<properties
	pageTitle="Vue d’ensemble des mesures dans Microsoft Azure | Microsoft Azure"
	description="Vue d’ensemble des mesures et de leurs utilisations dans Microsoft Azure"
	authors="kamathashwin"
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
	ms.date="09/26/2016"
	ms.author="ashwink"/>

# Vue d’ensemble des mesures dans Microsoft Azure 

Cet article décrit ce que sont les mesures dans Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser.

## Quelles sont les mesures ?

Azure Monitor vous permet d’utiliser la télémétrie pour surveiller les performances et l’intégrité de vos charges de travail sur Azure. Les mesures (aussi appelées compteurs de performances) émises par la plupart des ressources Azure sont le type de données de télémétrie Azure plus important. Azure Monitor propose plusieurs façons de configurer et d’utiliser ces mesures pour l’analyse et le dépannage.


## Que pouvez-vous faire avec les mesures ?

Les mesures sont une source précieuse de télémétrie et vous permettent d’effectuer les opérations suivantes :

- **Suivre les performances** de votre ressource (par exemple une machine virtuelle, un site web ou une application logique) en traçant ses mesures sur un graphique de portail et en épinglant ce graphique sur un tableau de bord.
- **Être averti d’un problème** ayant un impact sur les performances de votre ressource lorsqu’une mesure dépasse un certain seuil.
- **Configurer des actions automatisées**, telles que la mise à l’échelle automatique d’une ressource ou le déclenchement d’un runbook lorsqu’une mesure dépasse un certain seuil.
- **Effectuer des analyses avancées** ou créer des rapports sur les tendances de performances ou d’utilisation de vos ressources.
- **Archiver** l’historique des performances ou d’intégrité de votre ressource **à des fins d’audit/de conformité**.

##  Caractéristiques des mesures
Les mesures présentent les caractéristiques suivantes :

- Toutes les mesures ont **une fréquence d’une minute**. Vous recevez une valeur de mesure chaque minute à partir de votre ressource, pour une visibilité en quasi temps réel de l’intégrité de votre ressource.
- Les mesures sont **disponibles sans avoir à s’abonner** ou à configurer des diagnostics supplémentaires.
- Vous pouvez accéder à **30 jours d’historique** pour chaque mesure. Vous pouvez consulter rapidement les tendances récentes et mensuelles de performances ou d’intégrité de vos ressources.

Vous pouvez :

- Découvrir facilement, accéder à et **afficher toutes les mesures** via le portail Azure lorsque vous sélectionnez une ressource, et les tracer sur un graphique.
- Configurer une **règle d’alerte qui envoie une notification ou prend une action de façon automatique** pour une mesure lorsque celle-ci dépasse le seuil que vous avez défini. La mise à l’échelle automatique est une action automatique spéciale qui vous permet de faire évoluer votre ressource afin de répondre aux requêtes entrantes ou à la charge sur votre site web ou vos ressources de calcul. Vous pouvez configurer une règle de paramètre de mise à l’échelle automatique sur la base d’une mesure qui dépasse un seuil.
- **Archiver** les mesures plus longtemps ou les utiliser pour les rapports en mode hors connexion. Vous pouvez acheminer vos mesures de stockage d’objet Blob lorsque vous configurez les paramètres de diagnostic pour votre ressource.
- **Diffuser** des mesures vers un hub d’événements, pour les acheminer vers Azure Stream Analytics ou des applications personnalisées pour une analyse en quasi temps réel. Vous pouvez définir les paramètres de diagnostic.
- **Acheminer** toutes les mesures vers OMS Log Analytics pour obtenir instantanément des analyses, la recherche et des alertes personnalisées sur les données de mesure de vos ressources.
- **Utiliser** les mesures via les nouvelles API REST Azure Monitor.
- **Interroger** les mesures en utilisant les applets de commande PowerShell ou l’API REST multiplateforme.

 ![Acheminement des mesures dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview0.png)

## Accès aux mesures via le portail
Voici un bref aperçu de la création d’un graphique des mesures via le portail Azure

### Affichage des mesures après la création d’une ressource
1. Ouvrez le portail Azure
2. Créez un Service d'application - Site web.
3. Après avoir créé un site web, accédez au panneau Vue d’ensemble du site web.
4. Vous pouvez afficher les nouvelles mesures sous forme de vignette « Surveillance ». Vous pouvez modifier la vignette et sélectionner d’autres mesures

 ![Mesures sur une ressource dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### Accéder à toutes les mesures en un seul emplacement
1. Ouvrez le portail Azure
2. Accédez au nouvel onglet Monitor et sélectionnez l’option Mesures dans cette section
3. Vous pouvez sélectionner votre abonnement, le groupe de ressources et le nom de la ressource dans la liste déroulante.
4. Vous pouvez maintenant afficher la liste des mesures disponibles.
5. Sélectionnez la mesure qui vous intéresse et tracez.
6. Vous pouvez l’épingler au tableau de bord en cliquant sur l’épingle dans le coin supérieur droit.

 ![Accéder à toutes les mesures dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)


>[AZURE.NOTE] Vous pouvez accéder aux mesures à partir de machines virtuelles (basées sur Azure Resource Manager) et aux jeux de mise à l’échelle de machine virtuelle sans aucune installation de diagnostic supplémentaire. Ces nouvelles mesures au niveau hôte sont disponibles pour les instances Windows et Linux. Ces mesures ne doivent pas être confondues avec les mesures de niveau système d’exploitation invité auxquelles vous avez accès lorsque vous activez les Diagnostics Azure sur vos machines virtuelles ou VMSS. Pour en savoir plus sur la configuration d’Azure Diagnostics, consultez [Qu’est-ce que Microsoft Azure Diagnostics](../azure-diagnostics.md).

## Accès aux mesures via l’API REST
Les mesures Azure sont accessibles via les API Azure Monitor. Il existe deux API qui vous aident à découvrir et accéder aux mesures. Utilisez le :

- [L’API REST de définitions de mesures Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) pour accéder à la liste des mesures disponibles pour un service.
- [L’API REST Azure Monitor Metrics](https://msdn.microsoft.com/library/mt743622.aspx) pour accéder aux données de mesure elles-mêmes

>[AZURE.NOTE] Cet article décrit les mesures via la [nouvelle API pour les mesures](https://msdn.microsoft.com/library/dn931930.aspx) pour les ressources Azure. La version de l’API pour les nouvelles définitions de mesures est 2016-03-01 et la version pour les mesures est 2016-09-01. Les définitions de mesures et les mesures sont accessibles avec la version 2014-04-01 de l’API.

Pour une description plus détaillée à l’aide de l’API REST d’Azure Monitor, consultez [Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor](monitoring-rest-api-walkthrough.md).

## Options d’exportation pour les mesures
Vous pouvez accéder au panneau Journaux de diagnostic sous l’onglet Monitor et afficher les options d’exportation pour les mesures. Vous pouvez sélectionner les mesures (et les journaux de diagnostic) à acheminer vers un stockage d’objets Blob, des hubs d’événements ou pour OMS Log Analytics pour les cas d’utilisation mentionnés précédemment dans cet article.

 ![Options d’exportation pour les mesures dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Vous pouvez configurer cela via les modèles Resource Manager, [PowerShell](insights-powershell-samples.md), [le CLI](insights-cli-samples.md) ou [les API REST](https://msdn.microsoft.com/library/dn931943.aspx).

## Effectuer une opération sur les mesures
Vous pouvez recevoir des notifications ou des actions automatisées sur les données de mesure. Vous pouvez configurer des règles d’alerte ou des paramètres de mise à l’échelle pour le faire.

### Règles d'alerte
Vous pouvez configurer des règles d’alerte sur les mesures. Ces règles d’alerte peuvent vérifier si une mesure a atteint un certain seuil et vous envoyer une notification par e-mail ou déclencher un webhook qui peut ensuite être utilisé pour exécuter tout script personnalisé. Vous pouvez également utiliser le webhook pour configurer les intégrations de produits tiers.

 ![Mesures et règles d’alerte dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

### Autoscale
Certaines ressources Azure prennent en l’augmentation ou la diminution de la taille de plusieurs instances pour gérer vos charges de travail. La mise à l’échelle automatique s’applique à App Services (applications web), aux jeux de mise à l’échelle de machine virtuelle (VMSS) et aux services cloud classiques. Vous pouvez configurer les règles de mise à l’échelle automatique pour augmenter la taille des instances lorsqu’une certaine mesure affectant votre charge de travail dépasse un seuil spécifié. Pour plus d'informations, consultez la rubrique [Présentation de la mise à l’échelle automatique](monitoring-overview-autoscale.md).

 ![Mesures et règles de mise à l’échelle automatique dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## Services et mesures pris en charge
Azure Monitor est une nouvelle infrastructure de mesures. Il propose la prise en charge des services Azure suivants dans le portail Azure et la nouvelle version de l’API Azure Monitor :

- Machines virtuelles (basées sur Azure Resource Manager)
- Jeux de mise à l’échelle de machine virtuelle
- Batch
- Espace de noms de hub d’événements
- Espace de noms de Service Bus (référence SKU premium uniquement)
- SQL (version 12)
- Pool élastique SQL
- Microsoft Azure
- Batteries de serveurs web
- Logic Apps
- IoT Hubs
- Cache Redis
- Réseau : Passerelles d’application
- Search

Vous pouvez voir une liste détaillée de tous les services pris en charge et leurs mesures dans [Mesures d’Azure Monitor - Mesures prises en charge par type de ressource](monitoring-supported-metrics.md).


## Étapes suivantes

Consultez les liens dans cet article. De plus, consultez :

- à propos des [mesures courantes pour la mise à l’échelle automatique](insights-autoscale-common-metrics.md)
- comment [créer des règles d’alerte](insights-alerts-portal.md)

<!---HONumber=AcomDC_0928_2016-->