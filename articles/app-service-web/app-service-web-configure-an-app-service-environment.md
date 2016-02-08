<properties 
	pageTitle="Comment configurer un environnement App Service" 
	description="Configuration, gestion et surveillance d'environnements App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Configuration d'un environnement App Service #

## Vue d'ensemble ##

Les environnements App Service constituent une fonctionnalité de niveau Premium dans Azure App Service qui offre de nouvelles fonctionnalités de mise à l’échelle et d’accès réseau. Cette nouvelle fonctionnalité de mise à l'échelle permet de placer une instance d'Azure App Service dans votre réseau virtuel. Cette fonctionnalité peut héberger des applications web, applications mobiles et applications API. Les applications logiques ne s’exécutent pas encore dans un ASE.

Si vous n'êtes pas familiarisé avec la fonctionnalité Environnement App Service (ASE), lisez le document disponible ici [Présentation d'un environnement App Service](app-service-app-service-environment-intro.md). Pour plus d'informations sur la création d'un environnement App Service, consultez le document disponible ici [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Globalement, un environnement App Service se compose de plusieurs composants principaux :

- Ressources de calcul s'exécutant dans le service hébergé d'environnement Azure App
- Stockage
- Base de données
- Réseau virtuel « v1 » classique avec au moins un sous-réseau
- Sous-réseau sur lequel s'exécute le service hébergé d'environnement Azure App

Les ressources de calcul sont utilisées pour vos 4 pools de ressources. Chaque environnement App Service possède un ensemble de serveurs frontaux et 3 pools de travail. Vous n’avez pas besoin d’utiliser les 3 pools de travail et, si vous le souhaitez, vous pouvez n’en utiliser qu’un. Les serveurs frontaux sont les points de terminaison HTTP pour vos applications maintenues dans votre ASE. Les Workers sont le lieu d’exécution de vos applications. Le moment où vous devez ajouter des serveurs frontaux ou des Workers est lié aux performances des applications placées dans l’ASE. Par exemple, supposons que vous avez seulement une application dans votre ASE et qu’il s’agit d’une application Hello World qui reçoit un grand nombre de demandes. Dans ce cas, vous aurez besoin de faire monter en puissance vos serveurs frontaux pour gérer la charge HTTP mais, à l’inverse, vous n’aurez pas besoin de faire monter en puissance vos Workers. Gérer tous ces processus manuellement est plutôt rébarbatif, surtout lorsque l’on pense que chaque ASE possède probablement une combinaison d’applications qui s’exécutent avec des critères de performances divers. Fort heureusement, nous avons ajouté la mise à l’échelle automatique aux environnements App Service et cela va grandement vous faciliter la vie. Pour des informations concernant la mise à l’échelle classique et automatique des environnements App Service, consultez [Mise à l’échelle automatique et environnement App Service][ASEAutoscale].

Chaque ASE est configuré avec 500 Go de stockage. Cet espace est utilisé par toutes les applications dans l’ASE. Cet espace de stockage est une partie de l’ASE ; actuellement, aucun basculement pour utiliser l’espace de stockage du client n’est possible.

La base de données conserve les informations qui définissent l’environnement, ainsi que des détails sur les applications exécutées. Il s’agit également d’une partie de l’abonnement Azure et ce n’est pas un élément pour lequel les clients peuvent effectuer des manipulations directes.

Le réseau virtuel qui est utilisé avec votre ASE peut être un réseau que vous avez créé lors de la création de l’ASE ou un réseau dont vous disposiez au préalable. Si vous souhaitez que votre réseau virtuel figure dans un groupe de ressources distinct de celui utilisé pour votre ASE, vous devez créer votre réseau virtuel séparément du flux de création de l’ASE. Il est judicieux de créer le sous-réseau à utiliser en même temps que le sous-réseau, lors de la création de l’ASE, forçant ainsi l’ASE à figurer dans le même groupe de ressources que le réseau virtuel. Actuellement, seuls les réseaux virtuels « classiques » V1 sont pris en charge.

L’interface utilisateur permettant de gérer et surveiller votre environnement App Service est disponible à partir du portail Azure. Si vous possédez un ASE, il est possible que vous voyiez le symbole App Service dans votre barre latérale. Ce symbole est utilisé pour représenter les environnements App Service dans le portail Azure.

![][1]

Vous pouvez utiliser l’icône ou sélectionner le chevron (symbole « supérieur à ») au bas de la barre latérale et sélectionner les environnements App Service. Les deux actions produisent le même résultat, à savoir, ouvrir l’interface utilisateur qui répertorie l’ensemble de vos environnements App Service. La sélection de l’un des ASE répertoriés ouvre l’interface utilisateur utilisée pour le surveiller et le gérer.

![][2]

Ce premier panneau présente certaines propriétés de votre ASE avec un graphique de mesures par pool de ressources. Certaines des propriétés affichées dans le bloc Essentiels sont également des liens hypertexte qui ouvrent le panneau associé. Par exemple, vous pouvez sélectionner le nom de réseau virtuel pour ouvrir l’interface utilisateur associée au réseau virtuel dans lequel votre ASE est exécuté. Les plans App Service et les applications ouvrent des panneaux qui répertorient les éléments figurant dans votre ASE.

## Analyse ##

Les graphiques permettent de voir les différentes mesures de performances dans chaque pool de ressources. Pour le pool des serveurs frontaux, il est très judicieux de surveiller l’utilisation moyenne de l’UC et de la mémoire. Les serveurs frontaux ont une charge distribuée. En examinant une moyenne, vous pouvez bénéficier d’un bon aperçu des performances générales. Le fonctionnement des pools de travail est différent. Plusieurs plans App Service peuvent utiliser les Workers dans un pool de travail. Si tel est le cas, alors l’utilisation de l’UC et de la mémoire ne fournit pas beaucoup d’informations utiles. Il est plus important de suivre le nombre de Workers que vous avez utilisés et qui sont disponibles, en particulier si vous gérez ce système pour d’autres utilisateurs.

Toutes les mesures qui peuvent être suivies dans les graphiques peuvent également servir à configurer des alertes. La configuration d’alertes fonctionne de la même manière qu’ailleurs dans App Service. Vous pouvez définir une alerte à partir de l’interface utilisateur Alertes ou dans toute interface utilisateur de mesures en cliquant sur Ajouter une alerte.
 
![][3]

Les mesures dont nous venons de parler sont les mesures de l’environnement App Service. Des mesures sont également disponibles au niveau du plan App Service. C’est là que la surveillance de l’utilisation de l’UC et de la mémoire se révèle intéressante. Dans un ASE, tous les ASP sont des ASP dédiés. Cela signifie que les seules applications qui sont exécutées sur les hôtes alloués à cet ASP sont les applications de cet ASP. Pour afficher des détails sur votre ASP, affichez simplement votre ASP à partir d’une liste quelconque dans l’interface utilisateur de l’ASE, voire à partir des plans App Service qui les répertorient tous.

Il est possible que vous soyez déjà familiarisé avec les fonctionnalités de mise à l’échelle automatique qui sont disponibles pour les ASP en dehors d’un ASE, et avec la manière dont celles-ci exploitent les mesures qui sont disponibles pour une ressource. Cette remarque vaut également pour la mise à l’échelle automatique d’un environnement App Service. Non seulement vous pouvez toujours mettre à l’échelle automatiquement l’ASP en fonction des mesures dans un ASE, mais vous pouvez également configurer des règles de mise à l’échelle automatique pour l’ASE lui-même. Pour plus d’informations sur la configuration de la mise à l’échelle automatique, il existe un guide détaillé, [Mise à l’échelle automatique et environnement App Service][ASEAutoscale].


## Properties ##

Le volet Paramètres s’ouvre automatiquement lorsque vous affichez le panneau de votre ASE. Les Propriétés se trouvent dans la partie supérieure. Ici, plusieurs éléments sont redondants avec ce que vous voyez dans le bloc Essentiels, mais deux propriétés sont très utiles : l’adresse IP virtuelle et l’adresse IP sortante. Pour l’instant, il s’agit d’une seule et même adresse, mais à un moment donné à l’avenir, il sera probablement possible d’avoir une adresse IP sortante distincte de l’adresse IP virtuelle, raison pour laquelle elles sont toutes deux indiquées ici. Par conséquent, si vous ne pensez pas configurer un protocole SSL et ajouter une adresse IP juste pour une seule application dans votre ASE, l’adresse IP qui sera définie dans le système DNS pour les applications créées dans votre ASE sera l’adresse IP virtuelle que vous voyez ici dans Propriétés.

![][4]

## Adresses IP ##

Voici l’emplacement où vous pouvez ajouter des adresses IP supplémentaires à votre ASE pour une utilisation par vos applications. Lorsque vous créez une application dans votre ASE que vous souhaitez configurer avec IP SSL, vous devez avoir une adresse IP réservée pour cette application. Pour ce faire, votre ASE doit posséder certaines adresses IP qui peuvent être allouées. Lorsqu’un ASE est créé, il possède 1 adresse prévue à cet effet. Si vous avez besoin de plus d’adresses, accédez à cet emplacement et ajoutez-en d’autres. Avant de sélectionner l’unité maximum au cas où vous auriez besoin de plus d’adresses, sachez qu’il existe un coût pour les adresses IP supplémentaires. Les détails relatifs au coût supplémentaire sont suivis sur la page de tarification, [Tarification App Service][AppServicePricing]. Il vous suffit de faire défiler jusqu’à la section Connexions SSL. Le supplément de prix correspond au coût IP SSL.

**REMARQUE :** si vous ajoutez plus d’adresses IP, sachez qu’il s’agit d’une opération de mise à l’échelle. Il ne peut y avoir qu’une seule opération de mise à l’échelle en cours à la fois. Il existe trois opérations de mise à l’échelle :

- Modifier le nombre d’adresses IP dans l’ASE qui sont disponibles pour l’utilisation IP SSL
- Modifier la taille de la ressource de calcul utilisée dans un pool de ressources
- Modifier le nombre de ressources de calcul utilisées dans un pool de ressources manuellement ou via la mise à l’échelle automatique

## Pools de ressources ##

Dans Paramètres, vous pouvez accéder au pool de serveurs frontaux ou à l’interface utilisateur Pools de travail. Chacun de ces panneaux de pools de ressources offre la possibilité d’afficher des informations uniquement sur ce pool de ressources, en plus de proposer des contrôles pour mettre entièrement à l’échelle ce pool de ressources.

Le panneau de base pour chaque pool de ressources fournit un graphique avec des mesures pour ce pool de ressources. À l’instar des graphiques du panneau de l’ASE, vous pouvez entrer dans le graphique et configurer les alertes de votre choix. La configuration d’une alerte à partir du panneau de l’ASE pour un pool de ressources spécifique produit le même résultat que d’effectuer l’opération à partir du pool de ressources. Dans le panneau Paramètres du pool de travail, vous avez accès à la liste de l’ensemble des applications ou plans App Service qui s’exécutent dans ce pool de travail.

![][5]

### Mise à l’échelle de la quantité de ressources de calcul ###

Pour donner une meilleure perspective de la mise à l’échelle des applications dans un ASE, consultez le guide [Mise à l’échelle des applications dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Si vous souhaitez en savoir plus sur la façon de configurer la mise à l’échelle automatique pour les pools de ressources de l’ASE, commencez par consulter [Mise à l’échelle automatique et environnement App Service][ASEAutoscale]. Cette description présente en détail les opérations de mise à l’échelle manuelle sur vos pools de ressources.

Les pools de ressources (des serveurs frontaux et Workers) ne sont pas directement accessibles par les clients. Autrement dit, vous ne pouvez pas effectuer d’opération de protocole RDP vers eux, modifier leur configuration ou agir en tant qu’administrateur. Ils sont utilisés et gérés par Azure. Cela étant dit, il incombe cependant à l’utilisateur de décider de la quantité et de la taille des ressources de calcul.

Il existe en fait trois façons de contrôler le nombre de serveurs dont vous disposez dans vos pools de ressources : opération de mise à l’échelle à partir du panneau principal de l’ASE dans la partie supérieure ; opération de mise à l’échelle manuelle à partir du panneau Mise à l’échelle du pool de ressources individuel, sous Paramètres ; mise à l’échelle automatique configurée à partir du panneau Mise à l’échelle du pool de ressources individuel

Pour utiliser l’opération de mise à l’échelle sur le panneau de l’ASE, cliquez simplement dessus, faites glisser le curseur vers la quantité souhaitée et enregistrez. Cette interface utilisateur prend également en charge la modification de la taille.

![][6]

Pour utiliser les fonctionnalités manuelles ou automatiques de mise à l’échelle dans un pool de ressources spécifique, commencez par accéder au panneau de l’ASE, puis à Paramètres. Dans Paramètres, ouvrez un pool de serveurs frontaux ou des pools de travail selon vos besoins, puis ouvrez le pool que vous souhaitez modifier. Sous Paramètres, il existe une option Mise à l’échelle. Le panneau qui s’affiche en cliquant sur cette option peut servir pour la mise à l’échelle manuelle ou automatique.

![][7]

Il est possible de configurer un environnement App Service pour utiliser jusqu'à 55 ressources de calcul au total. De ces 55 ressources de calcul, seules 50 peuvent être utilisées pour héberger des charges de travail. Il y a deux raisons à cela. Il existe au minimum 2 ressources de calcul frontales, ce qui en laisse 53 au maximum pour prendre en charge d'allocation des pools de travaux. Pour fournir une tolérance de panne, une ressource de calcul supplémentaire doit être allouée en respectant les règles suivantes :

- Chaque pool de travail nécessite au moins une ressource de calcul supplémentaire qui n’est pas disponible pour qu’une charge de travail lui soit affectée.
- Lorsque la quantité de ressources de calcul dans un pool de travail dépasse une certaine valeur, une autre ressource de calcul est requise pour la tolérance de panne. Cela n’est pas le cas dans le pool de serveurs frontaux.

Dans tout un pool de travaux unique, les conditions requises pour la tolérance de pannes sont que pour une valeur donnée de X ressources affectées à un pool de travaux :

- si X est compris entre 2 et 20, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-1
- si X est compris entre 21 et 40, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-2
- si X est compris entre 41 et 53, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-3

N’oubliez pas que l’encombrement minimal comprend 2 serveurs frontaux et 2 Workers. Voici quelques exemples pour clarifier les déclarations ci-dessus.

- Si vous avez 30 Workers dans un pool unique, 28 d’entre eux peuvent servir à héberger les charges de travail. 
- Si vous avez 2 Workers dans un pool unique, 1 peut servir à héberger les charges de travail.
- Si vous avez 20 Workers dans un pool unique, 19 peuvent servir à héberger les charges de travail.  
- Si vous avez 21 Workers dans un pool unique, seuls 19 peuvent servir à héberger les charges de travail.  

L’aspect tolérance de panne est important, et vous devez le garder à l’esprit à mesure que votre mise à l’échelle dépasse certains seuils. Si vous souhaitez augmenter la capacité au-delà de 20 instances, alors passez à 22 instances ou plus, car un nombre de 21 instances n’ajoute pas plus de capacité. Cette remarque est également valable au-delà de 40 instances : le nombre suivant qui ajoute de la capacité est 42.

### Mise à l’échelle de la taille des ressources de calcul ###

En plus de pouvoir gérer la quantité de ressources de calcul que vous pouvez affecter à un pool donné, vous avez également le contrôle de la taille. Avec les environnements App Service, vous avez le choix entre 4 tailles différentes libellées P1 à P4. Vous trouverez des informations sur ces tailles et leur tarification ici [Service d'application Tarification](../app-service/app-service-value-prop-what-is.md). Les tailles de ressources de calcul P1 à P3 sont identiques à ce qui est normalement disponible. La ressource de calcul P4 offre 8 cœurs avec 14 Go de RAM et est uniquement disponible dans un environnement App Service.

Si vous souhaitez modifier la taille des ressources de calcul utilisées dans vos pools, deux méthodes s’offrent à vous. L’option Mise à l’échelle, disponible dans le panneau de l’ASE, et le panneau Niveaux tarifaires, que vous obtenez en accédant à Paramètres dans le pool de ressources individuel.

![][8]

Toutefois, avant d'apporter des modifications, il est important de noter plusieurs points :

- Les modifications apportées peuvent prendre plusieurs heures en fonction de la taille de la modification.
- Lorsqu'une modification de configuration de l'environnement App Service est déjà en cours, vous ne pouvez pas démarrer une autre modification.
- Si vous modifiez la taille des ressources de calcul utilisées dans un pool de travail, vous pouvez provoquer des pannes des applications en cours d’exécution dans ce pool de travail.

L’ajout d’instances supplémentaires à un pool de travail est une opération anodine et n’entraîne pas de pannes des applications pour les ressources dans ce pool. Toutefois, il n'en va pas de même pour la modification de la taille de la ressource de calcul utilisée dans un pool de travaux. Pour éviter tout arrêt d’application lors d’une modification de taille dans un pool de travail, il est préférable de procéder comme suit :

- Utilisez un pool de travaux inutilisé pour afficher les instances requises dans la taille souhaitée.
- Mettez à l'échelle les plans App Service en fonction du nouveau pool de travaux.  
 
Cela est moins perturbateur pour les applications en cours d'exécution que de modifier la taille des ressources de calcul avec des charges de travail en cours d'exécution. Pour plus d’informations sur la mise à l’échelle des applications dans un environnement App Service, consultez [Mise à l’échelle des applications dans un environnement App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

## Réseau virtuel ##

Contrairement au service hébergé qui contient l’ASE, le [réseau virtuel][virtualnetwork] et le sous-réseau sont tous les deux contrôlés par l’utilisateur. Certaines exigences réseau doivent être respectées pour les environnements App Service, mais il incombe à l’utilisateur de contrôler le reste. Ces exigences ASE sont les suivantes :

- un réseau virtuel « V1 » classique 
- un sous-réseau avec au moins 8 adresses ; 
- le réseau virtuel doit être un réseau virtuel régional.  
 
L’administration de votre réseau virtuel s’effectue via l’interface utilisateur de réseau virtuel ou Powershell.

Étant donné que cette fonctionnalité place Azure App Service sur votre réseau virtuel, vos applications hébergées dans votre environnement App Service (ASE) peuvent désormais accéder aux ressources rendues disponibles directement via des VPN ExpressRoute ou site à site. Les applications de vos environnements App Service ne nécessitent pas de fonctionnalités de mise en réseau supplémentaires pour accéder aux ressources disponibles sur le réseau virtuel qui héberge votre environnement App Service. Cela signifie que vous n’avez pas besoin d’utiliser l’intégration de réseau virtuel ou les connexions hybrides pour accéder aux ressources dans ou connectées à votre réseau virtuel. Vous pouvez toujours utiliser ces deux fonctionnalités pour accéder aux ressources dans les réseaux qui ne sont pas connectés à votre réseau virtuel. Par exemple, vous pouvez utiliser l’intégration de réseau virtuel pour vous intégrer à un réseau virtuel qui figure dans votre abonnement mais qui n’est pas connecté au réseau virtuel dans lequel se trouve votre ASE. Vous pouvez toujours également utiliser des connexions hybrides pour accéder aux ressources dans d’autres réseaux, comme vous le faites normalement.

Si votre réseau virtuel est configuré avec un VPN ExpressRoute, vous devez être informé de certains des besoins de routage d’un ASE. Certaines configurations d’itinéraire défini par l’utilisateur ne sont pas compatibles avec un ASE. Pour plus de détails concernant l’exécution d’un ASE dans un réseau virtuel avec ExpressRoute, consultez le document [Exécution d’un environnement App Service dans un réseau virtuel avec ExpressRoute][ExpressRoute].

Vous pouvez désormais également contrôler l’accès à vos applications à l’aide de groupes de sécurité réseau. Cette fonctionnalité vous permet de verrouiller votre environnement App Service pour autoriser uniquement les adresses IP auxquelles vous souhaitez le restreindre. Pour plus d'informations sur la façon de procéder, consultez le document disponible ici [Comment contrôler le trafic entrant dans un environnement App Service](app-service-app-service-environment-control-inbound-traffic.md).

## Suppression d'un environnement App Service ##

Si vous voulez supprimer un environnement App Service, utilisez simplement l'action de suppression en haut du volet Environnement App Service. Lorsque vous effectuez cette opération, vous êtes invité à entrer le nom de votre environnement App Service pour confirmer que vous souhaitez vraiment cela. REMARQUE : lorsque vous supprimez un environnement App Service, vous supprimez également tout son contenu.

![][9]

## Prise en main

Pour prendre en main les environnements App Service, consultez [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0128_2016-->