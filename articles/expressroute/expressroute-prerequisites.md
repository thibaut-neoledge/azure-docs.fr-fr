<properties
   pageTitle="Configuration requise pour l’adoption du service ExpressRoute | Microsoft Azure"
   description="Cette page fournit une liste des exigences à respecter avant de commander un circuit Azure ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/13/2016"
   ms.author="cherylmc"/>


# Configuration requise pour ExpressRoute et liste de contrôle  

Pour vous connecter aux services cloud de Microsoft en utilisant ExpressRoute, vous devez vérifier que les conditions requises des sections ci-dessous sont remplies.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## Compte Azure

- Un compte Microsoft Azure actif et valide. Cela est nécessaire pour configurer le circuit ExpressRoute. Les circuits ExpressRoute sont des ressources au sein des abonnements Azure. Un abonnement Azure est requis même si la connectivité est limitée aux services cloud de Microsoft étrangers à Azure, tels que les services Office 365 et CRM Online.
- Un abonnement Office 365 actif (si vous utilisez les services Office 365). Consultez la section [Conditions requises spécifiques pour Office 365](#office-365-specific-requirements) de cet article pour plus d’informations.

## Fournisseur de connectivité
- Vous pouvez travailler avec un [partenaire de connectivité ExpressRoute](expressroute-locations.md#partners) pour vous connecter à Microsoft Cloud. Vous pouvez établir une connexion entre votre réseau local et Microsoft de [trois façons différentes](expressroute-introduction.md#howtoconnect). 
- Si votre fournisseur n’est pas un partenaire de connectivité ExpressRoute, vous pouvez toujours vous connecter à Microsoft Cloud par le biais d’un [fournisseur de solutions d’interconnexion de clouds](expressroute-locations.md#nonpartners).

## Configuration requise pour le réseau
- **Connectivité redondante** : il n’est pas nécessaire d’assurer la redondance de la connectivité physique entre vous et votre fournisseur. Microsoft n’exige pas la configuration de sessions BGP redondantes entre ses routeurs et les routeurs d’homologation, même lorsque vous disposez simplement d’[une seule connexion physique à une solution d’interconnexion de clouds](expressroute-faqs.md#onep2plink). 
- **Routage** : selon la façon dont vous vous connectez à Microsoft Cloud, vous ou votre fournisseur pouvez avoir besoin de configurer et gérer les sessions BGP pour les [domaines de routage](expressroute-circuit-peerings.md). Certains fournisseurs de connectivité Ethernet ou fournisseurs d’échange de Cloud peuvent proposer la gestion BGP en tant que service à valeur ajoutée.
- **Traduction d’adresses réseau (NAT)** : Microsoft accepte uniquement les adresses IP publiques par le biais de l’homologation Microsoft. Si votre réseau local comporte des adresses IP privées, vous ou votre fournisseur devez traduire les adresses IP privées en adresses IP publiques [à l’aide de la traduction d’adresses réseau](expressroute-nat.md).
- **Qualité de service (QoS)** : Skype Entreprise comprend différents services (par exemple, voix, vidéo, texte) nécessitant un traitement QoS différencié. Vous et votre fournisseur devez suivre la [configuration requise pour ExpressRoute QoS](expressroute-qos.md).
- **Sécurité réseau** : pensez à la [sécurité réseau](../best-practices-network-security.md) lorsque vous vous connectez à Microsoft Cloud par le biais d’ExpressRoute.
 
## Office 365

Si vous prévoyez d’activer Office 365 sur ExpressRoute, consultez les documents suivants pour plus d’informations sur la configuration requise pour Office 365.


- [Vue d’ensemble d’ExpressRoute pour Office 365](https://support.office.com/fr-FR/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routage avec ExpressRoute pour Office 365](https://support.office.com/fr-FR/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [URL et plages d’adresses IP Office 365.](https://support.office.com/fr-FR/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planification réseau et optimisation des performances pour Office 365](https://support.office.com/fr-FR/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Outils et calculatrices de bande passante réseau](https://support.office.com/fr-FR/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Intégration d’Office 365 aux environnements locaux](https://support.office.com/fr-FR/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## CRM Online 
Si vous prévoyez d’activer CRM Online sur ExpressRoute, consultez les documents suivants pour plus d’informations sur la configuration requise pour CRM Online.

- [Internet accessible URLs required for connectivity to Microsoft Dynamics CRM Online](https://support.microsoft.com/kb/2655102) (URL accessibles par Internet requises pour la connectivité à Microsoft Dynamics CRM Online) et [Microsoft Dynamics CRM Online IP Address Ranges](https://support.microsoft.com/kb/2728473) (Plages d’adresses IP Microsoft Dynamics CRM Online)

## Étapes suivantes

- Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).
- Recherchez un fournisseur de connectivité ExpressRoute. Consultez [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).
- Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
- Configurez votre connexion ExpressRoute.
	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuration du routage](expressroute-howto-routing-classic.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0615_2016-->