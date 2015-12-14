<properties 
	pageTitle="Mise à l’échelle d’une application web dans Microsoft Azure App Service" 
	description="Apprenez à mettre à l’échelle une application web dans Azure App Service, notamment avec la fonction d’ajustement automatique." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Mise à l’échelle d’une application web dans Microsoft Azure App Service #

Si vous souhaitez améliorer les performances et le débit de vos applications web sur Microsoft Azure, vous pouvez utiliser le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pour faire évoluer votre plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) du mode **Gratuit** vers le mode **Partagé**, **De base**, **Standard** ou **Premium**.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

La montée en puissance des applications web Azure implique deux actions associées : évolution du mode de votre plan App Service vers un niveau de service supérieur et configuration de certains paramètres après le passage à ce niveau de service supérieur. Cet article décrit ces deux étapes. Les niveaux de service supérieurs comme les modes **Standard** et **Premium** augmentent la robustesse et la souplesse dont vous disposez pour déterminer le mode d’utilisation de vos ressources sur Azure.

Ces paramètres de mise à l’échelle sont applicables en quelques secondes et affectent toutes les applications web de votre plan App Service. Vous ne devez pas modifier votre code ni redéployer vos applications.

Pour plus d’informations sur les plans App Service, consultez les pages [Qu’est-ce qu’un plan App Service ?](../web-sites-web-hosting-plan-overview.md) et [Présentation approfondie des plans Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Pour plus d’informations sur la tarification et les fonctionnalités de chaque plan App Service, consultez la page [Détails sur la tarification d’App Service](/pricing/details/web-sites/).

> [AZURE.NOTE]Avant de faire évoluer une application web du mode **Gratuit** vers le mode **De base**, **Standard** ou **Premium**, commencez par supprimer le plafond de dépenses mis en place pour votre abonnement Azure App Service. Pour voir ou modifier les options de votre abonnement Microsoft Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Évolution vers le mode Partagé ou De base
<!-- ===================================== -->

1. Dans votre navigateur, ouvrez le [portail Azure][portal].
	
2. Dans le panneau de votre application web, cliquez sur **Tous les paramètres**, sur **Mettre à l’échelle**, puis sur **Mettre à niveau un plan Gratuit pour ajouter des instances et obtenir de meilleures performances**.
	
	![Choisir un plan][ChooseWHP]
	
4. Dans le panneau **Choisir un niveau de tarification**, choisissez un mode **Partagé** ou **De base**, puis cliquez sur **Sélectionner**.
	
	Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée.
	
5. Faites glisser la barre **Instance** de gauche à droite pour augmenter le nombre d’instances, puis cliquez sur **Enregistrer** dans la barre de commandes. L’option de taille des instances n’est pas disponible en mode **Partagé**. Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure][vmsizes].
	
	![Taille des instances en mode De base][ChooseBasicInstances]
	
	Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée.
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Évolution vers le mode Standard ou Premium
<!-- ================================= -->

> [AZURE.NOTE]Avant de faire passer un plan App Service en mode **Standard** ou **Premium**, commencez par supprimer le plafond de dépenses mis en place pour votre abonnement Microsoft Azure App Service. Dans le cas contraire, votre application web risque de devenir inaccessible si vous atteignez ce plafond avant la fin de la période de facturation. Pour voir ou modifier les options de votre abonnement Microsoft Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].

1. Pour évoluer vers le mode **Standard** ou **Premium**, suivez les mêmes étapes initiales que pour le passage au mode **Partagé** ou **De base**, choisissez un mode **Standard** ou **Premium** dans **Choisir votre niveau de tarification**, puis cliquez sur **Sélectionner**. 
	
	Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée, et le **Mode de mise à l’échelle automatique** est activé.
	
	![Évoluer vers le mode Standard ou Premium][ScaleStandard]
	
	Vous pouvez encore faire glisser la barre **Instance** pour configurer manuellement plus d’instances, comme dans le mode **De base** ci-dessus. Toutefois, vous apprendrez ici à utiliser le **Mode de mise à l’échelle automatique**.
	
2. Dans **Mode de mise à l’échelle automatique**, sélectionnez **Performances** pour effectuer une mise à l’échelle automatique en fonction des métriques de performance.
	
	![Mode de mise à l’échelle automatique défini sur Performance][Autoscale]
	
3. Dans **Plage d’instances**, déplacez les deux curseurs pour définir le nombre minimal et le nombre maximal d’instances à mettre à l’échelle automatiquement pour le plan App Service. Pour ce didacticiel, placez le curseur maximal sur **6** instances.
	
4. Cliquez sur **Enregistrer** dans la barre de commandes.
	
4. Sous **Métriques cibles**, cliquez sur **>** pour configurer les règles de mise à l’échelle automatique de la métrique par défaut.
	
	![Définir les métriques cibles][SetTargetMetrics]
	
	Vous pouvez configurer des règles de mise à l'échelle automatique pour différentes métriques de performances, y compris le processeur, la mémoire, la file d'attente du disque, la file d'attente HTTP et le flux de données. Ici, vous allez configurer une mise à l'échelle automatique pour le pourcentage UC qui effectue les opérations suivantes :
	
	- Augmentation de 1 instance si l'utilisation de l'UC dépasse 70 % au cours des 10 dernières minutes
	- Augmentation de 3 instances si l'utilisation de l'UC dépasse 90 % au cours des 5 dernières minutes
	- Diminution de 1 instance si l'utilisation de l'UC est inférieure à 50 % au cours des 30 dernières minutes 
	
	
