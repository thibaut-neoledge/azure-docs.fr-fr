<properties 
	pageTitle="Comment configurer un environnement App Service" 
	description="Configuration, gestion et surveillance d'environnements App Service" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Configuration d'un environnement App Service #

## Vue d'ensemble ##

La fonctionnalité Environnements App Service est une nouvelle fonctionnalité de niveau Premium proposée en version préliminaire. Elle offre de nouvelles possibilités de mise à l'échelle et d'accès réseau. Cette nouvelle fonctionnalité de mise à l'échelle permet de placer une instance d'Azure App Service dans votre réseau virtuel. Si vous n'êtes pas familiarisé avec la fonctionnalité Environnement App Service (ASE), lisez le document disponible ici [Présentation d'un environnement App Service](app-service-app-service-environment-intro.md). Pour plus d'informations sur la création d'un environnement App Service, consultez le document disponible ici [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Globalement, un environnement App Service se compose de plusieurs composants principaux :

- Ressources de calcul s'exécutant dans le service hébergé d'environnement Azure App
- Stockage
- Base de données
- Réseau virtuel avec au moins un sous-réseau
- Sous-réseau sur lequel s'exécute le service hébergé d'environnement Azure App

Pour vous aider à gérer et à surveiller vos environnements App Service, vous pouvez accéder à l'interface utilisateur destinée à cet effet en choisissant Parcourir -> Environnements App Service dans le portail Azure en version préliminaire. La version initiale dispose des éléments nécessaires pour gérer le système et continuera à s'améliorer avec des fonctionnalités supplémentaires au cours des prochaines semaines.

![][1]

## Analyse ##

Les fonctionnalités de métriques disponibles dans la version préliminaire initiale ne sont pas nombreuses, mais elles seront déployées prochainement. Ces fonctionnalités de métriques permettront aux administrateurs système de prendre des décisions sur les opérations et la mise à l'échelle du système.

Déjà maintenant dans le portail, vous pouvez répertorier tous les plans App Service de l'environnement App Service (ASE), ainsi que toutes les applications web de l'environnement App Service. Pour voir l'une de des listes, accédez à Paramètres, puis sélectionnez l'élément qui vous intéresse.

![][3]

Dans les deux listes, vous pouvez voir l'affectation des pools de travaux avec le nombre d'instances et la taille de la ressource de calcul utilisée. Les informations relatives aux performances dans un plan App Service individuel seront disponibles selon le processus habituel qui consiste à ouvrir l'interface utilisateur Plan App Service.

![][4]

## Ressources de calcul ##

Les ressources de calcul, le stockage et la base de données sont tous exploités par Azure App Service. Il incombe cependant à l'utilisateur de décider de la quantité et de la taille des ressources de calcul.

Quelle que soit la taille des ressources de calcul, l'encombrement minimal comprend 2 serveurs frontaux et 2 travaux. Il est possible de configurer un environnement App Service pour utiliser jusqu'à 55 ressources de calcul au total. De ces 55 ressources de calcul, seules 50 peuvent être utilisées pour héberger des charges de travail. Il y a deux raisons à cela. Il existe au minimum 2 ressources de calcul frontales, ce qui en laisse 53 au maximum pour prendre en charge d'allocation des pools de travaux. Cependant, pour fournir une tolérance de panne, une ressource de calcul supplémentaire doit être allouée en respectant les règles suivantes :

- Chaque pool de travaux nécessite au moins une ressource de calcul supplémentaire qui ne peut pas être affectée à la charge de travail.
- Lorsque la quantité de ressources de calcul d'un pool dépasse une certaine valeur, une autre ressource de calcul est requise.

Dans tout un pool de travaux unique, les conditions requises pour la tolérance de pannes sont que pour une valeur donnée de X ressources affectées à un pool de travaux :

- si X est compris entre 2 et 20, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-1
- si X est compris entre 21 et 40, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-2
- si X est compris entre 41 et 53, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-3

En plus de pouvoir gérer la quantité de ressources de calcul que vous pouvez affecter à un pool donné, vous avez également le contrôle de la taille. Avec les environnements App Service, vous avez le choix entre 4 tailles différentes libellées P1 à P4. Vous trouverez des informations sur ces tailles et leur tarification ici [Service d'application Tarification](../app-service/app-service-value-prop-what-is.md). Les tailles de ressources de calcul P1 à P3 sont identiques à ce qui est normalement disponible. La ressource de calcul P4 offre 8 cœurs avec 14 Go de RAM et est uniquement disponible dans un environnement App Service.

Comme indiqué précédemment, la fonctionnalité Environnement App Service est actuellement en version préliminaire et, en tant que tel, elle peut encore évoluer. En plus de fonctionnalités de surveillance supplémentaires, d'autres de fonctionnalités de gestion seront déployées à mesure que les environnements App Service évolueront vers la version grand public. Pour le moment, seuls quelques éléments peuvent être gérés dans cette interface :

- Le nombre de ressources de calcul dans chaque pool
- La taille des ressources de calcul dans chaque pool
- Le nombre d'adresses IP disponibles

Pour contrôler ces éléments, sélectionnez l'élément de configuration Échelle en haut.

![][2]

La quantité de ressources de calcul dans chaque pool et leur taille peuvent être ajustées ici. Toutefois, avant d'apporter des modifications, il est important de noter plusieurs points :

- Les modifications apportées peuvent prendre plusieurs heures en fonction de la taille de la modification demandée.
- Lorsqu'une modification de configuration de l'environnement App Service est déjà en cours, vous ne pouvez pas démarrer une autre modification.
- Si vous modifiez la taille des ressources de calcul utilisées dans un pool de travaux, vous pouvez provoquer des pannes des applications web en cours d'exécution dans ce pool de travaux.

L'ajout d'instances supplémentaires à un pool de travaux est une opération anodine et n'a pas d'impact sur le système. Toutefois, il n'en va pas de même pour la modification de la taille de la ressource de calcul utilisée dans un pool de travaux. Pour éviter tout arrêt d'application lors d'une modification de taille dans un pool de travaux, il est préférable de procéder comme suit :

- Utilisez un pool de travaux inutilisé pour afficher les instances requises dans la taille souhaitée.
- Mettez à l'échelle les plans App Service en fonction du nouveau pool de travaux.  
 
Cela est moins perturbateur pour les applications en cours d'exécution que de modifier la taille des ressources de calcul avec des charges de travail en cours d'exécution. Pour plus d'information sur la mise à l'échelle des applications web dans un environnement App Service, cliquez ici [Mise à l'échelle des applications web dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## Réseau virtuel ##

Le [réseau virtuel][virtualnetwork] et le sous-réseau sont tous les deux contrôlés par l'utilisateur. Certaines exigences réseau doivent être respectées pour les environnements App Service, mais il incombe à l'utilisateur de contrôler le reste. Ces exigences ASE sont les suivantes :

- Un réseau virtuel avec au moins 512 adresses
- Un sous-réseau avec au moins 256 adresses 
- Le réseau virtuel doit être un réseau virtuel régional  
 
L'administration de votre réseau virtuel s'effectue via l'interface utilisateur normale de Virtual Network.

Étant donné que cette fonctionnalité place Azure App Service sur votre réseau virtuel, vos applications hébergées dans votre environnement App Service (ASE) peuvent désormais accéder aux ressources rendues disponibles directement via des VPN ExpressRoute ou site à site. Les applications de vos environnements App Service ne nécessitent pas de fonctionnalités de mise en réseau supplémentaires pour accéder aux ressources disponibles sur le réseau virtuel qui héberge votre environnement App Service.

Si vous le souhaitez, vous pouvez désormais également contrôler l'accès à l'aide de groupes de sécurité réseau. Cette fonctionnalité vous permet de verrouiller votre environnement App Service pour autoriser uniquement les adresses IP auxquelles vous souhaitez le restreindre. Pour plus d'informations sur la façon de procéder, consultez le document disponible ici [Comment contrôler le trafic entrant dans un environnement App Service](app-service-app-service-environment-control-inbound-traffic.md).

## Suppression d'un environnement App Service ##

Si vous voulez supprimer un environnement App Service, utilisez simplement l'action de suppression en haut du volet Environnement App Service. Vous ne pouvez toutefois pas supprimer un ASE comprenant du contenu. Veillez à supprimer l'ensemble des applications web et plans App Service pour supprimer votre environnement App Service.

## Prise en main

Pour prendre en main les environnements App Service, consultez [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=62-->