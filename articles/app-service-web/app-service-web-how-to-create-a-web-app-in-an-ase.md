<properties
	pageTitle="Comment créer une application web dans un environnement App Service"
	description="Flux de création pour les applications web et les plans App Service examinés d'un environnement App Service"
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# Comment créer une application web dans un environnement App Service #

La création d'applications web est pratiquement identique dans un environnement App Service qu'elle l'est dans un environnement normal. Si vous connaissez mal les fonctionnalités d'un environnement de service d'applications, lisez le document[Présentation d'un environnement App Service](app-service-app-service-environment-intro.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Pour créer une application web dans un environnement App Service, vous devez commencer par en avoir un. Pour plus d'informations sur la création d'un environnement App Service, consultez [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

La première étape pour créer une application web consiste à créer ou à sélectionner un plan App Service. La création d'un plan App Service dans un environnement App Service commence normalement, sous forme d'un flux de création d'application web via Nouveau -> Web + Mobile -> Application web.

![][1]


Si vous utilisez un plan App Service que vous avez déjà créé dans votre environnement App Service, choisissez le plan, entrez le nom de votre application web, puis sélectionnez Créer. Le flux est identique à celui que vous obtenez normalement lors de la création d'une application web. La principale différence est que votre application web est accessible à l'adresse suivante :

[*nom du site*].[*nom de votre environnement App Service*].p.azurewebsites.net

et non à l'adresse

[*nom du site*]. azurewebsites.net

À ce stade, le nom de votre application web doit être unique dans la totalité d'Azure App Service. Cela signifie que si vous souhaitez créer une application web nommée « monappweb », il ne doit pas y avoir d'autre application web nommée « monappweb » dans Azure App Service.

### Plans App Service ###

Les plans App Service sont un ensemble géré de vos applications web. Lorsque vous sélectionnez la tarification, le prix facturé s'applique au plan App Service et non aux applications individuelles. Pour augmenter le nombre d'instances d'une application web, vous augmentez la taille des instances de votre plan App Service et toutes les applications web du plan sont affectées. Certaines fonctionnalités, telles que les emplacements de site ou l'intégration de réseau virtuel, présentent aussi des restrictions de quantité à l'intérieur du plan. Pour plus d'informations sur les plans App Service, consultez le document : [Plans Azure App Service détaillés](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

La création d'un plan App Service présente quelques différences avec la création d'un plan App Service dans un environnement App Service. Entre autres choses, vos choix de travaux sont différents, car il n'existe pas de travaux partagés dans un environnement App Service. Les travaux que vous devez utiliser sont ceux qui ont été alloués à l'environnement App Service par l'administrateur. Cela signifie que pour créer un nouveau plan App Service, le nombre de travaux alloués à votre environnement App Service doit être supérieur au nombre total d'instances à travers l'ensemble de vos plans App Service de votre environnement App Service. Si vous n'avez pas suffisamment de travaux dans votre environnement App Service pour créer votre plan App Service, demandez à l'administrateur de votre environnement App Service de les ajouter.

L'absence du choix de tarification constitue une autre différence avec les plans App Service hébergés de l'environnement App Service. Lorsque vous avez un environnement App Service, vous payez les ressources de calcul utilisées par le système et n'avez pas de frais supplémentaires pour les plans App Service de cet environnement. Normalement lorsque vous créez un plan App Service, vous sélectionnez un plan de tarification qui détermine votre facturation. Un environnement App Service est essentiellement un emplacement privé où vous pouvez créer un contenu. Vous payez l'environnement et non l'hébergement de votre contenu.

### Sélection de votre environnement App Service ###

Un environnement App Service étant essentiellement un emplacement de déploiement privé, commencez par sélectionner l'environnement App Service que vous souhaitez utiliser à l'aide de votre sélecteur d'emplacements.

![][2]

Après la sélection, l'interface utilisateur met à jour le sélecteur de plan de tarification et le remplace par un sélecteur de pool de travaux. L'emplacement affiche le nom du système de l'environnement App Service et de la région dans laquelle il se trouve. Sous l'URL, le nom de domaine de l'environnement App Service remplace l'actuel azurewebsites.net par le nom de l'environnement App Service.

![][3]

### Sélection de votre pool de travaux ###

Normalement, dans Azure App Service et en dehors d'un environnement App Service, il existe 3 tailles disponibles avec la sélection d'un plan de tarification dédié. De la même manière, les clients qui possèdent un environnement App Service peuvent définir jusqu'à trois pools de travaux et spécifier la taille de la machine virtuelle utilisée pour ce pool de travaux. Au lieu de sélectionner un plan de tarification pour votre plan App Service, vous sélectionnez un pool de travaux.

L'interface utilisateur de la sélection du pool de travaux affiche la taille des machines virtuelles utilisées pour le pool de travaux sous le nom. La quantité disponible fait référence au nombre de machines virtuelles disponibles pour une utilisation dans le pool. Le pool total peut avoir plus de machines virtuelles que ce nombre, mais la valeur fait simplement référence à ceux qui ne sont pas en cours d'utilisation. Si vous avez besoin d'ajuster votre environnement App Service pour ajouter des ressources de calcul, consultez le document [Configuration de votre environnement App Service](app-service-web-configure-an-app-service-environment.md).

![][4]

Dans cet exemple, vous constatez que seuls deux pools de travaux sont disponibles. La raison en est que l'administrateur de l'environnement App Service n'a alloué les machines virtuelles qu'à ces deux pools de travaux. Le troisième apparaît lorsque des machines virtuelles lui ont été attribuées.

### Après la création d'une application web ###

Il existe quelques considérations à prendre en compte quant à l'exécutions d'applications web et à la gestion de plans App Service dans un environnement App Service.

Comme indiqué précédemment, le propriétaire de l'environnement App Service est responsable de la taille du système et, par conséquent, il est également chargé de s'assurer qu'il existe une capacité suffisante pour héberger les plans App Service souhaités. Si aucun travail n'est disponible, vous ne pourrez pas créer votre plan App Service. Il est en de même pour l'augmentation de la taille de votre application web. Si vous avez besoin de davantage d'instances, demandez à l'administrateur de votre environnement App Service d'ajouter plus de travaux.

Après avoir créé votre application web et votre plan App Service, leur mise à l'échelle constitue une bonne idée. Dans un environnement App Service, vous devez toujours avoir au moins 2 instances de votre plan App Service pour fournir une tolérance de panne à vos applications. La mise à l'échelle d'un plan App Service dans un environnement App Service s'effectue normalement, de la même façon que via l'interface utilisateur du plan App Service. Pour plus d'informations sur la mise à l'échelle, consultez le document [Comment mettre à l'échelle une application web dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=Oct15_HO1-->