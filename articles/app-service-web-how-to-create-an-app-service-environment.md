<properties 
	pageTitle="Comment créer un environnement App Service" 
	description="Description du flux de création pour les environnements App Service" 
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

# Comment créer un environnement App Service #

La fonctionnalité Environnements App Service (ASE) est une option de service Premium d'Azure App Service qui est actuellement en version préliminaire. Elle offre une fonction de configuration améliorée qui n'est pas disponible dans les clusters mutualisés. Pour mieux comprendre les possibilités offertes par les environnements App Service, lisez la documentation [Présentation d'un environnement App Service][WhatisASE].

### Vue d'ensemble ###

La fonctionnalité ASE déploie essentiellement Azure App Service sur le réseau virtuel du client. Pour ce faire, le client a besoin des éléments suivants :

- Un réseau virtuel régional avec 512 (/23) adresses ou plus est requis
- Un sous-réseau de ce réseau virtuel avec 256(/ 24) adresses ou plus est requis

Si vous ne disposez pas encore d'un réseau virtuel à utiliser pour héberger votre environnement App Service, vous pouvez en créer un lors de la création de l'environnement App Service.

Chaque déploiement d'ASE est un service hébergé qu'Azure gère et tient à jour. Les ressources de calcul qui hébergent les rôles système d'ASE ne sont pas accessibles au client, mais le client gère la quantité d'instances et leur taille.

## Création d'un environnement App Service ##

Il existe deux façons d'accéder à l'interface utilisateur de création d'un ASE. Il est possible de la trouver en recherchant ***environnement App Service *** dans Azure Marketplace ou en accédant à Nouveau -> Web et mobilité.

### Création rapide ###
Après avoir accédé à l'interface utilisateur de création, vous pouvez rapidement créer un ASE en entrant simplement un nom pour le déploiement. Cela crée ensuite un réseau virtuel comportant 512 adresses, un sous-réseau comportant 256 adresses sur ce réseau virtuel et un environnement ASE comportant 2 serveurs frontaux et 2 travaux dans le pool de travaux 1. Veillez à sélectionner l'emplacement où placer le système et l'abonnement dans lequel vous voulez qu'il soit. Les seuls comptes qui peuvent utiliser l'ASE pour héberger le contenu doivent être dans l'abonnement utilisé pour le créer.

Le nom spécifié pour l'ASE sera utilisé pour les applications web créées dans l'ASE. Si le nom de l'ASE est appsvcenvdemo, le nom de domaine serait .*appsvcenvdemo.p.azurewebsites.net*. Par conséquent, si vous avez créé une application web nommée mytestapp, elle serait adressable à l'adresse *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Vous ne pouvez pas utiliser d'espace blanc dans le nom. Si vous utilisez des caractères en majuscules dans le nom, le nom de domaine sera la version complète de ce nom en minuscules.


![][1]

### Pools de ressources de calcul ###

Les ressources de calcul utilisées pour l'environnement App Service sont gérées dans des pools de ressources de calcul qui permet de configurer la façon dont les instances de ressources de calcul dont vous disposez dans le pool, en plus de leur taille. Un environnement App Service se compose de serveurs frontaux et des travaux. Les serveurs frontaux gèrent la charge de connexion d'application et les travaux exécutent le code d'application. Les serveurs frontaux sont gérés dans un pool de ressources de calcul dédiées. Les travaux, quant à eux, sont gérés dans 3 pools de ressources de calcul distincts nommés

- Pool de travaux 1
- Pool de travaux 2
- Pool de travaux 3

Si vous avez un grand nombre de demandes pour des applications web simples, vous adapterez probablement la taille de vos serveurs frontaux et réduirez le nombre de travaux. Si vous avez des applications web utilisant beaucoup de mémoire ou le processeur avec un faible trafic, vous n'aurez pas besoin de nombreux serveurs frontaux, mais probablement de travaux plus nombreux ou plus volumineux.

Quelle que soit la taille des ressources de calcul, l'encombrement minimal comprend 2 serveurs frontaux et 2 travaux. Il est possible de configurer un environnement App Service pour utiliser jusqu'à 55 ressources de calcul au total. De ces 55 ressources de calcul, seules 50 peuvent être utilisées pour héberger des charges de travail. Il y a deux raisons à cela. Il existe au minimum 2 ressources de calcul frontales, ce qui en laisse 53 au maximum pour prendre en charge d'allocation des pools de travaux. Cependant, pour fournir une tolérance de panne, une ressource de calcul supplémentaire doit être allouée en respectant les règles suivantes :

- Chaque pool de travaux nécessite au moins une ressource de calcul supplémentaire qui ne peut pas être affectée à la charge de travail.
- Lorsque la quantité de ressources de calcul d'un pool dépasse une certaine valeur, une autre ressource de calcul est requise.

Dans tout un pool de travaux unique, les conditions requises pour la tolérance de pannes sont que pour une valeur donnée de X ressources affectées à un pool de travaux :

- si X est compris entre 2 et 20, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-1
- si X est compris entre 21 et 40, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-2
- si X est compris entre 41 et 53, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-3

En plus de pouvoir gérer la quantité de ressources de calcul que vous pouvez affecter à un pool donné, vous avez également le contrôle de la taille. Avec les environnements App Service, vous avez le choix entre 4 tailles différentes libellées P1 à P4. Vous trouverez des informations détaillées sur ces tailles et leur tarification ici [Service d'application Tarification][AppServicePricing]. Les tailles de ressources de calcul P1 à P3 sont identiques à ce qui est disponible dans les environnements mutualisés. La ressource de calcul P4 offre 8 cœurs avec 14 Go de RAM et est uniquement disponible dans un environnement App Service.

La tarification pour les environnements App Service est fonction des ressources de calcul affectées. Vous payez pour les ressources de calcul allouées à votre environnement App Service, qu'elles hébergent, ou non, des charges de travail.



### Création d'un réseau virtuel ###
Même s'il existe une fonction de création rapide qui crée automatiquement un réseau virtuel, la fonctionnalité prend également en charge la sélection d'un réseau virtuel existant et la création manuelle d'un réseau virtuel. Vous pouvez sélectionner un réseau virtuel existant s'il est suffisamment grand pour prendre en charge le déploiement d'un environnement App Service. Le réseau virtuel doit avoir au moins 512 adresses. Si vous ne sélectionnez pas un réseau virtuel préexistant, vous devez également spécifier un sous-réseau à utiliser ou en créer un. Le sous-réseau doit avoir au moins 256 adresses.

Si vous utilisez l'interface utilisateur de création de réseau virtuel, vous devez indiquez les éléments suivants :

- Nom du réseau virtuel
- Plage d'adresses du réseau virtuel en notation CIDR
- Nom du sous-réseau
- Plage de sous-réseau en notation CIDR

Si vous n'êtes pas familiarisé avec la notation CIDR, elle se présente sous la forme 10.0.0.0/22, où le /22 spécifie la plage. Dans cet exemple, un /22 signifie une plage de 1 024 adresses ou comprise entre 10.0.0.0 et 10.0.3.255. Un /23 signifie 512 adresses, et ainsi de suite.

![][2]

### Définition de la taille de l'environnement App Service ###

L'élément suivant à configurer est l'échelle du système. Par défaut, il existe 2 ressources de calcul Serveurs frontaux P2, 2 travaux P1 et 1 adresse IP. Il y a 2 serveurs frontaux de façon à fournir une haute disponibilité et de répartir la charge. La taille minimale pour les serveurs frontaux est P2 afin de garantir qu'ils disposent d'une capacité suffisante pour prendre en charge un système modeste. Si vous savez que le système doit prendre en charge un nombre élevé de demandes, vous pouvez ajuster la quantité de serveurs frontaux et la taille de serveur utilisée.

Comme indiqué précédemment, un ASE comprend 3 pools de travaux qu'un client peut définir. La taille des ressources de calcul peut être comprise entre P1 et P4. Par défaut, seuls 2 travaux P1 sont configurés dans le pool de travaux 1. Cela est suffisant pour prendre en charge un seul plan App Service avec 1 instance.

Les curseurs s'ajustent automatiquement afin de refléter la capacité de calcul totale disponible dans l'environnement App Service. Comme les curseurs sont ajustés dans un pool donné, les autres curseurs changent pour refléter la quantité de ressources de calcul disponibles restantes avant d'atteindre 55.
 
![][3]

L'ajout de nouvelles instances à rendre disponibles ne se produit pas rapidement. Si vous savez que vous allez avoir besoin de ressources de calcul supplémentaires, vous devez les approvisionner à l'avance. L'approvisionnement peut prendre plusieurs heures en fonction du nombre de ressources ajoutées au système. N'oubliez pas que pour garantir que votre système peut répondre aux exigences de tolérance de panne, chaque ASE doit disposer d'une instance de réserve disponible dans chaque pool de travaux.

Par défaut, un ASE est fourni avec 1 adresse IP disponible pour IP SSL. Si vous savez que vous en aurez besoin de plus, vous pouvez le spécifier ici ou la gérer une fois la création effectuée.
  
### Après la création d'un environnement App Service ###

Après la création d'un ASE, vous pouvez ajuster les éléments suivants :

- Quantité de serveurs frontaux (minimum : 2)
- Quantité de travaux (minimum : 2)
- Quantité d'adresses IP
- Tailles de ressources de calcul utilisées par les serveurs frontaux ou les travaux (la taille minimale des serveurs frontaux est P2)

Vous ne pouvez pas modifier les éléments suivants :

- Lieu
- Abonnement
- Groupe de ressources
- Réseau virtuel utilisé
- Sous-réseau utilisé

Des détails supplémentaires sur la gestion et la surveillance des environnements App Service sont disponibles ici : [Comment configurer un environnement App Service][ASEConfig].

D'autres dépendances, telles que la base de données et le stockage, ne peuvent pas être personnalisées. Celles-ci sont gérées par Azure et sont fournies avec le système. Le stockage système prend en charge jusqu'à 500 Go pour tout l'environnement App Service.


## Prise en main

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][WhatisASE].

Pour plus d'informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].



<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
<!--HONumber=52-->
