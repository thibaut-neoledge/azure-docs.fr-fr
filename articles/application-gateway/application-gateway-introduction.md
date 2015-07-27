<properties 
   pageTitle="Introduction à Application Gateway | Microsoft Azure"
   description="Cette page fournit une vue d'ensemble du service Application Gateway pour l'équilibrage de charge de couche 7, y compris les tailles de passerelle, l'équilibrage de charge HTTP, l'affinité de session basée sur les cookies et le déchargement SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# Vue d'ensemble technique d'Application Gateway 


Microsoft Azure Application Gateway est un service géré par Azure semblable à Azure VPN Gateway. Application Gateway fournit une solution d'équilibrage de charge HTTP basée sur ARR (Application Request Routing) IIS géré par Azure. Le service Application Gateway est hautement disponible et contrôlé. Pour le contrat SLA et la tarification, reportez-vous aux pages [SLA](http://azure.microsoft.com/support/legal/sla/) et [Tarification](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway prend actuellement en charge la remise d'application de couche 7 pour les éléments suivants :

- Équilibrage de charge HTTP
- Affinité de session basée sur les cookies
- Déchargement SSL

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Équilibrage de charge de couche 7 HTTP
Azure fournit l'équilibrage de charge de couche 4 par le biais d'un équilibreur de charge logiciel. Ceci se produit implicitement pour chaque service cloud qui a une adresse VIP (public ou interne) avec équilibrage de charge. Toutefois, il existe de nombreuses applications qui peuvent utiliser l'équilibrage de charge de couche 7 (HTTP).


L'équilibrage de charge de couche 7 HTTP est utile pour :


- Les applications qui demandent des requêtes à partir de la même session client/utilisateur pour atteindre la même machine virtuelle principale. Exemples : applications de panier d'achat et serveurs de messagerie.
- Les applications qui veulent libérer les batteries de serveurs web de la surcharge de terminaison SSL.
- Les applications, telles que CDN, qui demandent plusieurs requêtes HTTP sur la même connexion TCP de longue durée pour être acheminées/à charge équilibrée sur différents serveurs principaux.

## Instances et tailles de passerelle

Application Gateway est actuellement disponible en 3 tailles : Small (petite), Medium (moyenne) et Large (grande). Les instances de petite taille sont conçues pour les scénarios de développement et de test.

Vous pouvez créer jusqu'à 10 passerelles Application Gateway par abonnement et chacune peut contenir jusqu'à 10 instances. L'équilibrage de charge Application Gateway sous forme de service géré par Azure permet la mise en service d'un équilibreur de charge de couche 7 derrière l'équilibreur de charge logiciel Azure.

## Configuration et gestion

Vous pouvez créer et gérer la passerelle Application Gateway à l'aide des API REST et des applets de commande PowerShell.

## Étapes suivantes

Créer une passerelle Application Gateway. Voir [Création d'une passerelle Application Gateway](application-gateway-create-gateway.md).

Configurer le déchargement SSL. Voir [Configuration du déchargement SSL avec une passerelle Application Gateway](application-gateway-ssl.md).

<!---HONumber=July15_HO3-->