4. Conservez **Pourcentage UC** dans la liste déroulante **Métrique**.
	
5. Dans **Règles d’extension**, configurez la première règle en réglant **Condition** sur **Plus**, **Seuil** sur **70** (%), **Au cours de** sur **10** (minutes), **Augmentation de** sur **1** (instance) et **Délai d’attente** sur **10** (minutes).
	
	![Définir la première règle de mise à l’échelle automatique][SetFirstRule]
	
	>[AZURE.NOTE]Le paramètre **Délai d’attente** spécifie le temps que cette règle doit attendre après une mise à l’échelle pour pouvoir effectuer une nouvelle mise à l’échelle.
	
6. Cliquez sur **Ajouter une règle d’extension**, puis configurez la deuxième règle en réglant **Condition** sur **Plus**, **Seuil** sur **90** (%), **Au cours de** sur **1** (minute), **Augmentation de** sur **3** (instances) et **Délai d’attente** sur **1** (minute).
	
	![Définir la deuxième règle de mise à l’échelle automatique][SetSecondRule]
	
5. Dans **Règles de réduction**, configurez la troisième règle en réglant **Condition** sur **Moins**, **Seuil** sur **50** (%), **Au cours de** sur **30** (minutes), **Réduction de** sur **1** (instance) et **Délai d’attente** sur **60** (minutes).
	
	![Définir la troisième règle de mise à l’échelle automatique][SetThirdRule]
	
7. Cliquez sur **Enregistrer** dans la barre de commandes. Votre règle de mise à l’échelle automatique doit maintenant être répercutée dans le panneau **Mettre à l’échelle**.
	
	![Définir le résultat de la règle de mise à l’échelle automatique][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Mise à l'échelle d'une base de données SQL Server connectée à votre application web
Si vous disposez d'une ou de plusieurs bases de données SQL Server liées à votre application web (quel que soit le mode de plan App Service utilisé), vous pouvez rapidement les mettre à l'échelle selon vos besoins.

1. Pour faire évoluer l’une des bases de données liées, ouvrez le panneau de votre application web dans le [portail Azure][portal]. Dans la liste déroulante réductible **Essentials**, cliquez sur le lien **Groupe de ressources**. Ensuite, dans la partie **Résumé** du panneau de groupe de ressources, cliquez sur l’une des bases de données liées.

	![Base de données liée][ResourceGroup]
	
2. Dans le panneau de votre base de données SQL liée, cliquez sur la partie **Niveau de tarification**, sélectionnez l’un des niveaux en fonction des performances requises, puis cliquez sur **Sélectionner**.
	
	![Mettre à l’échelle votre base de données SQL][ScaleDatabase]
	
3. Vous pouvez également configurer une géo-réplication pour augmenter les capacités de récupération d'urgence et de haute disponibilité de votre base de données SQL. Pour ce faire, cliquez sur la partie **Géo-réplication**.
	
	![Configurer la géo-réplication pour la base de données SQL][GeoReplication]

<a name="devfeatures"></a>
## Fonctionnalités pour développeur
Selon le mode de l’application web, les fonctionnalités orientées développeur disponibles sont les suivantes :

### Nombre de bits ###

- Les modes **De base**, **Standard** et **Premium** prennent en charge les applications 64 bits et 32 bits.
- Les modes **Gratuit** et **Partagé** ne prennent en charge que les applications 32 bits.

### Prise en charge du débogueur ###

- Une prise en charge du débogueur est disponible en modes **Gratuit**, **Partagé** et **De base** pour 1 connexion simultanée par plan App Service.
- Une prise en charge du débogueur est disponible en modes **Standard** et **Premium** pour 5 connexions simultanées par plan App Service.

<a name="OtherFeatures"></a>
## Autres fonctionnalités

### Surveillance de point de terminaison Web ###

- La surveillance des points de terminaison web est disponible dans les modes **De base**, **Standard** et **Premium**. Pour plus d’informations sur la surveillance des points de terminaison web, consultez la page [Surveillance des applications web](web-sites-monitor.md).

- Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans App Service, notamment la tarification et les fonctionnalités intéressant tous les utilisateurs (y compris les développeurs), consultez la page [Détails sur la tarification d’App Service](/pricing/details/web-sites/).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<a name="Next Steps"></a>
## Étapes suivantes

- Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).
- Pour plus d'informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.
	
	[Détails de la tarification – Transferts de données](/pricing/details/data-transfers/)
	
	[Plans de support Microsoft Azure](/support/plans/)
	
	[Contrats de niveau de service](/support/legal/sla/)
	
	[Tarification – Base de données SQL](/pricing/details/sql-database/)
	
	[Tailles de machines virtuelles et services cloud pour Microsoft Azure][vmsizes]
	
	[Détails de la tarification – App Service](/pricing/details/web-sites/)
	
	[Détails de la tarification – App Service - Connexions SSL](/pricing/details/web-sites/#ssl-connections)

- Pour plus d’informations sur les meilleures pratiques liées à Azure App Service, notamment la création d’une architecture évolutive et résiliente, consultez la page [Meilleures pratiques : Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vidéos sur l’évolution des applications web :
	
	- [Quand mettre à l’échelle Sites Web Azure - avec Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Mise à l’échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Mise à l’échelle de Sites Web Azure - avec Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
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
 

<!---HONumber=AcomDC_1203_2015-->