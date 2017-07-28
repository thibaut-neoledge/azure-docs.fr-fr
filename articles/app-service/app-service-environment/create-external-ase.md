---
title: "Créer un environnement Azure App Service Environment externe"
description: "Explique comment créer un environnement Azure App Service Environment lors de la création d’une application ou en mode autonome"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>Créer un environnement App Service Environment externe #

L’ASE (App Service Environment) est un déploiement d’Azure App Service dans un sous-réseau de votre réseau virtuel Azure. Vous pouvez déployer un ASE de deux façons :

- avec une adresse IP virtuelle sur une adresse IP externe, pour un environnement souvent appelé _ASE externe_ ;
- avec l’adresse IP virtuelle sur une adresse IP interne, pour un environnement souvent appelé _ASE ILB_, car le point de terminaison interne est un équilibreur de charge interne (ILB, Internal Load Balancer).

Cet article vous explique comment créer un ASE externe. Pour une vue d’ensemble de l’ASE, consultez [An Introduction to the App Service Environment (Présentation de l’environnement ASE)][Intro]. Pour plus d’informations sur la création d’un ASE ILB, consultez [Create and use an ILB ASE (Créer et utiliser un ASE ILB)][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Avant de créer votre ASE ##

Il est important de savoir ce que vous ne pouvez plus modifier après la création de l’ASE :

- Emplacement
- Abonnement
- Groupe de ressources
- Réseau virtuel utilisé
- Sous-réseau utilisé
- Taille du sous-réseau

> [!NOTE]
> Lors de la sélection d’un réseau virtuel et de la spécification d’un sous-réseau, vérifiez que leur taille leur permet de prendre en compte les évolutions futures. La taille recommandée est un `/25` avec 128 adresses.
>

## <a name="three-ways-to-create-an-ase"></a>Trois façons de créer un ASE ##

Il existe 3 façons de créer un ASE. Vous pouvez créer un ASE :

- Lors de la création d’un plan App Service, ce qui crée l’ASE et le plan App Service en une seule étape.
- À partir de l’interface utilisateur de création d’ASE autonome, ce qui crée un ASE vide. Ce mode de création plus avancé s’utilise pour créer un ASE avec un équilibreur de charge interne (ILB).
- À l’aide d’un modèle Resource Manager. Ce mode convient à un utilisateur avancé et est détaillé dans [Create an ASE from a template (Créer un ASE à partir d’un modèle)][MakeASEfromTemplate].

Un ASE créé sans équilibreur de charge interne possède une adresse IP virtuelle publique. Cela signifie que tout le trafic HTTP destiné aux applications dans l’ASE sera envoyé à une adresse IP accessible par Internet. Un ASE avec un ILB a un point de terminaison sur une adresse IP de réseau virtuel. Ces applications ne sont pas exposées directement à Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Créer un ASE et un plan App Service ensemble ##

Le plan App Service est un conteneur d’applications. Lorsque vous créez une application dans App Service, vous devez toujours choisir ou créer un plan App Service. Le modèle de conteneur est le suivant : les environnements contiennent les plans App Service et les plans App Service contiennent les applications.

Pour créer un ASE lors de la création du plan App Service :

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau &gt; Web + mobile &gt; Application web**.

    ![][1]
1. Sélectionnez votre abonnement. Si vous avez plusieurs abonnements, sachez que pour créer une application dans votre ASE, vous devez utiliser le même abonnement que celui utilisé lors de la création de l’ASE.
1. Sélectionnez ou créez un groupe de ressources. Les *groupes de ressources* vous permettent de gérer les ressources Azure connexes en tant qu’unité et sont utiles lors de l’établissement de règles de *contrôle d’accès en fonction du rôle* (RBAC) pour vos applications. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager][ARMOverview].
1. Cliquez sur le plan App Service, puis sélectionnez **Créer un nouveau**.

    ![][2]
