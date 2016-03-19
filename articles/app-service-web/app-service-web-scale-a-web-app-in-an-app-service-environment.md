<properties 
	pageTitle="Comment mettre à l'échelle une application dans un environnement App Service" 
	description="Mise à l'échelle d'une application dans un environnement App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# Mise à l'échelle des applications dans un environnement App Service #

À un niveau élevé, les environnements App Service sont essentiellement des déploiements personnels d'Azure App Service sur votre réseau virtuel et ne peuvent être gérés que par votre abonnement. Ils offrent de nouvelles fonctionnalités réseau, car ils sont dans votre réseau virtuel et peuvent également être mis à l'échelle au-delà de ce qui est normalement disponible dans les environnements Azure App Service. Pour plus d'informations sur ce qu'est un environnement App Service, consultez [Présentation d'un environnement App Service][WhatisASE]. Pour plus d'informations sur la création d'un environnement App Service ou la création d'une application web dans un environnement App Service, consultez [Comment créer un environnement App Service][HowtoCreateASE] et [Comment créer une application web dans un environnement App Service][CreateWebappinASE]

À titre de bref rappel, lorsque, normalement, vous modifiez l'attribut d'échelle d'une application web, mobile ou API dans Azure App Service, vous le modifiez au niveau du plan App Service (ASP). Pour plus d'informations sur la mise à l'échelle des plans App Service ou sur les seuls plans App Service en dehors des environnements App Service, consultez [Mettre à l'échelle une application web dans Azure App Service][ScaleWebapp] et [Présentation détaillée des plans App Service][Appserviceplans].

La mise à l'échelle d'une application dans un environnement App Service est très similaire à celle des applications dans un environnement normal. Dans Azure App Service, il existe normalement trois éléments que vous pouvez mettre à l'échelle :

- Plan de tarification
- Taille du travail (pour les instances dédiées)
- Nombre d'instances

Dans un environnement App Service, il est inutile de sélectionner ou de modifier le plan de tarification. En termes de fonctionnalités, il est déjà au niveau de tarification Premium. Dans un environnement App Service, il n'existe pas non plus de travaux partagés. Ce sont tous des travaux dédiés.

En ce qui concerne les tailles de travaux, l'administrateur de l'environnement App Service peut affecter la taille de la ressource de calcul à utiliser pour chaque pool de travaux. Cela signifie que vous pouvez avoir le pool de travaux 1 avec les ressources de calcul P4 et le pool de travaux 2 avec les ressources de calcul P1, si vous le souhaitez. Ils n'ont pas à être par ordre de taille. Pour plus d'informations sur les tailles et leurs tarifications, consultez le document [Tarification d'Azure App Service][AppServicePricing]. Les options de mise à l'échelle des applications web et des plans App Service d'un environnement App Service sont alors les suivantes :

- Sélection du pool de travaux
- Nombre d'instances

La modification de l'un ou l'autre élément s'effectue via l'interface utilisateur appropriée pour vos plans App Service hébergés par ASE. Vous ne pourrez pas mettre à l'échelle votre ASP au-delà du nombre de ressources de calcul disponibles du pool de travaux où se trouve votre ASP. Si besoin est, vous devez demander à l'administrateur de votre environnement App Service d'ajouter les ressources de calcul nécessaires au pool de travaux. Pour plus d'informations sur la reconfiguration de votre environnement App Service, consultez [Comment configurer un environnement App Service][HowtoConfigureASE]. Vous pouvez également tirer parti des fonctionnalités de mise à l'échelle automatique d'ASE pour ajouter de la capacité en fonction de la planification ou des mesures. Pour obtenir des informations sur la configuration de mise à l'échelle automatique de l'environnement ASE lui-même, consultez [Configuration de la mise à l'échelle automatique pour un environnement App Service][ASEAutoscale].

![][1]

### Mise à l'échelle du nombre d'instances ###

Lorsque vous créez votre application web dans un environnement App Service, vous devez la monter en puissance jusqu'à 2 instances au moins pour fournir une tolérance de panne.

Si votre environnement Application Service a une capacité suffisante, l'opération est assez simple. Vous accédez à votre plan App Service qui contient les sites que vous souhaitez mettre à l'échelle et vous sélectionnez Mettre à l'échelle. Ceci affiche l'interface utilisateur où vous pouvez définir manuellement l'échelle de votre ASP ou configurer des règles de mise à l'échelle automatique pour votre ASP. Pour mettre à l'échelle manuellement votre application, il vous suffit de définir ***Mise à l'échelle selon*** sur ***un nombre d'instances saisi manuellement***. À partir de là, faites glisser le curseur vers la quantité souhaitée ou entrez la quantité souhaitée dans la zone à côté du curseur.

![][2]

Les règles de mise à l'échelle automatique d'un ASP dans un travail ASE fonctionnent de manière normale. Vous pouvez sélectionner ***Pourcentage UC*** sous ***Mise à l'échelle selon*** et créer des règles de mise à l'échelle automatique de votre ASP basées sur le pourcentage UC ou vous pouvez créer des règles plus complexes à l'aide des ***règles de performances et de planification***. Pour plus d'informations sur la configuration de la mise à l'échelle automatique, utilisez le guide [Mise à l'échelle d'une application dans Azure App Service][AppScale].


### Sélection du pool de travaux ###

Comme indiqué précédemment, la sélection du pool de travaux s'effectue à partir de l'interface utilisateur du plan App Service (ASP). Ouvrez le panneau de l'ASP que vous souhaitez mettre à l'échelle et sélectionnez le pool de travaux. Tous les pools de travaux que vous avez configurés dans votre environnement App Service s'affichent. Si vous n'avez qu'un pool de travaux, lui seul apparaît. Pour modifier le pool de travaux dans lequel se trouve votre ASP, il suffit de sélectionner le pool de travaux vers lequel vous souhaitez déplacer votre plan App Service.

![][3]

Avant de déplacer votre ASP à partir d'un pool de travaux vers un autre, il est important de vous assurer que la capacité est adaptée pour votre ASP. Dans la liste des pools de travaux, non seulement figure le nom du pool de travaux, mais également le nombre de travaux disponibles dans ce pool. Veillez à ce qu'il y ait suffisamment d'instances disponibles pour contenir votre plan App Service. Si vous avez besoin de plus de ressources de calcul dans le pool de travaux vers lequel vous souhaitez déplacer votre plan, demandez à l'administrateur de votre environnement App Service de les ajouter.

> [AZURE.NOTE] Le déplacement d'un ASP d'un pool de travaux entraîne le redémarrage de vos applications dans cet ASP. Il peut en résulter une interruption de votre application dont la durée varie en fonction du temps nécessaire au redémarrage de vos applications.

## Prise en main

Pour prendre en main les environnements App Service, consultez [Comment créer un environnement App Service][HowtoCreateASE].

Pour plus d'informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=AcomDC_0128_2016-->