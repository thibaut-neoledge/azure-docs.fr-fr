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


Microsoft Azure Application Gateway fournit une solution d’équilibrage de la charge HTTP gérée par Azure et basée sur l’équilibrage de la charge de couche 7. L’équilibrage de la charge d’application permet aux administrateurs informatiques et aux développeurs de créer des règles de routage pour le trafic réseau basé sur HTTP. Le service Application Gateway est hautement disponible et contrôlé. Pour le contrat SLA et la tarification, reportez-vous aux pages [SLA](http://azure.microsoft.com/support/legal/sla/) et [Tarification](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway prend actuellement en charge la remise d'application de couche 7 pour les éléments suivants :

- Équilibrage de charge HTTP
- Affinité de session basée sur les cookies
- Déchargement SSL

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Équilibrage de charge de couche 7 HTTP

Azure fournit un équilibrage de la charge de couche 4 via l’équilibreur de charge Azure fonctionnant au niveau du transport (TCP/UDP) et via l’équilibrage de la charge de tout le trafic réseau entrant pour le service App Gateway. La passerelle Application Gateway applique ensuite les règles de routage au trafic HTTP, fournissant ainsi un équilibrage de la charge de niveau 7 (HTTP). Quand vous créez une passerelle Application Gateway, un point de terminaison (VIP) est associé et utilisé comme adresse IP publique pour le trafic réseau en entrée.

La passerelle Application Gateway achemine le trafic HTTP en fonction de sa configuration, qu’il s’agisse d’un ordinateur virtuel, d’un service cloud, d’une application web ou d’une adresse IP externe.

Le diagramme ci-dessous explique comment le trafic circule pour la passerelle Application Gateway : ![Application Gateway2](./media/application-gateway-introduction/appgateway2.png)

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

Créer une passerelle Application Gateway. Consultez [Création d’une passerelle Application Gateway](application-gateway-create-gateway.md).

Configurer le déchargement SSL. Consultez [Configuration du déchargement SSL avec une passerelle Application Gateway](application-gateway-ssl.md).

<!---HONumber=August15_HO6-->