1. Dans la liste déroulante **Emplacement**, choisissez la région où vous souhaitez créer l’ASE. Si vous sélectionnez un ASE, vous ne pourrez pas en créer un. Vous ne pourrez que créer le plan App Service dans l’ASE sélectionné.
1. Cliquez sur l’interface utilisateur **Plan de tarification** et choisissez l’une des références SKU de tarification **Isolé**. La sélection d’une carte de référence SKU **Isolé** et d’un emplacement qui n’est pas un ASE indique que vous souhaitez créer un ASE dans cet emplacement. L’interface utilisateur de création d’ASE s’affiche lorsque vous cliquez sur **Sélectionner** dans la page de la carte de tarification. La référence SKU **Isolé** n’est disponible qu’avec un ASE. Vous ne pouvez pas utiliser une autre référence SKU de tarification dans un ASE qui n’est pas **Isolé**.

    ![][3]
1. Indiquez le nom de votre ASE. Le nom de votre ASE est utilisé dans le nom adressable de vos applications. Si le nom de l’ASE est _appsvcenvdemo_, le nom du sous-domaine est *.appsvcenvdemo.p.azurewebsites.net*. Donc, si vous créez une application nommée *mytestapp*, elle est adressable à l’adresse *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Vous ne pouvez pas utiliser d’espace blanc dans le nom de votre ASE. Si vous utilisez des caractères en majuscules dans le nom, le nom de domaine sera la version complète de ce nom en minuscules.

    ![][4]
1. Choisissez **Créer un nouveau** ou **sélectionner**. L’option permettant de sélectionner un réseau virtuel n’est disponible que si vous avez un réseau virtuel dans la région sélectionnée. Si vous sélectionnez **Créer un nouveau**, indiquez le nom du réseau virtuel. Un réseau virtuel Resource Manager portant ce nom sera créé avec l’espace d’adressage `192.168.250.0/23` dans la région sélectionnée. Si vous choisissez **Sélectionner**, vous devez :
    1. sélectionner le bloc d’adresses du réseau virtuel si vous avez plusieurs ;
    2. indiquer le nom d’un nouveau sous-réseau ;
    3. sélectionner la taille du sous-réseau. **Rappel : prévoyez une taille suffisante pour la croissance future de votre ASE.** La taille recommandée est un `/25` qui comprend 128 adresses et peut gérer un ASE d’une taille maximale. `/28` est déconseillé, par exemple, car 16 adresses seulement sont disponibles. L’infrastructure utilise au moins 5 adresses, ce qui vous laisserait au maximum 11 instances dans un sous-réseau `/28`.
    4. Sélectionnez la plage IP du sous-réseau.

Le processus de création d’ASE commence lorsque vous sélectionnez **Créer**. Cette opération crée également le plan App Service et l’application. L’ASE, le plan App Service et l’application seront regroupés dans le même abonnement et le même groupe de ressources. Si vous devez placer votre ASE dans un groupe de ressources autre que celui de votre plan App Service et de l’application, ou si vous avez besoin d’un ASE ILB, utilisez le mode de création d’ASE autonome.

## <a name="create-an-ase-by-itself"></a>Créer un ASE autonome ##

Le mode de création autonome crée un ASE vide. Même vide, un ASE occasionne des frais mensuels pour l’infrastructure. L’intérêt de ce mode est de créer un ASE avec un ILB ou dans son propre groupe de ressources. Une fois l’ASE créé, vous pouvez y créer des applications en utilisant le processus normal et en sélectionnant votre nouvel ASE comme emplacement.

Pour accéder à l’interface utilisateur de création d’ASE, recherchez ***App Service Environment*** dans Azure Marketplace ou sélectionnez Nouveau -&gt; Web et mobilité -&gt; App Service Environment. Pour créer un ASE en mode de création autonome :

