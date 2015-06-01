<properties 
	pageTitle="Comment mettre à l'échelle une application web dans un environnement App Service" 
	description="Mise à l'échelle d'une application web dans un environnement App Service" 
	services="app-services\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Mise à l'échelle des applications web dans un environnement App Service #

À un niveau élevé, les environnements App Service sont essentiellement des déploiements personnels d'Azure App Service sur votre réseau virtuel et ne peuvent être gérés que par votre abonnement. Ils offrent de nouvelles fonctionnalités réseau, car ils sont dans votre réseau virtuel et peuvent également être mis à l'échelle au-delà de ce qui est normalement disponible dans les environnements Azure App Service. Pour plus d'informations sur ce qu'est un environnement App Service, consultez [Présentation d'un environnement App Service][WhatisASE]. Pour plus d'informations sur la création d'un environnement App Service ou la création d'une application web dans un environnement App Service, consultez [Comment créer un environnement App Service][HowtoCreateASE] et [Comment créer une application web dans un environnement App Service][CreateWebappinASE]

À titre de bref rappel, lorsque, normalement, vous modifiez l'attribut d'échelle d'une application web, vous le modifiez au niveau du plan App Service. Pour plus d'informations sur la mise à l'échelle des plans App Service ou sur les seuls plans App Service en dehors des environnements App Service, consultez [Mettre à l'échelle une application web dans Azure App Service][ScaleWebapp] et [Présentation détaillée des plans App Service][Appserviceplans].

La mise à l'échelle d'une application web dans un environnement App Service est très similaire à celle des applications web dans un environnement normal. Dans Azure App Service, il existe normalement trois éléments que vous pouvez mettre à l'échelle :

- Plan de tarification
- Taille du travail (pour les instances dédiées)
- Nombre d'instances

Dans un environnement App Service, il est inutile de sélectionner ou de modifier le plan de tarification. En termes de fonctionnalités, il est déjà au niveau de tarification Premium. Dans un environnement App Service, il n'existe pas non plus de travaux partagés. Ce sont tous des travaux dédiés. À la place de tailles fixes, l'administrateur de l'environnement App Service peut affecter la taille de la ressource de calcul à utiliser pour chaque pool de travaux. Cela signifie que vous pouvez avoir le pool de travaux 1 avec les ressources de calcul P4 et le pool de travaux 2 avec les ressources de calcul P1, si vous le souhaitez. Ils n'ont pas à être par ordre de taille. Pour plus d'informations sur les tailles et leurs tarifications, consultez le document [Tarification d'Azure App Service][AppServicePricing]. Les options de mise à l'échelle des applications web et des plans App Service d'un environnement App Service sont alors les suivantes :

- Sélection du pool de travaux
- Nombre d'instances

La modification de l'un ou l'autre élément s'effectue via l'interface utilisateur appropriée affichée avec votre plan App Service.

![][1]

### Mise à l'échelle du nombre d'instances ###

Lorsque vous créez votre application web dans un environnement App Service, vous devez la monter en puissance jusqu'à 2 instances au moins pour fournir une tolérance de panne.

Si votre environnement Application Service a une capacité suffisante, l'opération est assez simple. Vous accédez à votre plan App Service qui contient les sites que vous souhaitez mettre à l'échelle et vous sélectionnez Mettre à l'échelle. Dans l'interface utilisateur qui s'affiche, vous faites simplement glisser l'indicateur d'instance jusqu'à la valeur souhaitée et enregistrez celle-ci.

![][2]

Vous ne pourrez pas mettre à l'échelle votre plan App Service au-delà du nombre de ressources de calcul disponibles du pool de travaux où se trouve votre plan App Service. Si besoin est, vous devez demander à l'administrateur de votre environnement App Service d'ajouter les ressources de calcul nécessaires au pool de travaux. Pour plus d'informations sur la reconfiguration de votre environnement App Service, consultez [Comment configurer un environnement App Service][HowtoConfigureASE]
 

### Sélection du pool de travaux ###

La sélection du pool de travaux s'effectue à partir de l'interface utilisateur du plan App Service. Ouvrez le plan App Service que vous souhaitez mettre à l'échelle et sélectionnez le pool de travaux. Tous les pools de travaux que vous avez configurés dans votre environnement App Service s'affichent. Si vous n'avez qu'un pool de travaux, lui seul apparaît. Pour modifier le pool de travaux dans lequel se trouve votre plan App Service, il suffit de sélectionner le pool de travaux vers lequel vous souhaitez déplacer votre plan App Service.

![][3]

Au préalable, il importe que vous vous assuriez d'avoir une capacité suffisante pour votre plan App Service. Dans la liste des pools de travaux, non seulement figure le nom du pool de travaux, mais également le nombre de travaux disponibles dans ce pool. Veillez à ce qu'il y ait suffisamment d'instances disponibles pour contenir votre plan App Service. Si vous avez besoin de plus de ressources de calcul dans le pool de travaux vers lequel vous souhaitez déplacer votre plan, demandez à l'administrateur de votre environnement App Service de les ajouter.

Le déplacement d'une application web d'un pool de travaux entraîne le redémarrage de vos applications web. Il peut en résulter une interruption de votre application dont la durée varie en fonction du temps nécessaire au redémarrage.

## Prise en main

Pour commencer à utiliser les environnements App Service, consultez [Comment créer un environnement App Service][HowtoCreateASE]

Pour plus d'informations sur la plateforme Azure App Service, consultez [Azure App Service][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-app-service-environment/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
<!--HONumber=52-->
