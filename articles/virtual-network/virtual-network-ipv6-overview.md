<properties 
   pageTitle="Présentation du protocole IPv6 | Microsoft Azure"
   description="En savoir plus sur l’adressage IPv6 dans Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# Prise en charge du protocole IPv6 dans Azure

Le protocole IPv6 a été initialement introduit en 1998 par l’Internet Engineering Task Force (IETF) pour résoudre la limitation des adresses IPv4 32 bits sur l’Internet public. Avec l’augmentation du nombre d’appareils mobiles connectés à Internet et l’avènement de l’Internet des objets (IoT), les adresses IPv6 sont de plus en plus fréquentes sur l’Internet public.

>[AZURE.IMPORTANT] IPv6 dans Azure est actuellement en version préliminaire privée et pas accessible pour tous les clients. Cette page sera mise à jour lorsque la fonctionnalité sera disponible pour tous les clients.

Vous pouvez utiliser des adresses IPv6 sur vos machines virtuelles IaaS Azure dans les déploiements Resource Manager pour la connectivité de trafic entrant et sortant à l’Internet public. La liste suivante décrit les fonctionnalités d’IPv6 dans Azure.

- **Machines virtuelles IaaS dans Resource Manager**
	- Les machines virtuelles disposent à la fois d’un point de terminaison IPv4 et IPv6.
	- Les machines virtuelles peuvent se connecter à Internet par le biais d’une adresse IP publique d’équilibreur de charge pour la connectivité de trafic entrant (équilibreur de charge et NAT) et les connexions sortantes (SNAT).
	- Les machines virtuelles se connectent à tous les services Azure à l’aide de leurs points de terminaison IPv4.
	- Les machines virtuelles utilisent uniquement les points de terminaison IPv4 pour la communication est-ouest.
- **DNS Azure**
	- Azure DNS prend en charge IPv6 et fournit des enregistrements AAAA, ainsi que des enregistrements IPv4 A.
	- Azure DNS répond aux requêtes avec les enregistrements AAAA et A pour les recherches, lorsque les deux existent. 
- **Office365**
	- Les services Office 365 prennent en charge IPv6.
	- Vous pouvez vous connecter à partir de votre réseau local aux ressources IPv6 dans Office 365 à l’aide d’un circuit ExpressRoute.

## Machines virtuelles IaaS dans Resource Manager

Vous ne pouvez profiter de la connectivité IPv6 qu’à l’aide du modèle de déploiement Resource Manager. Bien que chaque machine virtuelle dans un déploiement Resource Manager a des points de terminaison IPv4 et IPv6, le point de terminaison IPv6 ne peut être utilisé que pour la connexion avec le réseau Internet public via un équilibreur de charge, comme indiqué ci-dessous.

![Connectivité IPv6](./media/virtual-network-ipv6-overview/figure1.png)

## DNS Azure

Vous pouvez héberger des enregistrements IPv6 AAAA dans Azure DNS. Quand Azure DNS reçoit une requête pour une ressource, il répond avec tous les enregistrements disponibles pour le nom d’hôte. Par exemple, si vous avez un enregistrement A et un enregistrement AAAA pour un nom d’hôte donné (par exemple, www.contoso.com), Azure DNS envoie les deux adresses en réponse à la requête. Il incombe au client DNS de décider d’utiliser le point de terminaison IPv4 ou IPv6 pour la connectivité, lorsqu’il reçoit la réponse d’Azure DNS ou de tout autre serveur DNS qui prend en charge les enregistrements AAAA.

>[AZURE.NOTE] Les ordinateurs Windows essaient toujours de se connecter à l’aide du protocole IPv6 avant de tenter une connexion IPv4.

## Office 365

Vous pouvez utiliser la connectivité IPv6 entre votre réseau local et Office 365 à l’aide d’un circuit ExpressRoute et de l’homologation Microsoft. Veillez à bien comprendre le [fonctionnement d’ExpressRoute](../expressroute/expressroute-introduction.md) dans le cadre de l’homologation Microsoft.

## Étapes suivantes

- En savoir plus sur [Azure DNS](../dns/dns-overview.md).
- En savoir plus sur [ExpressRoute](../expressroute/expressroute-introduction.md).

<!------HONumber=AcomDC_0309_2016-->