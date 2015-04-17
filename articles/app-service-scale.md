<properties 
	pageTitle="Mise à l'échelle d'une application web dans Azure App Service" 
	description="Découvrez comment mettre à l'échelle et monter en charge une application web dans Azure App Service, avec notamment la mise à l'échelle automatique." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# Mise à l'échelle d'une application web dans Azure App Service #

Pour améliorer les performances et le débit de vos applications web sur Microsoft Azure et pour activer des fonctionnalités supplémentaires afin de répondre aux besoins de votre entreprise, vous pouvez utiliser le portail de gestion Azure pour mettre à l'échelle votre plan App Service du mode Gratuit au mode Partagé, De base, Standard ou Premium (version préliminaire). 

L'augmentation de la taille des applications web Azure implique deux actions connexes : la modification du mode de votre plan App Service vers un niveau de service plus élevé et la configuration de certains paramètres une fois ce changement de mode effectué. Cet article décrit ces deux étapes. Un niveau de service plus élevé, tel que le mode Standard, offre davantage de robustesse et de souplesse dans le choix d'utilisation des ressources sur Azure.

Vous pouvez facilement modifier les modes et les configurer sous l'onglet Mettre à l'échelle du portail de gestion. Vous pouvez augmenter ou diminuer la taille des instances au besoin. L'application de ces modifications ne prend que quelques secondes et affecte toutes les applications web de votre plan de services. Vous ne devez pas modifier votre code ni redéployer vos applications.

Pour plus d'informations sur les plans App Service (anciennement plans d'hébergement web), consultez [Qu'est-ce qu'un plan d'hébergement App Service ?](web-sites-web-hosting-plan-overview.md) et [Présentation approfondie des plans d'hébergement App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md). Pour plus d'informations sur la tarification et les fonctionnalités de chaque plan App Service, consultez la page [Détails de la tarification - App Service](http://http://azure.microsoft.com/pricing/details/web-sites/).  

> [AZURE.NOTE] Avant de faire passer une application web du mode **Gratuit** au mode **De base** ou **Standard**, vous devez supprimer les limites de dépense mises en place pour votre abonnement Azure App Service. Pour voir ou modifier les options de votre abonnement Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Mise à l'échelle vers le mode Partagé ou De base du plan App Service
<!-- ===================================== -->

1. Dans votre navigateur, ouvrez le [portail Azure][portal].
	
2. Dans le panneau de votre application web, cliquez sur **Tous les paramètres**, sur **Mettre à l'échelle**, puis sur **Mettre à niveau un plan Gratuit pour ajouter des instances et obtenir de meilleures performances**.
	
	![Choose Hosting Plan][ChooseWHP]
	
4. Dans le panneau **Choisir un niveau de tarification**, choisissez un mode **Partagé** ou **De base**, puis cliquez sur **Sélectionner**.

	Dans l'onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l'opération terminée. 
	
5. Faites glisser la barre **Instance** de gauche à droite pour augmenter le nombre d'instances, puis cliquez sur **Enregistrer** dans la barre de commandes. L'option de taille des instances n'est pas disponible en mode **Partagé**. Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et de services cloud pour Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	Dans l'onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l'opération terminée. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Mise à l'échelle vers le mode Standard du plan App Service
<!-- ================================= -->

> [AZURE.NOTE] Avant de faire passer un plan App Service du mode **Gratuit** au mode **Standard**, vous devez supprimer les limites de dépense mises en place pour votre abonnement Microsoft Azure App Service. Dans le cas contraire, votre site risque de devenir inaccessible si vous atteignez la limite avant la fin de la période de facturation. Pour voir ou modifier les options de votre abonnement Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].

1. Pour passer au mode **Standard**, suivez les mêmes étapes initiales que pour passer au mode **Partagé** ou **De base**, puis choisissez un mode **Standard** dans **Choisir un niveau de tarification** et cliquez sur **Sélectionner**. 
	
	Dans l'onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l'opération terminée, et le **Mode de mise à l'échelle automatique** est activé.

	![Scale in Standard Mode][ScaleStandard]

	Vous pouvez encore faire glisser la barre **Instance** pour configurer manuellement plus d'instances, tout comme dans le mode **De base** ci-dessus. Toutefois, vous apprendrez ici à utiliser le **Mode de mise à l'échelle automatique**. 

2. Dans le **Mode de mise à l'échelle automatique**, sélectionnez **Performances** pour effectuer une mise à l'échelle automatique basée sur les mesures de performances.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. Dans **Plage d'instances**, déplacez les deux curseurs pour définir le nombre minimal et le nombre maximal d'instances à mettre à l'échelle automatiquement pour le plan App Service. Pour ce didacticiel, placez le curseur maximal sur **6** instances.

4. Cliquez sur **Enregistrer** dans la barre de commandes.

