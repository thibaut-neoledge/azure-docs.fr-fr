<properties 
   pageTitle="Guide de résolution des problèmes d’ExpressRoute - Obtention de tables ARP | Microsoft Azure"
   description="Cette page fournit des instructions sur l’obtention des tables ARP pour un circuit ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/06/2016"
   ms.author="ganesr"/>

#Guide de résolution des problèmes d’ExpressRoute - Obtention de tables ARP dans le modèle de déploiement Classic

> [AZURE.SELECTOR]
[PowerShell - Resource Manager](expressroute-troubleshooting-arp-resourcemanager.md)
[PowerShell - Classic](expressroute-troubleshooting-arp-classic.md)

Cet article vous guide tout au long des étapes d’apprentissage des tables ARP pour votre circuit ExpressRoute.

>[AZURE.IMPORTANT] Ce document a pour objet de vous aider à diagnostiquer et résoudre les problèmes simples. Il n’a pas pour objet de remplacer la prise en charge de Microsoft. Si vous ne parvenez pas à résoudre le problème en suivant les conseils ci-dessous, ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## Protocole ARP (Address Resolution Protocol) et tables ARP
Le protocole ARP (Address Resolution Protocol) est un protocole de couche 2 défini dans [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper l’adresse Ethernet (adresse MAC) avec une adresse IP.

La table ARP fournit un mappage de l’adresse ipv4 et une adresse MAC pour une homologation particulière. La table ARP d’une homologation de circuit ExpressRoute fournit les informations suivantes pour chaque interface (principale et secondaire)

1. Mappage de l’adresse IP de l’interface du routeur local sur l’adresse MAC
2. Mappage de l’adresse IP de l’interface du routeur ExpressRoute sur l’adresse MAC
3. Âge du mappage

Les tables ARP permettent de valider la configuration de la couche 2 et de résoudre les problèmes de connectivité de base de la couche 2.

Exemple de table ARP :

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La section suivante fournit des informations sur l’affichage des tables ARP vues par les routeurs de bordure ExpressRoute.

## Conditions préalables à l’apprentissage des tables ARP

Assurez-vous que vous disposez des éléments suivants avant de poursuivre

 - Un circuit ExpressRoute valide configuré avec au moins une homologation. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) devez avoir configuré au moins une des homologations (Azure privé, Azure public et Microsoft) sur ce circuit.
 - Plages d’adresses IP utilisées pour configurer les homologations (Azure privé, Azure public et Microsoft). Passez en revue les exemples d’affectation d’adresses IP dans la [page de configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour comprendre de quelle manière les adresses IP sont mappées sur les interfaces de votre côté et du côté d’ExpressRoute. Vous pouvez obtenir plus d’informations sur la configuration d’homologation en examinant la [page de configuration d’homologation ExpressRoute](expressroute-howto-routing-classic.md).
 - Informations de votre équipe réseau/fournisseur de connectivité sur les adresses MAC des interfaces utilisées avec ces adresses IP.
 - Vous devez disposer du dernier module PowerShell pour Azure (version 1.50 ou plus récente).

## Obtention des tables ARP pour votre circuit ExpressRoute
Cette section fournit des instructions sur la manière d’afficher les tables ARP par homologation à l’aide de PowerShell. Vous ou votre fournisseur de connectivité devez avoir configuré l’homologation avant de poursuivre. Chaque circuit a deux chemins d’accès (principal et secondaire). Vous pouvez contrôler indépendamment la table ARP de chaque chemin d’accès.

### Tables ARP pour l’homologation privée Azure
L’applet de commande suivante fournit les tables ARP pour l’homologation privée Azure

		# Required Variables
		$ckt = "<your Service Key here>
		
		# ARP table for Azure private peering - Primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary
		
		# ARP table for Azure private peering - Secodary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary 

Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### Tables ARP pour l’homologation publique Azure
L’applet de commande suivante fournit les tables ARP pour l’homologation publique Azure

		# Required Variables
		$ckt = "<your Service Key here>
		
		# ARP table for Azure public peering - Primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary
		
		# ARP table for Azure public peering - Secodary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary 

Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           64.0.0.1 ffff.eeee.dddd
		  0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### Tables ARP pour l’homologation Microsoft
L’applet de commande suivante fournit les tables ARP de l’homologation Microsoft

    # ARP table for Microsoft peering - Primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering - Secodary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary 


Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## Utilisation de ces informations
La table ARP d’une homologation peut servir à valider la connectivité et la configuration de la couche 2. Cette section fournit une vue d’ensemble de l’aspect des tables ARP dans différents scénarios.

### Table ARP lorsqu’un circuit est dans un état opérationnel (état attendu)

 - La table ARP aura une entrée pour le côté local avec une adresse IP valide et une adresse MAC, ainsi qu’une entrée similaire pour le côté Microsoft. 
 - Le dernier octet de l’adresse IP locale sera toujours un nombre impair.
 - Le dernier octet de l’adresse IP Microsoft sera toujours un nombre pair.
 - La même adresse MAC s’affichera côté Microsoft pour les 3 homologations (principales/secondaires). 


		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### Table ARP en cas de problèmes côté fournisseur de connectivité/local

 - Une seule entrée apparaîtra dans la table ARP. Cette commande affiche le mappage entre l’adresse MAC et l’adresse IP utilisée côté Microsoft. 

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Ouvrez une demande de support avec votre fournisseur de connectivité pour déboguer ces problèmes.


### Table ARP en cas de problèmes côté Microsoft

 - Aucune table ARP ne s’affiche pour une homologation en cas de problèmes côté Microsoft. 
 -  Ouvrez un incident auprès du [support technique Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifiez que vous avez un problème au niveau de la connectivité de couche 2. 

## Étapes suivantes

 - Valider les configurations de couche 3 pour votre circuit ExpressRoute
	 - Obtenir un récapitulatif d’itinéraires pour déterminer l’état des sessions BGP 
	 - Obtenir une table d’itinéraires pour déterminer quels préfixes sont publiés sur ExpressRoute
 - Valider le transfert des données en examinant les octets en entrée/sortie
 - Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.

<!---HONumber=AcomDC_0615_2016-->