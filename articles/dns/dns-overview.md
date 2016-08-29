<properties 
   pageTitle="Vue d’ensemble d’Azure DNS | azure.microsoft.com/ Azure" 
   description="Vue d’ensemble des services d’hébergement Azure DNS sur azure.microsoft.com/ Azure Héberger votre domaine sur azure.microsoft.com/ Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Vue d’ensemble d’Azure DNS


Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure azure.microsoft.com/ Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.


Les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Nous utilisons la mise en réseau Anycast, afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Cette technique offre des performances élevées et une haute disponibilité pour votre domaine.

Le service Azure DNS est basé sur Azure Resource Manager (ARM). Ainsi, il tire parti de fonctionnalités ARM telles que le contrôle d’accès en fonction du rôle, les journaux d’audit et le verrouillage de ressources. Vos domaines et enregistrements peuvent être gérés via le portail Azure, des applets de commande Azure PowerShell et l’interface CLI Azure multiplateforme. Les applications nécessitant une gestion automatique de DNS peuvent s’intégrer au service par le biais de l’API REST et des Kits de développement logiciel (SDK).

Azure DNS ne prend actuellement pas en charge l'achat de noms de domaine. Si vous voulez acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers. Le bureau d’enregistrement facture généralement des frais annuels peu élevés. Les domaines peuvent être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md).


## Étapes suivantes

[Création d’une zone DNS](dns-getstarted-create-dnszone-portal.md)




 

<!---HONumber=AcomDC_0817_2016-->