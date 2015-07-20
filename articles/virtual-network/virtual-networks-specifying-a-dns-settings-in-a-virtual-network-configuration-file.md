<properties 
   pageTitle="Définition des paramètres DNS dans un fichier de configuration de réseau virtuel"
   description="Description"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# Définition des paramètres DNS dans un fichier de configuration de réseau virtuel

Un fichier de configuration réseau possède deux éléments que vous pouvez utiliser pour définir les paramètres DNS : **DnsServers** et **DnsServerRef**. Vous pouvez ajouter une liste de serveurs DNS en définissant leur adresse IP et leur nom de référence sur l’élément **DnsServers**. Ensuite, utilisez un élément **DnsServerRef** afin de spécifier les entrées du serveur DNS de l’élément DnsServers qui sont utilisées pour différents sites au sein de votre réseau virtuel.

>[AZURE.IMPORTANT]Pour plus d’informations sur la configuration du fichier de configuration réseau, consultez la section [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](https://msdn.microsoft.com/library/azure/jj156097.aspx). Pour plus d’informations sur les éléments contenus dans le fichier de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Le fichier de configuration réseau peut contenir les éléments suivants. Le titre de chaque élément est associé à une page qui fournit des informations supplémentaires sur les paramètres de valeurs de l’élément.

[Élément Dns](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]L’attribut **name** de l’élément **DnsServer** est utilisé uniquement en tant que référence pour l’élément **DnsServerRef**. Il ne représente aucunement le nom d’hôte attribué au serveur DNS. Chaque attribut **DnsServer** doit être unique au sein de l’abonnement Microsoft Azure

[Élément de sites de réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE]Pour définir ce paramètre pour l’élément de sites de réseau virtuel, vous devez préalablement le définir dans l’élément DNS. L’attribut *name* DnsServerRef de l’élément de sites de réseau virtuel doit faire référence à une valeur de nom spécifiée dans l’élément DNS pour l’attribut *name* DnsServer.

## Voir aussi

[Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](http://go.microsoft.com/fwlink/?LinkId=248094)

[Schéma de configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Schéma de configuration de service Microsoft Azure](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=July15_HO2-->