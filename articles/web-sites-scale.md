<properties 
	pageTitle="Faire évoluer une application web dans Azure App Service" 
	description="Apprenez à effectuer la montée en puissance et l'augmentation de la taille des instances d'une application web dans Azure App Service, et notamment à utiliser la fonction d'ajustement automatique." 
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
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Faire évoluer une application web dans Azure App Service #

Si vous souhaitez améliorer les performances et le débit de vos applications web sur Microsoft Azure, vous pouvez utiliser le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pour faire évoluer votre plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) du mode **Gratuit** vers le mode **Partage**, **De base**, **Standard** ou **Premium**. 

La montée en puissance des applications web Azure implique deux actions associées : évolution du mode de votre plan App Service vers un niveau de service supérieur et configuration de certains paramètres après le passage à ce niveau de service supérieur. Cet article décrit ces deux étapes. Les niveaux de service supérieurs comme les modes **Standard** et **Premium** augmentent la robustesse et la souplesse dont vous disposez pour déterminer la façon dont vos ressources sont utilisées sur Azure.

Vous pouvez facilement modifier les modes et les configurer sous l'onglet Mettre à l'échelle du portail de gestion. Vous pouvez augmenter ou diminuer la taille des instances au besoin. Ces modifications sont applicables en quelques secondes et affectent toutes les applications web de votre plan App Service. Vous ne devez pas modifier votre code ni redéployer vos applications.

Pour plus d'informations sur les plans App Service, voir [Qu'est-ce qu'un plan App Service ?](web-sites-web-hosting-plan-overview.md) et [Vue d'ensemble détaillée des plans Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md). Pour plus d'informations sur la tarification et les fonctionnalités des différents plans App Service, voir [Détails de la tarification - App Service](/pricing/details/web-sites/).  

