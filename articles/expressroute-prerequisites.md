<properties 
   pageTitle="Configuration requise pour l'adoption du service ExpressRoute"
   description="Cette page fournit une liste des exigences à respecter avant de commander un circuit ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# Configuration requise pour ExpressRoute  

Pour vous connecter aux services cloud de Microsoft en utilisant ExpressRoute, vous devez vérifier que les conditions préalables suivantes sont remplies :

## Configuration requise pour la connectivité

- Un compte Microsoft Azure actif et valide
- Un partenariat avec un fournisseur de services réseau ou un fournisseur Exchange à partir de la [liste prise en charge](expressroute-locations.md) par l'intermédiaire duquel la connectivité doit être facilitée. Vous devez avoir établi une relation commerciale avec le fournisseur de services réseau ou le fournisseur Exchange. Vous devez vous assurer que le service que vous utilisez est compatible avec ExpressRoute. 
- Si vous souhaitez faire appel à un fournisseur de services réseau qui ne figure pas sur cette liste, vous pouvez toujours établir une connexion à Azure. 
	- Vérifiez si votre fournisseur de services réseau est présent dans l’un des emplacements Exchange répertoriés ci-dessus.
	- Demandez à votre fournisseur de services réseau d’étendre votre réseau à l’emplacement Exchange de votre choix.
	- Commandez un circuit ExpressRoute via le fournisseur Exchange pour vous connecter à Azure.
- Une connectivité à l'infrastructure de votre fournisseur de services. Votre situation doit correspondre à un (ou plusieurs) des points suivants :
	- Vous êtes un client VPN d’un fournisseur de services réseau et vous avez au moins un site local connecté à l'infrastructure VPN de ce fournisseur. Vérifiez auprès de votre fournisseur de services réseau si votre service VPN répond à la configuration requise pour ExpressRoute
	- Votre infrastructure est colocalisée dans le centre de données du fournisseur Exchange.
	- Vous disposez d’une connectivité Ethernet à l'infrastructure du réseau Ethernet Exchange du fournisseur Exchange.	
- Des adresses IP et des numéros de système autonome (AS) pour la configuration de routage. 
	- Vous pouvez utiliser des numéros de système autonome (AS) privés pour vous connecter à un domaine de routage pour l’homologation privée Azure. Si vous choisissez de procéder ainsi, ces numéros doivent être > 65 000. Pour plus d'informations sur les numéros AS, consultez [Numéros de système autonome (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Des adresses IP pour configurer des itinéraires. Un sous-réseau/28 est requis. Il ne doit pas chevaucher les plages d'adresses IP utilisées dans votre réseau local ou dans Azure.
	- Vous devez utiliser vos propres numéros de système autonome (AS) publics pour la configuration des sessions BGP avec les services publics Azure.

## Étapes suivantes

- Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).
- Pour plus d'informations sur la façon de configurer votre connexion ExpressRoute, consultez 
	- [Configuration d’une connexion ExpressRoute via un fournisseur de services réseau](expressroute-configuring-nsps.md)
	- [Configuration d’une connexion ExpressRoute via un fournisseur Exchange](expressroute-configuring-exps.md)
<!--HONumber=54-->