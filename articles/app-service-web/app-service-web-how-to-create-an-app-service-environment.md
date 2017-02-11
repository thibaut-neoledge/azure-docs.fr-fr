---
title: "Comment créer un environnement App Service"
description: "Description du flux de création pour les environnements App Service"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: af98ae3b425e2e0584da53721249dbcd605a17d1
ms.openlocfilehash: 8d8e1ce7758691a392fdb6d6459e04c6b4f7f5f6


---
# <a name="how-to-create-an-app-service-environment"></a>Comment créer un environnement App Service
### <a name="overview"></a>Vue d’ensemble
Les environnements App Service constituent une option de service Premium d’Azure App Service offrant une fonction de configuration améliorée qui n’est pas disponible dans les clusters mutualisés.  La fonctionnalité ASE déploie essentiellement Azure App Service sur le réseau virtuel du client.  Pour mieux comprendre les possibilités offertes par les environnements App Service, lisez la documentation [Qu'est-ce qu'un environnement App Service ?][WhatisASE].

### <a name="before-you-create-your-ase"></a>Avant de créer votre ASE
Il est important de connaître les choses que vous ne pouvez pas modifier.  Voici les aspects que vous ne pouvez pas modifier concernant votre ASE après sa création :

* Lieu
* Abonnement
* Groupe de ressources
* Réseau virtuel utilisé
* Sous-réseau utilisé 
* Taille du sous-réseau

Lors de la sélection d’un réseau virtuel et de la spécification d’un sous-réseau, assurez-vous qu’il est suffisamment grand pour contenir toute croissance future.  

### <a name="creating-an-app-service-environment"></a>Création d’un environnement App Service
Il existe deux façons d'accéder à l'interface utilisateur de création d'un ASE.  Il est possible de la trouver en recherchant ***App Service Environment*** dans Azure Marketplace ou en accédant à Nouveau -> Web et mobilité -> App Service Environment.  Pour créer un ASE :

1. Indiquez le nom de votre ASE.  Le nom spécifié pour l’ASE sera utilisé pour les applications créées dans l’ASE.  Si le nom de l’ASE est appsvcenvdemo, le nom du sous-domaine est .*appsvcenvdemo.p.azurewebsites.net*.  Par conséquent, si vous créez une application nommée *mytestapp*, elle est adressable à l’adresse *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  Vous ne pouvez pas utiliser d’espace blanc dans le nom de votre ASE.  Si vous utilisez des caractères en majuscules dans le nom, le nom de domaine sera la version complète de ce nom en minuscules.  Si vous utilisez un ILB, le nom de votre ASE n’est pas utilisé dans votre sous-domaine mais explicitement indiqué lors de la création de l’ASE.
   
    ![][1]
2. Sélectionnez votre abonnement.  L’abonnement utilisé pour votre ASE est également celui avec lequel toutes les applications dans cet ASE seront créées.  Vous ne pouvez pas placer votre ASE dans un réseau virtuel qui se trouve dans un autre abonnement.
3. Sélectionnez ou spécifiez un nouveau groupe de ressources.  Le groupe de ressources utilisé pour votre ASE doit être le même que celui utilisé pour votre réseau virtuel.  Si vous sélectionnez un réseau virtuel préexistant, la sélection du groupe de ressources pour votre ASE sera mise à jour pour refléter celle de votre réseau virtuel.
   
    ![][2]
4. Effectuez vos sélections de réseau virtuel et d’emplacement.  Vous pouvez choisir de créer un réseau virtuel ou de sélectionner un réseau virtuel existant.  Si vous sélectionnez un réseau virtuel, vous pouvez indiquer un nom et un emplacement. Le nouveau réseau virtuel se voit affecter la plage d’adresses 192.168.250.0/23 et un sous-réseau nommé **default** est défini sur la plage 192.168.250.0/24.  Vous pouvez aussi simplement sélectionner un réseau virtuel préexistant classique ou du Gestionnaire de ressources.  La sélection du type de l’adresse IP virtuelle détermine si votre ASE est accessible directement à partir d’internet (externe) ou si elle utilise un équilibrage de charge interne (ILB).  Pour en savoir plus, consultez [Utilisation d’un équilibreur de charge interne avec un environnement App Service][ILBASE].  Si vous sélectionnez un type d’adresse IP virtuelle d’externe, vous pouvez sélectionner le nombre d’adresses IP externes avec lesquelles le système est créé à des fins IPSSL.  Si vous sélectionnez Interne, vous devez spécifier le sous-domaine que votre ASE utilisera.  Les ASE peuvent être déployés dans les réseaux virtuels qui utilisent *soit* des plages d’adresses publiques, *soit* des espaces d’adressage RFC1918 (par exemple, des adresses privées).  Pour utiliser un réseau virtuel avec une plage d’adresses publiques, vous devrez créer le réseau virtuel à l’avance.  Lorsque vous sélectionnez un réseau virtuel préexistant, vous devrez créer un nouveau sous-réseau lors de la création de l’ASE.  **Vous ne pouvez pas utiliser un sous-réseau créé au préalable dans le portail.  Vous pouvez créer un ASE avec un sous-réseau pré-existant si vous le créez à l’aide d’un modèle Resource Manager.**  Pour créer un ASE à partir d’un modèle, utilisez les informations dans les rubriques sur la [création d’un environnement App Service à partir du modèle][ILBAseTemplate] et la[création d’un environnement App Service ILB à partir du modèle][ASEfromTemplate].

### <a name="details"></a>Détails
Un ASE est créé avec 2 serveur frontaux et 2 travaux.  Les ressources frontales servent de points de terminaison HTTP/HTTPS et envoient le trafic vers les travaux, les rôles qui hébergent vos applications.   Vous pouvez ajuster la quantité après la création de l’ASE et pouvez même définir des règles de mise à l’échelle automatique sur ces pools de ressources.  Pour plus de détails sur la mise à l’échelle manuelle et la gestion et la surveillance d’un environnement App Service, consultez : [Comment configurer un environnement App Service][ASEConfig]. 

Seul un ASE peut exister dans le sous-réseau utilisé par l’ASE.  Le sous-réseau ne peut pas être utilisé à d’autres fins que l’ASE.

### <a name="after-app-service-environment-creation"></a>Après la création d'un environnement App Service
Après la création d'un ASE, vous pouvez ajuster les éléments suivants :

* Quantité de serveurs frontaux (minimum : 2)
* Quantité de travaux (minimum : 2)
* Quantité d’adresses IP disponibles pour IP SSL
* Tailles de ressources de calcul utilisées par les serveurs frontaux ou les travaux (la taille minimale des serveurs frontaux est P2)

Des détails supplémentaires sur la mise à l’échelle manuelle, ainsi que sur la gestion et la surveillance des environnements App Service, sont disponibles dans la rubrique [Comment configurer un environnement App Service][ASEConfig]. 

Pour plus d’informations sur la mise à l’échelle automatique, un guide est disponible dans la rubrique sur la [configuration de la mise à l’échelle automatique pour un environnement App Service][ASEAutoscale]

D'autres dépendances, telles que la base de données et le stockage, ne peuvent pas être personnalisées.  Celles-ci sont gérées par Azure et sont fournies avec le système.  Le stockage système prend en charge jusqu’à 500 Go pour l’ensemble de l’environnement App Service, et la base de données est ajustée par Azure en fonction de la mise à l’échelle du système.

## <a name="getting-started"></a>Prise en main
Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

Pour prendre en main les environnements App Service, consultez [la présentation des environnements App Service][WhatisASE].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/



<!--HONumber=Feb17_HO2-->