> [AZURE.NOTE] Avant de faire évoluer une application web du mode **Gratuit** vers le mode **De base**, **Standard** ou **Premium**, commencez par supprimer le plafond de dépenses mis en place pour votre abonnement Azure App Service. Pour visualiser ou modifier les options de votre abonnement Microsoft Azure App Service, voir [Abonnements Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Évolution vers le mode Partagé ou De base
<!-- ===================================== -->

1. Dans votre navigateur, ouvrez le [portail Azure][portal].
	
2. Dans le panneau de votre application web, cliquez sur **Tous les paramètres**, sur **Mettre à l'échelle**, puis sur **Mettre à niveau depuis à un plan Gratuit pour ajouter des instances et améliorer les performances**.
	
	![Choisir un plan][ChooseWHP]
	
4. Dans le panneau **Choisir votre niveau de tarification**, sélectionnez le mode **Partagé** ou **De base**, puis cliquez sur **Sélectionner**.
	
	Une fois l'opération terminée, l'onglet **Notifications** affiche un message **RÉUSSITE** clignotant de couleur verte. 
	
5. Faites glisser la barre **Instance** de la gauche vers la droite pour augmenter le nombre d'instances, puis cliquez sur **Enregistrer** dans la barre de commandes. L'option de taille d'instance n'est pas disponible en mode **Partagé**. Pour plus d'informations sur ces tailles d'instance, voir [Tailles de machines virtuelles et de services cloud pour Microsoft Azure][vmsizes].
	
	![Taille d'instance en mode De base][ChooseBasicInstances]
	
	Une fois l'opération terminée, l'onglet **Notifications** affiche un message **RÉUSSITE** clignotant de couleur verte. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Évolution vers le mode Standard ou Premium
<!-- ================================= -->

> [AZURE.NOTE] Avant de faire passer un plan App Service au mode **Standard** ou **Premium**, commencez par supprimer le plafond de dépenses mis en place pour votre abonnement Microsoft Azure App Service. Dans le cas contraire, votre application web risque de devenir inaccessible si vous atteignez ce plafond avant la fin de la période de facturation. Pour visualiser ou modifier les options de votre abonnement Microsoft Azure App Service, voir [Abonnements Microsoft Azure][azuresubscriptions].

1. Pour évoluer vers le mode **Standard** ou **Premium**, suivez les mêmes étapes initiales que pour le passage au mode **Partagé** ou **De base**, choisissez un mode **Standard** ou **Premium** dans **Choisir votre niveau de tarification**, puis cliquez sur **Sélectionner**. 
	
	Une fois l'opération terminée, l'onglet **Notifications** affiche un message **RÉUSSITE** clignotant de couleur verte, et le **Mode Ajustement automatique** est activé.
	
	![Évoluer vers le mode Standard ou Premium][ScaleStandard]
	
	Vous pouvez toujours faire glisser la barre **Instance** pour augmenter manuellement le nombre d'instances, comme décrit précédemment pour le mode **De base**. Toutefois, vous allez apprendre ici à utiliser le **Mode Ajustement automatique**. 
	
2. En **Mode Ajustement automatique**, sélectionnez **Performances** pour faire reposer l'ajustement automatique sur les métriques de performances.
	
	![Mode Ajustement automatique défini sur Performances][Autoscale]
	
3. Dans **Plage d'instances**, déplacez les deux curseurs pour définir le nombre minimal et maximal d'instances pour l'ajustement automatique du plan App Service. Pour ce didacticiel, positionnez le curseur du nombre maximal sur la valeur **6** instances.
	
4. Cliquez sur **Enregistrer** dans la barre de commandes.
	
4. Sous **Métriques cibles**, cliquez sur **>** pour configurer les règles d'ajustement automatique pour la métrique par défaut.  
	
	![Définir les métriques cibles][SetTargetMetrics]
	
	Vous pouvez configurer les règles d'ajustement automatique pour différentes métriques de performances, telles que l'unité centrale, la mémoire, la file d'attente de disque, la file d'attente HTTP et le flux de données. Ici, vous allez configurer le comportement d'ajustement automatique ci-après pour le pourcentage UC :
	
	- montée en puissance correspondant à 1 instance si le pourcentage UC a dépassé 70 % au cours des 10 dernières minutes ;
	- montée en puissance correspondant à 3 instances si le pourcentage UC a dépassé 90 % au cours des 5 dernières minutes ;
	- descente en puissance correspondant à 1 instance si le pourcentage UC est passé en dessous de 50 % au cours des 30 dernières minutes. 
	
	
4. Laissez la liste déroulante **Métrique** définie sur **Pourcentage UC**.
	
5. Dans **Règles de montée en puissance**, configurez la première règle en définissant **Condition** sur **Supérieur**, **Seuil** sur **70** (%), **Au cours des dernières** sur **10** (minutes), **Augmentation de** sur **1** (instance), et **Finalisation** sur **10** (minutes). 
	
	![Définir la première règle d'ajustement automatique][SetFirstRule]
	
	>[AZURE.NOTE] Le paramètre **Finalisation** indique l'intervalle de temps requis entre deux actions de montée en charge par la règle.
	
6. Cliquez sur **Ajouter une règle de montée en puissance**, puis configurez la deuxième règle en définissant **Condition** sur **Supérieur**, **Seuil** sur **90** (%), **Au cours des dernières** sur **1** (minutes), **Augmentation de** sur **3** (instance) et **Finalisation** sur **1** (minutes).
	
	![Définir la deuxième règle d'ajustement automatique][SetSecondRule]
	
5. Dans **Règles de descente en puissance**, configurez la troisième règle en définissant **Condition** sur **Inférieur**, **Seuil** sur **50** (%), **Au cours des dernières** sur **30** (minutes), **Réduction de** sur **1** (instance) et **Finalisation** sur **60** (minutes). 
	
	![Définir la troisième règle d'ajustement automatique][SetThirdRule]
	
7. Cliquez sur **Enregistrer** dans la barre de commandes. Votre règle d'ajustement automatique doit à présent apparaître dans le panneau **Mettre à l'échelle**. 
	
	![Définir le résultat de règle d'ajustement automatique][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Évolution d'une base de données SQL Server connectée à votre application web
Si vous disposez d'une ou de plusieurs bases de données SQL Server liées à votre application web (quel que soit le mode de plan App Service), vous pouvez les faire évoluer rapidement selon vos besoins.

1. Pour faire évoluer l'une des bases de données liées, ouvrez le panneau de votre application web dans le [portail Azure][portal]. Dans la liste déroulante **Informations de base**, cliquez sur le lien **Groupe de ressources**. Puis, dans la partie **Résumé** du panneau du groupe de ressources, cliquez sur l'une des bases de données liées.

	![Base de données liée][ResourceGroup]
	
2. Dans le panneau de votre base de données SQL liée, cliquez sur la partie **Niveau de tarification**, sélectionnez l'un des niveaux en fonction de vos exigences en matière de performances, puis cliquez sur **Sélectionner**. 
	
	![Faire évoluer votre base de données SQL][ScaleDatabase]
	
3. Vous pouvez également configurer la géo-réplication de façon à augmenter les fonctionnalités de haute disponibilité et de récupération d'urgence de votre base de données SQL. Pour effectuer cette opération, cliquez sur la partie **Géo-réplication**.
	
	![Configurer la géo-réplication pour la base de données SQL][GeoReplication]

<a name="devfeatures"></a>
## Fonctionnalités pour développeur
Selon le mode de l'application web, les fonctionnalités orientées développeur disponibles sont les suivantes :

### Nombre de bits ###

- Les modes **De base**, **Standard** et **Premium** prennent en charge les applications 64 bits et 32 bits.
- Les modes **Gratuit** et **Partagé** prennent uniquement en charge les applications 32 bits.

### Prise en charge du débogueur ###

- Une prise en charge du débogueur est disponible en modes **Gratuit**, **Partagé** et **De base** pour 1 connexion simultanée par plan App Service.
- Une prise en charge du débogueur est disponible en modes **Standard** et **Premium** pour 5 connexions simultanées par plan App Service.

<a name="OtherFeatures"></a>
## Autres fonctionnalités

### Surveillance des points de terminaison web ###

- La surveillance des points de terminaison web est disponible en modes **De base**, **Standard** et **Premium**. Pour plus d'informations sur la surveillance des points de terminaison web, voir [Surveillance des applications web](web-sites-monitor.md).

- Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans App Service, notamment concernant la tarification et les fonctionnalités présentant de l'intérêt pour tous les utilisateurs (y compris les développeurs), voir [Détails de la tarification - App Service](/pricing/details/web-sites/).

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

<a name="Next Steps"></a>	
## Étapes suivantes

- Pour la prise en main d'Azure, voir [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).
- Pour plus d'informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.
	
	[Détails de la tarification - Transferts de données](/pricing/details/data-transfers/)
	
	[Plans de support Microsoft Azure](/support/plans/)
	
	[Contrats de niveau de service](/support/legal/sla/)
	
	[Détails de la tarification - Base de données SQL](/pricing/details/sql-database/)
	
	[Tailles de machines virtuelles et de services cloud pour Microsoft Azure][vmsizes]
	
	[Détails de la tarification - App Service](/pricing/details/web-sites/)
	
	[Détails de la tarification - App Service - Connexions SSL](/pricing/details/web-sites/#ssl-connections)

- Pour plus d'informations sur les meilleures pratiques liées à Azure App Service, notamment sur la création d'une architecture évolutive et résistante, voir [Meilleures pratiques : Azure App Service Web Apps (en anglais)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vidéos sur l'évolution des applications web :
	
	- [Quand faire évoluer Sites Web Azure - avec Stefan Schackow (en anglais)](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Ajustement automatique de Sites Web Azure basée sur l'unité centrale ou planifiée - avec Stefan Schackow (en anglais)](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Procédure d'évolution de Sites Web Azure - avec Stefan Schackow (en anglais)](/documentation/videos/how-azure-web-sites-scale/)

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portail]: https://portal.azure.com/

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