1. Indiquez le nom de votre ASE. Le nom spécifié pour l’ASE sera utilisé pour les applications créées dans l’ASE. Si le nom de l’ASE est *mynewdemoase*, le nom du sous-domaine est *.mynewdemoase.p.azurewebsites.net*. Si, ensuite, vous créez une application nommée *mytestapp*, elle est adressable à l’adresse *mytestapp.mynewdemoase.p.azurewebsites.net*. Vous ne pouvez pas utiliser d’espace blanc dans le nom de votre ASE. Si vous utilisez des majuscules dans le nom, le nom de domaine sera la version complète de ce nom en minuscules. Si vous utilisez un ILB, le nom de votre ASE n’est pas utilisé dans votre sous-domaine mais explicitement indiqué lors de la création de l’ASE.

    ![][5]
1.  Sélectionnez votre abonnement. L’abonnement utilisé pour votre ASE est également celui avec lequel toutes les applications dans cet ASE seront créées. Vous ne pouvez pas placer votre ASE dans un réseau virtuel qui se trouve dans un autre abonnement.
1.  Sélectionnez ou spécifiez un nouveau groupe de ressources. Le groupe de ressources utilisé pour votre ASE doit être le même que celui utilisé pour votre réseau virtuel. Si vous sélectionnez un réseau virtuel, le groupe de ressources choisi pour votre ASE sera mis à jour en fonction de celui de votre réseau virtuel.

    ![][6]
1. Effectuez vos sélections de réseau virtuel et d’emplacement. Vous pouvez choisir entre créer ou sélectionner un réseau virtuel. Si vous sélectionnez un nouveau réseau virtuel, vous pouvez indiquer un nom et un emplacement. Le nouveau réseau virtuel reçoit la plage d’adresses 192.168.250.0/23 et un sous-réseau nommé **default** et défini sur la plage 192.168.250.0/24. Vous ne pouvez sélectionner qu’un réseau virtuel Resource Manager. La sélection du **type d’adresse IP virtuelle** détermine si votre ASE est accessible directement à partir d’Internet (externe) ou s’il utilise un équilibreur de charge interne (ILB). Pour en savoir plus, consultez [Create and Use an Internal Load Balancer with an App Service Environment (Créer et utiliser un équilibreur de charge interne avec un ASE)][MakeILBASE]. Si vous sélectionnez un type d’adresse IP virtuelle **Externe**, vous pouvez sélectionner le nombre d’adresses IP externes avec lesquelles le système est créé, pour le protocole SSL basé sur IP. Si vous sélectionnez **Interne**, vous devez spécifier le sous-domaine que votre ASE va utiliser. Les ASE peuvent être déployés dans les réseaux virtuels qui utilisent *soit* des plages d’adresses publiques, *soit* des espaces d’adressage RFC1918 (par exemple, des adresses privées). Pour utiliser un réseau virtuel avec une plage d’adresses publiques, vous devez créer le réseau virtuel à l’avance. Lorsque vous sélectionnez un réseau virtuel, vous devez créer un sous-réseau lors de la création de l’ASE. **Vous ne pouvez pas utiliser un sous-réseau créé au préalable dans le portail. Vous pouvez créer un ASE avec un sous-réseau existant si vous utilisez un modèle Resource Manager.** Pour créer un ASE à partir d’un modèle, consultez [Create an App Service Environment from template (Créer un ASE à partir d’un modèle)][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Vous pouvez toujours créer des instances de la première version de la fonctionnalité ASE (ASEv1). Pour ce faire, recherchez **App Service Environment v1** dans MarketPlace. Ce mode de création est identique au mode de création d’ASE autonome. Votre ASEv1 est créé avec deux serveurs frontaux et deux rôles de travail. Avec ASEv1, vous devez gérer les serveurs frontaux et les rôles de travail. Ils ne sont pas ajoutés automatiquement lors de la création de vos plans App Service. Les serveurs frontaux se comportent comme des points de terminaison HTTP/HTTPS et envoient le trafic aux rôles de travail qui hébergent vos applications. Vous pouvez en ajuster la quantité après la création de l’ASE. Pour plus d’informations sur ASEv1, consultez [Présentation de l’environnement App Service v1][ASEv1Intro]. Pour plus d’informations sur la mise à l’échelle, la gestion et la surveillance d’ASEv1, consultez [Configuration d’un environnement App Service][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

