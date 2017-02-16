---
title: "Guide pratique de la Gestion des API Azure avec un réseau virtuel interne | Microsoft Docs"
description: "Découvrez comment installer et configurer la Gestion des API Azure dans un réseau virtuel interne."
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 456c541be7aadcce494bbd54e97deb6f30d5141b
ms.openlocfilehash: dc9af7e4ef9599886d1be6676f88f71f80c20474


---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>Utiliser le service Gestion des API Azure avec un réseau virtuel interne
Avec les réseaux virtuels Azure (VNET), la Gestion des API peut gérer des API inaccessibles sur Internet. Un certain nombre de technologies VPN sont disponibles pour établir la connexion et la Gestion des API peut être déployée selon deux modes principaux à l’intérieur du réseau virtuel :
* Externe
* Interne

## <a name="overview"> </a>Vue d’ensemble
Lorsque la Gestion des API est déployée en mode réseau virtuel interne, tous les points de terminaison de service (passerelle, portail des développeurs, portail des éditeurs, gestion directe et git) ne sont visibles que dans un réseau virtuel auquel vous contrôlez l’accès. Aucun point de terminaison de service n’est inscrit sur le serveur DNS Public.

Avec la Gestion des API en mode interne, vous pouvez effectuer les scénarios suivants :
* Rendre les API hébergées dans votre centre de données privé accessibles de l’extérieur en toute sécurité à des tiers à l’aide de connexions VPN ExpressRoute ou de site à site.
* Activer les scénarios de cloud hybride en exposant vos API cloud et locales par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs emplacements géographiques à l’aide d’un seul point de terminaison de passerelle. 

## <a name="enable-vpn"> </a>Créer une Gestion des API dans un réseau virtuel interne
Le service Gestion des API dans un réseau virtuel interne est hébergé derrière un équilibreur de charge interne. L’adresse IP de l’équilibreur se trouve dans la plage [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-vnet-connection-using-azure-portal"></a>Activer la connexion au réseau virtuel à l’aide du Portail Azure
Tout d’abord, créez le service Gestion des API en suivant les étapes de la section [Créer un service Gestion des API][Create API Management service]. Ensuite, configurez la Gestion des API de façon à la déployer à l’intérieur d’un réseau virtuel.

![Menu de configuration de la Gestion des API dans un réseau virtuel interne][api-management-using-internal-vnet-menu]

Une fois le déploiement réussi, l’adresse IP virtuelle interne de votre service apparaît sur le tableau de bord.

![Tableau de bord Gestion des API avec réseau virtuel interne configuré][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>Activer la connexion au réseau virtuel à l’aide d’applets de commande PowerShell
Vous pouvez également activer la connectivité de réseau virtuel à l’aide d’applets de commande PowerShell.

* **Créer un service Gestion des API au sein d’un réseau virtuel** : utilisez l’applet de commande [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) pour créer un service Gestion des API Azure au sein d’un réseau virtuel et le configurer de sorte qu’il utilise le type réseau virtuel interne.

* **Déployer un service Gestion des API existant au sein d’un réseau virtuel** : utilisez l’applet de commande [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) pour déplacer un service Gestion des API Azure existant au sein d’un réseau virtuel et le configurer de sorte qu’il utilise le type réseau virtuel interne.

## <a name="a-nameapim-dns-configurationadns-configuration"></a><a name="apim-dns-configuration"></a>Configuration DNS
Lorsque vous utilisez la Gestion des API en mode réseau virtuel externe, DNS est géré par Azure. En mode réseau virtuel interne, vous devez gérer votre propre serveur DNS.

> [!NOTE]
> Le service Gestion des API n’écoute pas les demandes entrantes sur les adresses IP. Il répond uniquement aux demandes envoyées au nom d’hôte configuré sur ses points de terminaison de service (à savoir la passerelle, le portail des développeurs, le portail des éditeurs, le point de terminaison de gestion directe et git).

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut :
Lorsque vous créez un service Gestion des API dans le cloud public Azure, nommé « contoso » par exemple, les points de terminaison de service suivants sont configurés par défaut.

>   Passerelle / Proxy : contoso.azure-api.net

> Portail des éditeurs et portail des développeurs : contoso.portal.azure-api.net

> Point de terminaison de gestion directe : contoso.management.azure-api.net

>   Git : contoso.scm.azure-api.net

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.0.0.5, vous pouvez effectuer le mappage des fichiers hôtes (%SystemDrive%\drivers\etc\hosts) de la façon suivante :

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée. Si vous utilisez un serveur DNS personnalisé dans un réseau virtuel, vous pouvez également créer des enregistrements DNS A et accéder à ces points de terminaison à partir de l’endroit de votre choix dans votre réseau virtuel. 

### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés :
Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut, vous pouvez configurer des noms de domaines personnalisés pour tous vos points de terminaison de service, comme ci-dessous :

![Configuration d’un domaine personnalisé pour la Gestion des API][api-management-custom-domain-name]

Vous pouvez ensuite créer des enregistrements A dans votre serveur DNS de façon à accéder à ces points de terminaison, qui ne sont accessibles qu’à partir de votre réseau virtuel.

## <a name="related-content"> </a>Contenu connexe
* [Problèmes courants de configuration réseau lors de l’installation de la Gestion des API dans un réseau virtuel][Common Network Configuration Issues]
* [FAQ des réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues



<!--HONumber=Jan17_HO3-->


