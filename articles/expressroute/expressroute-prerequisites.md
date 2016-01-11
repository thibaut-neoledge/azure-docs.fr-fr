<properties
   pageTitle="Configuration requise pour l’adoption du service ExpressRoute | Microsoft Azure"
   description="Cette page fournit une liste des exigences à respecter avant de commander un circuit Azure ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc"/>


# Configuration requise pour ExpressRoute   

Pour vous connecter aux services cloud de Microsoft en utilisant ExpressRoute, vous devez vérifier que les conditions requises des sections ci-dessous sont remplies.

## Conditions requises pour le compte

- Un compte Microsoft Azure actif et valide. Cela est nécessaire pour configurer le circuit ExpressRoute. Les circuits ExpressRoute sont des ressources au sein des abonnements Azure. Un abonnement Azure est requis même si la connectivité est limitée aux services cloud de Microsoft étrangers à Azure, tels que les services Office 365 et CRM Online.
- Un abonnement Office 365 actif (si vous utilisez les services Office 365). Consultez la section [Conditions requises spécifiques pour Office 365](#office-365-specific-requirements) de cet article pour plus d’informations.

## Partenariat avec un fournisseur de connectivité

- Un partenariat avec un fournisseur de connectivité issu de la liste prise en charge par l’intermédiaire duquel la connectivité doit être facilitée. Vous devez disposer d’un partenariat commercial existant avec un fournisseur de connectivité. Vous devez vous assurer que le service dont vous bénéficiez avec le fournisseur de connectivité est compatible avec ExpressRoute.
- Si vous souhaitez utiliser un fournisseur de connectivité qui ne figure pas dans la liste prise en charge, vous pouvez tout de même créer une connexion aux services cloud de Microsoft via un emplacement Exchange.
	- Vérifiez auprès de votre fournisseur de connectivité s’il est présent dans l’un des emplacements Exchange apparaissant dans la liste prise en charge.
	- Demandez au fournisseur de connectivité d’étendre votre réseau à l’emplacement Exchange de votre choix.
	- Commandez un circuit ExpressRoute avec l’emplacement Exchange en tant que fournisseur de connectivité.

## Connectivité physique entre votre réseau et le fournisseur de connectivité

Reportez-vous à la section Modèles de connectivité pour plus d’informations sur les modèles de connectivité. Les clients doivent s’assurer que leur infrastructure locale est physiquement connectée à l’infrastructure du prestataire de services par le biais de l’un des modèles décrits.

## Conditions requises de redondance pour la connectivité

Il n’existe aucune condition requise de redondance sur la connectivité physique entre l’infrastructure du client et l’infrastructure du prestataire de services. Microsoft a besoin de redondance dans la Couche 3. Microsoft a besoin qu’un routage redondant soit configuré entre la périphérie de Microsoft et le réseau du client via le prestataire de services, afin que chacune des homologations soit activée. Si les sessions de routage ne sont pas configurées de manière redondante, l’accord de niveau de service relatif à la disponibilité des services est nul et non avenu.

## Considérations relatives aux adresses IP et au routage

Les clients et les fournisseurs de connectivité sont responsables de la configuration de sessions BGP redondantes avec l’infrastructure de périphérie de Microsoft. Les clients qui choisissent de se connecter via des fournisseurs de réseaux VPN IP s’appuient généralement sur les fournisseurs de connectivité pour gérer les configurations de routage. Les clients colocalisés avec un emplacement Exchange ou se connectant à Microsoft via un fournisseur Ethernet de point à point doivent configurer des sessions BGP redondantes par homologation afin de répondre aux conditions requises de l’accord de niveau de service relatif à la disponibilité. Les fournisseurs de connectivité peuvent proposer cette option en tant que service à valeur ajoutée. Reportez-vous à la table des domaines de routage dans l’article [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md) pour plus d’informations sur les limites.

## Sécurité et pare-feu

Reportez-vous au document, [Services cloud et sécurité réseau de Microsoft](../best-practices-network-security.md) pour des informations relatives à la sécurité et aux pare-feu.

## Configuration NAT pour homologations Azure public et Microsoft

Reportez-vous à [Conditions requises de traductions d’adresses réseau ExpressRoute](expressroute-nat.md) pour obtenir des instructions détaillées sur les conditions requises et les configurations. Vérifiez auprès de votre fournisseur de connectivité s’il gère la configuration et la gestion de la traduction d’adresses réseau pour vous. En règle générale, les fournisseurs de connectivité de Couche 3 gèrent la traduction d’adresses réseau pour vous.

## Conditions requises spécifiques pour Office 365

Passez en revue les ressources suivantes pour plus d’informations sur les conditions requises pour Office 365.

- [Planification réseau et optimisation des performances pour Office 365](http://aka.ms/tune)
- [Gestion du trafic réseau Office 365](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- Reportez-vous à l’article [Conditions requises de qualité de service ExpressRoute](expressroute-qos.md) pour des instructions détaillées sur les conditions requises et les configurations relatives à la qualité de service. Vérifiez auprès de votre fournisseur de connectivité s’il offre plusieurs classes de services pour votre réseau VPN. 

## Étapes suivantes

- Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).
- Recherchez un fournisseur de services. Consultez [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).
- Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
- Configurez votre connexion ExpressRoute.
	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuration du routage](expressroute-howto-routing-classic.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_1223_2015-->