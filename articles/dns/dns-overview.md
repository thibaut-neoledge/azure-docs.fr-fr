<properties 
   pageTitle="Vue d’ensemble d’Azure DNS | Microsoft Azure"
	description="Vue d'ensemble des services d’hébergement Azure DNS dans Microsoft Azure et hébergement de votre domaine sur Microsoft Azure"
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="joaoma"/>

# Vue d’ensemble d’Azure DNS

Derrière un site web ou un service sur Internet, il existe une adresse IP. Par exemple, www.microsoft.com utilise l'adresse IP 134.170.185.46. Le DNS (Domain Name System) se charge de traduire (ou résoudre) le nom du site web ou du service en une adresse IP.

Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d'identification, API, outils et facturation que vos autres services Azure.

Les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Nous utilisons la mise en réseau Anycast, afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Cette technique offre des performances élevées et une haute disponibilité pour votre domaine.

Le service est basé sur Azure Resource Manager (ARM). Vos domaines et vos enregistrements peuvent être gérés via les API REST d’Azure Resource Manager, le Kit de développement logiciel (SDK) .NET, les cmdlets PowerShell et l’interface en ligne de commande. Azure DNS est actuellement en version préliminaire et n'est pas encore pris en charge dans le portail de gestion Azure.<BR><BR> Azure DNS ne prend actuellement pas en charge l'achat de noms de domaine. Pour acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers qui facturera généralement un tarif annuel minime. Ces domaines peuvent être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Consultez [Délégation d’un domaine dans Azure DNS](dns-domain-delegation.md) pour plus d’informations.


## Étapes suivantes

[Prise en main de la création de zones DNS](dns-getstarted-create-dnszone.md)

[Automatisation des opérations Azure avec le Kit de développement (SDK) .NET](dns-sdk.md)

[Référence de l'API REST d’Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=August15_HO9-->