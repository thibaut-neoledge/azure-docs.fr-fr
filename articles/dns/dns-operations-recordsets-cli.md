<properties 
   pageTitle="Gestion des jeux d’enregistrements DNS et des enregistrements dans Azure DNS à l’aide de l’interface de ligne de commande Azure | Microsoft Azure" 
   description="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS lorsque votre domaine est hébergé dans Azure DNS. Toutes les commandes d’interface de ligne de commande pour les opérations sur les jeux d'enregistrements et les enregistrements." 
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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Gérer les enregistrements et les jeux d’enregistrements DNS en utilisant l’interface CLI


> [AZURE.SELECTOR]
- [Portail Azure](dns-operations-recordsets-portal.md)
- [Interface de ligne de commande Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Ce guide explique comment gérer des jeux d’enregistrements et des enregistrements pour votre zone DNS à l’aide de l’interface CLI Azure multiplateforme.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et les enregistrements DNS individuels. Un jeu d’enregistrements est une collection d’enregistrements dans une zone ayant le même nom et le même type. Pour plus d’informations, consultez [Présentation des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md).


## Azure DNS et l’interface CLI Azure multiplateforme

Azure DNS est un service Azure Resource Manager uniquement. Il ne possède aucune API ASM. Vous devez vérifier que l’interface CLI Azure est configurée pour utiliser le mode Resource Manager. Vous faites cela via la commande `azure config mode arm`.<BR>Si vous voyez le message « *Erreur : « dns » n’est pas une commande azure* », c’est probablement dû au fait que vous utilisez l’interface CLI Azure en mode ASM et non pas en mode Resource Manager.

## Création d’un nouveau jeu d’enregistrements et d’un enregistrement

Pour créer un jeu d’enregistrements dans le portail Azure, consultez [Créer un jeu d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md).


## Récupérer un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `azure network dns record-set show`. Spécifiez le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements et le type d’enregistrement. Utilisez l’exemple ci-dessous en remplaçant les valeurs par les vôtres.

	azure network dns record-set show myresourcegroup contoso.com www A


## Liste des jeux d'enregistrements

Vous pouvez répertorier tous les enregistrements d’une zone DNS en utilisant la commande `azure network dns record-set list`. Vous devez spécifier le nom du groupe de la ressource et le nom de la zone.

### Pour répertorier tous les jeux d’enregistrements

Cet exemple retourne tous les jeux d’enregistrements, quel que soit le nom ou le type d’enregistrement :

	azure network dns record-set list myresourcegroup contoso.com

### Pour répertorier les jeux d’enregistrements d’un type donné

Cet exemple retourne tous les jeux d’enregistrements correspondant au type d’enregistrement donné (dans ce cas, les enregistrements A).

	azure network dns record-set list myresourcegroup contoso.com A 


## Ajout d’un enregistrement à un jeu d'enregistrements

Les enregistrements sont ajoutés aux jeux d’enregistrements à l’aide de la commande `azure network dns record-set add-record`. Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type de jeu d'enregistrements. Par exemple, si vous utilisez un jeu d’enregistrements de type *A*, vous pourrez spécifier seulement des enregistrements avec le paramètre `-a <IPv4 address>`.

Pour créer un jeu d’enregistrements, utilisez `azure network dns record-set create`. Spécifiez le groupe de ressources, le nom de la zone, le nom relatif du jeu d’enregistrements, le type d’enregistrement et la durée de vie (TTL). Si le paramètre --ttl n’est pas défini, la valeur par défaut est 4 (en secondes).
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Après avoir créé le jeu d’enregistrements A, ajoutez l’adresse IPv4 en utilisant `azure network dns record-set add-record`.

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 


Les exemples suivants montrent comment créer un jeu d'enregistrements de chaque type d'enregistrement contenant un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## Mettre à jour un enregistrement dans un jeu d’enregistrements

### Pour ajouter une autre adresse IP (1.2.3.4) à un jeu d’enregistrements A (www) existant : 

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK

### Pour supprimer une valeur existante d’un jeu d’enregistrements, utilisez `azure network dns record-set delete-record`.
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:    IPv4 address                    : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Suppression d’un enregistrement d’un jeu d’enregistrements

Les enregistrements peuvent être supprimés d’un jeu d’enregistrements avec `azure network dns record-set delete-record`. L’enregistrement à supprimer doit correspondre exactement à un enregistrement existant relativement à tous les paramètres.

La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements. Pour plus d’informations, consultez la section [Supprimer un jeu d’enregistrements](#delete) de cet article.

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Supprimer un enregistrement AAAA d’un jeu d’enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Supprimer un enregistrement CNAME d’un jeu d’enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Supprimer un enregistrement MX d’un jeu d’enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Supprimer un enregistrement NS d’un jeu d’enregistrements
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Supprimer un enregistrement SRV d’un jeu d’enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Supprimer un enregistrement TXT d’un jeu d’enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Supprimer un jeu d’enregistrements

Les jeux d’enregistrements peuvent être supprimés avec l’applet de commande `Remove-AzureRmDnsRecordSet`. Vous ne pouvez pas supprimer les jeux d’enregistrements SOA et NS à l’apex (name = '@') qui ont été créés automatiquement lors de la création de la zone. Ils sont supprimés automatiquement lors de la suppression de la zone.

Dans l’exemple ci-dessous, le jeu d’enregistrements A « test-a » sera supprimé de la zone DNS contoso.com :

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

Le commutateur facultatif *-q* peut être utilisé pour supprimer l’invite de confirmation.


## Étapes suivantes

Pour plus d’informations sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [Création de zones et de jeux d’enregistrements DNS à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).

Si vous voulez utiliser des enregistrements DNS inversés, consultez [Comment gérer les enregistrements DNS inversés](dns-reverse-dns-record-operations-cli.md).




 

<!---HONumber=AcomDC_0518_2016-->