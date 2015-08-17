<properties 
	pageTitle="Changement de niveau de tarification dans Azure App Service" 
	description="Apprenez à effectuer la mise à l’échelle des applications logiques, API, web et mobiles dans Azure App Service, et notamment la mise à l’échelle automatique." 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="stepsic"/>

# Changement de niveau de tarification dans Azure App Service

Si vous souhaitez améliorer les performances et le débit de vos applications web sur Microsoft Azure, vous pouvez utiliser le [portail Azure](https://portal.azure.com/) pour faire évoluer votre plan App Service du niveau **Gratuit** au mode **Partage**, **De base**, **Standard** ou **Premium**.

Le niveau de service de votre plan App Service est basé sur le [*niveau de tarification* du plan](/pricing/details/app-service/). Les niveaux de tarification supérieurs comme les modes **Standard** et **Premium** augmentent la robustesse et la souplesse dont vous disposez pour déterminer la façon dont vos ressources sont utilisées sur Azure. La modification du niveau de tarification a une incidence sur le nombre de cœurs et la quantité de mémoire de votre service. Elle appelée *montée en puissance* (ou *descente en puissance*).

Outre la montée en puissance du niveau de tarification, vous pouvez augmenter le nombre d’instances du service. On parle alors d’*augmentation* ou de *diminution de la taille des instances*. Consultez l’article sur la [mise à l’échelle manuelle ou automatique du nombre d’instances](../insights-how-to-scale.md) pour en savoir plus sur la *diminution* et *l’augmentation de la taille des instances*.

Pour plus d’informations sur les plans App Service, consultez les pages [Présentation des plans App Service](../web-sites-web-hosting-plan-overview.md) et [Présentation détaillée des plans d’Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md). Pour plus d’informations sur la tarification et les fonctionnalités de chaque plan App Service, consultez la page [Détails de la tarification – App Service](/pricing/details/app-service/).

Pour finir, la mise à l’échelle fonctionne différemment si vous souhaitez utiliser un [environnement App Service](app-service-app-service-environment-intro.md) dédié. Pour plus d’informations, voir l’article [Mise à l’échelle des applications web dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

> [AZURE.NOTE]Avant de passer du mode **Gratuit** au mode **De base**, **Standard** ou **Premium**, commencez par supprimer le plafond de dépenses mis en place pour votre abonnement Azure App Service. Pour voir ou modifier les options de votre abonnement Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Modification du niveau de tarification

1. Dans votre navigateur, ouvrez le [portail Azure][portal] et accédez à l’application que vous souhaitez mettre à l’échelle.
	
2. Dans la partie **Essentials** correspondant à votre application, cliquez sur le lien **Niveau de tarification/plan App Service**.

3. Cliquez sur **Niveau de tarification** pour afficher la liste des niveaux de service disponibles pour votre plan. Chaque niveau est accompagné d’une estimation de prix qui vous donnera une idée du coût moyen pour ce niveau.
	
	![Choisir un plan](./media/app-service-scale/ChoosePricingTier.png)
	
4. Une fois que vous avez choisi votre niveau, cliquez sur **Sélectionner**.
	
	Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée.
 
Vous pouvez également en savoir plus sur les différents niveaux de calcul dans Azure en cliquant [ici](http://go.microsoft.com/fwlink/?LinkId=309169).
	
<a name="ScalingSQLServer"></a>
##Mise à l’échelle des ressources associées
Si votre application dépend d’autres services, tels que SQL ou Storage, vous pouvez également mettre à l’échelle ces derniers selon vos besoins.

1. Dans **Essentials**, cliquez sur le lien **Groupe de ressources**.

2. Ensuite, dans la partie **Résumé** du panneau de groupe de ressources, cliquez sur l’une des bases de données (ou une autre ressource à mettre à l’échelle).

	![Base de données liée](./media/app-service-scale/ResourceGroup.png)
	
3. Dans le panneau de votre ressource liée, cliquez sur la partie **Niveau de tarification**, sélectionnez l’un des niveaux en fonction des performances dont vous avez besoin, puis cliquez sur **Sélectionner**.
	
	![Mettre à l’échelle votre base de données SQL](./media/app-service-scale/ScaleDatabase.png)
	
4. Si votre application utilise Storage, la géo-réplication est automatiquement configurée lorsque vous choisissez un niveau de tarification qui la prend en charge. Pour SQL, vous devez configurer manuellement la géo-réplication pour augmenter les capacités de récupération d’urgence et de haute disponibilité de votre base de données SQL. Pour ce faire, cliquez sur la partie **Géo-réplication**.
	
	![Configurer la géo-réplication pour la base de données SQL](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## Fonctionnalités pour développeur
Selon le niveau de tarification, les fonctionnalités orientées développeur disponibles sont les suivantes :

### Nombre de bits ###

- Les modes **De base**, **Standard** et **Premium** prennent en charge les applications 64 bits et 32 bits.
- Les niveaux de plan **Gratuit** et **Partagé** prennent uniquement en charge des applications 32 bits.

### Prise en charge du débogueur ###

- Une prise en charge du débogueur est disponible en modes **Gratuit**, **Partagé** et **De base** pour 1 connexion simultanée par plan App Service.
- Une prise en charge du débogueur est disponible en modes **Standard** et **Premium** pour 5 connexions simultanées par plan App Service.

<a name="OtherFeatures"></a>
## Autres fonctionnalités

- Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans App Service, notamment concernant la tarification et les fonctionnalités présentant de l’intérêt pour tous les utilisateurs (y compris les développeurs), consultez la page [Détails de la tarification - App Service](/pricing/details/web-sites/).

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
	
	[Détails de la tarification – App Service](/pricing/details/app-service/)
	
	[Détails de la tarification – App Service - Connexions SSL](/pricing/details/web-sites/#ssl-connections)

- Pour plus d’informations sur les meilleures pratiques liées à Azure App Service, notamment la création d’une architecture évolutive et résiliente, consultez la page [Meilleures pratiques : Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vidéos sur l’évolution des applications web :
	
	- [Quand mettre à l’échelle Sites Web Azure - avec Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Mise à l’échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Mise à l’échelle de Sites Web Azure - avec Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=August15_HO6-->