4. Sous **Mesures cibles**, cliquez sur **>** pour configurer les règles de mise à l'échelle automatique de la mesure par défaut.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Vous pouvez configurer des règles de mise à l'échelle automatique pour différentes métriques de performances, y compris le processeur, la mémoire, la file d'attente du disque, la file d'attente HTTP et le flux de données. Ici, vous allez configurer une mise à l'échelle automatique pour le pourcentage UC qui effectue les opérations suivantes :

	- Augmentation de 1 instance si l'utilisation de l'UC dépasse 70 % au cours des 10 dernières minutes
	- Augmentation de 3 instances si l'utilisation de l'UC dépasse 90 % au cours des 5 dernières minutes
	- Diminution de 1 instance si l'utilisation de l'UC est inférieure à 50 % au cours des 30 dernières minutes 


4. Laissez la liste déroulante **Métrique** définie sur **Pourcentage UC**.
	
5. Dans **Règles d'extension**, configurez la première règle en définissant **Condition** sur **Plus**, **Seuil** sur **70** (%), **Au cours de** sur **10** (minutes), **Augmentation de** sur **1** (instance) et **Délai d'attente** sur **10** (minutes). 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] Le paramètre **Délai d'attente** spécifie le temps que cette règle doit attendre après une mise à l'échelle pour pouvoir effectuer une nouvelle mise à l'échelle.
	
6. Cliquez sur **Ajouter une règle d'extension**, puis configurez la deuxième règle en définissant **Condition** sur **Plus**, **Seuil** sur **90** (%), **Au cours de** sur **1** (minute), **Augmentation de** sur **3** (instances) et **Délai d'attente** sur **1** (minute).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. Dans **Règles de réduction**, configurez la troisième règle en définissant **Condition** sur **Moins**, **Seuil** sur **50** (%), **Au cours de** sur **30** (minutes), **Réduction de** sur **1** (instance) et **Délai d'attente** sur **60** (minutes). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Cliquez sur **Enregistrer** dans la barre de commandes. Votre règle de mise à l'échelle automatique doit maintenant être répercutée dans le panneau **Mettre à l'échelle**. 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
##Mise à l'échelle d'une base de données SQL Server connectée à votre application web
Si vous disposez d'une ou de plusieurs bases de données SQL Server liées à votre application web (quel que soit le mode de plan App Service utilisé), vous pouvez rapidement les mettre à l'échelle selon vos besoins.

1. Pour mettre à l'échelle l'une des bases de données liées, ouvrez le panneau de votre application web dans le [portail Azure][portal]. Dans la liste déroulante réductible **Essentials**, cliquez sur le lien **Groupe de ressources**. Ensuite, dans la partie **Résumé** du panneau de groupe de ressources, cliquez sur l'une des bases de données liées.
	
	![Linked database][ResourceGroup]
	
2. Dans le panneau de votre base de données SQL liée, cliquez sur la partie **Niveau de tarification**, sélectionnez l'un des niveaux en fonction des performances dont vous avez besoin, puis cliquez sur **Sélectionner**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. Vous pouvez également configurer une géo-réplication pour augmenter les capacités de récupération d'urgence et de haute disponibilité de votre base de données SQL. Pour ce faire, cliquez sur la partie **Géo-réplication**.

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Fonctionnalités pour développeur
Selon le mode du plan de service de l'application web, les fonctionnalités orientées développeur suivantes sont disponibles :

### Nombre de bits ###

- Les modes de plan De base et Standard prennent en charge des applications 32 bits et 64 bits.
- Les modes de plan Gratuit et Partagé prennent uniquement en charge des applications 32 bits.

### Prise en charge du débogueur ###

- La prise en charge du débogueur est disponible pour les modes Gratuit, Partagé et De base du plan App Service et permet une connexion simultanée par application.
- La prise en charge du débogueur est disponible pour le mode Standard du plan App Service et permet cinq connexions simultanées par application.

<a name="OtherFeatures"></a>
## Autres fonctionnalités

### Surveillance de point de terminaison Web ###

- La surveillance de point de terminaison web est disponible dans les modes De base et Standard du plan App Service. Pour plus d'informations sur la surveillance des points de terminaison web, consultez la page [Surveillance des applications web](web-sites-monitor.md).

- Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans App Service, notamment la tarification et les fonctionnalités présentant de l'intérêt pour tous les utilisateurs (notamment les développeurs), consultez la page [Détails de la tarification - App Service](http://azure.microsoft.com/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Étapes suivantes

- Pour prendre en main Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

- Pour plus d'informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.
	
	[Détails de la tarification - Transferts de données](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Plans de support Microsoft Azure](http://azure.microsoft.com/support/plans/)
	
	[Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/)
	
	[Détails de la tarification - Base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Tailles de machines virtuelles et services cloud pour Microsoft Azure][vmsizes]
	
	[Détails de la tarification - App Service](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[Détails de la tarification - App Service - Connexions SSL](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- Pour plus d'informations sur les meilleures pratiques liées à Azure App Service, notamment la création d'une architecture extensible et résiliente, consultez le blog sur les [meilleures pratiques liées à Microsoft Azure Web Sites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vidéos sur la mise à l'échelle des sites web Azure :
	
	[Quand mettre à l'échelle des sites web Azure - avec Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Mise à l'échelle automatique des sites web Azure, unité centrale ou planification - avec Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Mise à l'échelle des sites web Azure - avec Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->