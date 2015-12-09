<properties
	pageTitle="Créer une application web dans un environnement App Service"
	description="Découvrez comment créer des applications web et des plans App Service dans un environnement App Service."
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="10/26/2015"
	ms.author="ccompy"/>

# Créer une application web dans un environnement App Service

## Vue d'ensemble

Ce didacticiel explique comment créer des applications web et des plans App Service dans un [environnement App Service](app-service-app-service-environment-intro.md) (ASE).

> [AZURE.NOTE]Si vous voulez apprendre à créer une application web mais pas spécialement dans un environnement App Service, consultez [Créer une application web .NET](web-sites-dotnet-get-started.md) ou l’un des didacticiels associés pour d’autres langages et infrastructures.

## Configuration requise

Ce didacticiel part du principe que vous avez créé un environnement App Service. Si ce n’est pas encore le cas, consultez [Créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

## Créer une application web

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau > Web + mobile > Application web**. 

	![][1]

2. Sélectionnez votre abonnement.

	Si vous disposez de plusieurs abonnements, n’oubliez pas que pour créer une application dans votre environnement App Service, vous devez utiliser le même abonnement que celui utilisé lors de la création de l’environnement.

3. Sélectionnez ou créez un groupe de ressources.

	Les *groupes de ressources* vous permettent de gérer les ressources Azure connexes en tant qu’unité et sont utiles lors de l’établissement de règles de *contrôle d’accès en fonction du rôle* (RBAC) pour vos applications. Pour plus d’informations, consultez la rubrique [Gestion de vos ressources Azure][ResourceGroups].

4. Sélectionnez ou créez un plan App Service.

	Les *plans App Service* sont des ensembles gérés d’applications web. Lorsque vous sélectionnez la tarification, le prix facturé s’applique au plan App Service et non aux applications individuelles. Pour augmenter le nombre d’instances d’une application web, vous augmentez la taille des instances de votre plan App Service, et toutes les applications web du plan sont affectées. Certaines fonctionnalités, telles que les emplacements de site ou l'intégration de réseau virtuel, présentent aussi des restrictions de quantité à l'intérieur du plan. Pour plus d’informations, consultez [Présentation des plans Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	Vous pouvez identifier les plans App Service dans votre environnement App Service en examinant l’emplacement indiqué sous le nom du plan.

	![][5]

	Si vous souhaitez utiliser un plan App Service existant dans votre environnement App Service, sélectionnez ce plan. Si vous souhaitez créer un plan App Service, consultez la section suivante de ce didacticiel : [Créer un plan App Service dans un environnement App Service](#createplan).

5. Entrez le nom de votre application web, puis cliquez sur **Créer**.

	Le nom de votre application web doit être unique dans Azure App Service. Cela signifie que si vous souhaitez créer une application web nommée « monappweb », il ne doit pas y avoir d’autre application web nommée « monappweb » dans Azure App Service.

	L’URL d’une application web dans un environnement ASE est :

	[*nom du site*].[*nom de votre environnement App Service*].p.azurewebsites.net

	et non à l'adresse

	[*nom du site*]. azurewebsites.net

## <a name="createplan"></a> Créer un plan App Service

Lorsque vous créez un plan App Service dans un environnement App Service, vos choix de travaux sont différents, car il n’existe pas de travaux partagés dans un environnement App Service. Les travaux que vous devez utiliser sont ceux qui ont été alloués à l’ASE par l’administrateur. Cela signifie que pour créer un plan, le nombre de travaux alloués à votre pool de travail ASE doit être supérieur au nombre total d’instances à travers l’ensemble de vos plans déjà présents dans ce pool de travail. Si vous n’avez pas suffisamment de travaux dans votre pool de travail ASE pour créer votre plan, demandez à l’administrateur de votre ASE de les ajouter.

L’absence du choix de tarification constitue une autre différence avec les plans App Service hébergés par un environnement App Service. Lorsque vous avez un environnement App Service, vous payez les ressources de calcul utilisées par le système et n’avez pas de frais supplémentaires pour les plans de cet environnement. Normalement lorsque vous créez un plan App Service, vous sélectionnez un niveau de tarification qui détermine votre facturation. Un environnement App Service est essentiellement un emplacement privé où vous pouvez créer un contenu. Vous payez l'environnement et non l'hébergement de votre contenu.

Les instructions suivantes indiquent comment créer un plan App Service lors de la création d’une application web, comme expliqué dans la section précédente du didacticiel.

1. Cliquez sur **Nouveau** dans l’interface utilisateur de sélection de plan et attribuez un nom à votre plan, exactement comme vous le feriez en dehors d’un environnement ASE.

2. Dans le sélecteur d’emplacements, sélectionnez l’environnement ASE que vous souhaitez utiliser.

	Un environnement App Service étant essentiellement un emplacement de déploiement privé, il s’affiche sous Emplacement.

	![][2]

	Après la sélection d’un environnement ASE dans le sélecteur d’emplacements, l’interface utilisateur de création de plan App Service se met à jour. L’emplacement affiche désormais le nom du système ASE et la région dans laquelle il se trouve, et le sélecteur de plans de tarification est remplacé par un sélecteur de pool de travail.

	![][3]

### Sélection d’un pool de travail

Normalement, dans Azure App Service et en dehors d’un environnement App Service, il existe 3 tailles de calcul disponibles avec la sélection d’un plan de tarification dédié. De la même manière, pour un ASE, vous pouvez définir jusqu’à 3 pools de travaux et spécifier la taille de calcul utilisée pour ce pool de travail. Pour les clients de l’ASE, cela signifie que, au lieu de sélectionner un plan de tarification avec une taille de calcul pour votre plan App Service, vous sélectionnez ce qu’on appelle un *pool de travail*.

L’interface utilisateur de sélection du pool de travaux affiche la taille de calcul utilisée pour le pool de travaux sous le nom. La quantité disponible fait référence au nombre d’instances de calcul disponibles pour une utilisation dans le pool. Le pool total peut avoir plus d’instances que ce nombre, mais la valeur fait simplement référence à ceux qui ne sont pas en cours d’utilisation. Si vous avez besoin d’ajuster votre environnement App Service pour ajouter des ressources de calcul, consultez [Configuration de votre environnement App Service](app-service-web-configure-an-app-service-environment.md).

![][4]

Dans cet exemple, vous constatez que seuls deux pools de travaux sont disponibles. La raison en est que l’administrateur de l’ASE n’a alloué les hôtes qu’à ces deux pools de travaux. Le troisième apparaît lorsque des machines virtuelles lui ont été attribuées.

## Après la création d'une application web

Il existe quelques considérations à prendre en compte pour l’exécution d’applications web et la gestion de plans App Service dans un environnement App Service.

Comme indiqué précédemment, le propriétaire de l’environnement App Service est responsable de la taille du système et, par conséquent, il est également chargé de s’assurer qu’il existe une capacité suffisante pour héberger les plans App Service souhaités. Si aucun travail n’est disponible, vous ne pourrez pas créer votre plan App Service. Il est en de même pour l'augmentation de la taille de votre application web. Si vous avez besoin de davantage d'instances, demandez à l'administrateur de votre environnement App Service d'ajouter plus de travaux.

Après avoir créé votre application web et votre plan App Service, il peut être judicieux de les mettre à l’échelle. Dans un environnement App Service, vous devez toujours avoir au moins 2 instances de votre plan App Service pour fournir une tolérance de panne à vos applications. La mise à l’échelle d’un plan App Service dans un ASE s’effectue selon la procédure normale via l’interface utilisateur du plan App Service. Pour plus d’informations sur la mise à l’échelle, consultez [Mise à l’échelle des applications web dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=AcomDC_1203_2015-->