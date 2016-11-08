---
title: Définition des paramètres DNS dans un fichier de configuration de réseau virtuel | Microsoft Docs
description: Comment modifier les paramètres de serveur DNS dans un réseau virtuel à l’aide d’un fichier de configuration de réseau virtuel dans le modèle de déploiement classique
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial

---
# Définition des paramètres DNS dans un fichier de configuration de réseau virtuel
Un fichier de configuration réseau possède deux éléments que vous pouvez utiliser pour définir les paramètres DNS : **DnsServers** et **DnsServerRef**. Vous pouvez ajouter une liste de serveurs DNS en définissant leur adresse IP et leur nom de référence sur l’élément **DnsServers**. Ensuite, utilisez un élément **DnsServerRef** afin de spécifier les entrées du serveur DNS de l’élément DnsServers qui sont utilisées pour différents sites au sein de votre réseau virtuel.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique.

Le fichier de configuration réseau peut contenir les éléments suivants. Le titre de chaque élément est associé à une page qui fournit des informations supplémentaires sur les paramètres de valeurs de l’élément.

> [!IMPORTANT]
> Pour plus d'informations sur la configuration du fichier de configuration réseau, consultez la section [Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur les éléments contenus dans le fichier de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Élément Dns](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> L’attribut **name** de l’élément **DnsServer** est utilisé uniquement en tant que référence pour l’élément **DnsServerRef**. Il ne représente aucunement le nom d’hôte attribué au serveur DNS. Chaque attribut **DnsServer** doit être unique au sein de l’abonnement Microsoft Azure
> 
> 

[Élément de sites de réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Pour définir ce paramètre pour l’élément de sites de réseau virtuel, vous devez préalablement le définir dans l’élément DNS. L’attribut *name* DnsServerRef de l’élément de sites de réseau virtuel doit faire référence à une valeur de nom spécifiée dans l’élément DNS pour l’attribut *name* DnsServer.
> 
> 

## Étapes suivantes
* Découvrez le [schéma de configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
* Découvrez le [schéma de configuration du service Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0810_2016-->