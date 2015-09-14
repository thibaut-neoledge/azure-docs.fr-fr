<properties 
   pageTitle="Définition des paramètres DNS dans un fichier de configuration de service | Microsoft Azure"
	description="spécification de paramètres DNS personnalisés à l'aide du fichier de configuration de service d’un réseau virtuel"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="joaoma"/>

# Définition des paramètres DNS dans un fichier de configuration de service

## Éléments DNS

Un fichier de configuration de service peut contenir un élément DnsServers avec une liste d’adresses IPv4 dédiée aux serveurs DNS que le service utilisera. Les paramètres du fichier de configuration de service priment sur les paramètres du fichier de configuration de réseau. Pour plus d’informations, consultez la rubrique [Schéma de configuration de service Azure (.cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Élément NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING]L’attribut **name** de l’élément **DnsServer** est utilisé uniquement en tant que nom de référence. Il ne représente aucunement le nom d’hôte attribué au serveur DNS. Chaque valeur d’attribut **DnsServer** doit être unique au sein de l’abonnement Microsoft Azure.

## Voir aussi

[Schéma de configuration de service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma de configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](http://go.microsoft.com/fwlink/?LinkId=248094)

[À propos des paramètres de réseau virtuel dans le Portail de gestion](http://go.microsoft.com/fwlink/?LinkId=248092)

<!---HONumber=September15_HO1-->