<properties 
   pageTitle="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS à l’aide de l’interface de ligne de commande | Microsoft Azure" 
   description="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS lorsque votre domaine est hébergé dans Azure DNS. Toutes les commandes d’interface de ligne de commande pour les opérations sur les jeux d'enregistrements et les enregistrements." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Gestion des enregistrements DNS à l'aide de l’interface de ligne de commande

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)

Ce guide explique comment gérer des jeux d'enregistrements et des enregistrements pour votre zone DNS.

>[AZURE.NOTE] Azure DNS est un service Azure Resource Manager uniquement. Il ne possède aucune API ASM. Par conséquent, vous devez vous assurer que l’interface CLI Azure est configurée pour le mode Gestionnaire de ressources, à l’aide de la commande « azure config mode arm ».

>Si vous voyez « erreur: « dns » n’est pas une commande azure », cela est probablement dû au fait que vous utilisez Azure CLI en mode ASM et non en mode Gestionnaire de ressources.

Il est important de comprendre la différence entre les jeux d'enregistrements DNS et les enregistrements DNS individuels. Un jeu d'enregistrements est une collection d'enregistrements dans une zone ayant le même nom et le même type. Pour plus d'informations, consultez [Compréhension des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Création d’un jeu d'enregistrements

Des jeux d’enregistrements sont créés à l’aide de la commande `azure network dns record-set create`. Vous devez indiquer le nom du jeu d’enregistrements, la zone, la durée de vie (TTL) et le type d'enregistrement.

Le nom du jeu d'enregistrements doit être un nom relatif, à l'exclusion du nom de la zone. Par exemple, le nom du jeu d'enregistrements « www » dans la zone « contoso.com » créera un jeu d’enregistrements avec le nom complet « www.contoso.com ».

Pour un jeu d'enregistrements à l'apex de la zone, utilisez "@" comme nom du jeu d'enregistrements, guillemets compris. Le nom complet du jeu d'enregistrements est donc égal au nom de la zone, dans cet exemple, « contoso.com ».

Azure DNS prend en charge les types d'enregistrements suivants : A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Les jeux d'enregistrements de type SOA sont créés automatiquement avec chaque zone. Ils ne peuvent pas être créés séparément. Notez que [le type d’enregistrement SPF a été abandonné dans les normes DNS en faveur de la création d’enregistrements SPF à l’aide du type d’enregistrement TXT](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT] Les jeux d'enregistrements CNAME ne peuvent pas coexister avec d'autres jeux d'enregistrements portant le même nom. Par exemple, vous ne pouvez pas créer un CNAME avec le nom relatif « www » et un enregistrement A avec le nom relatif « www » en même temps. Étant donné que l’extrémité de la zone (nom = « @ ») contient toujours les jeux d’enregistrements NS et SOA créés lors de la création de la zone, cela signifie que vous ne pouvez pas créer un jeu d’enregistrements CNAME au niveau de l’extrémité de la zone. Ces contraintes sont dues aux normes DNS, il ne s’agit pas de limites d'Azure DNS.

### Enregistrements génériques

Azure DNS prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ces derniers sont retournés pour toute requête avec un nom correspondant (à moins qu’une correspondance plus proche provienne d'un jeu d'enregistrements non génériques). Pour créer un jeu d'enregistrements génériques, utilisez le nom de jeu d'enregistrements « * », ou un nom dont la première étiquette est « * », par exemple, « *.foo ».

Les jeux d'enregistrements génériques sont pris en charge pour tous les types d'enregistrements, hormis NS et SOA.

## Obtention d’un jeu d'enregistrements
Pour récupérer un jeu d’enregistrement existant, utilisez `azure network dns record-set show`, en spécifiant le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements et le type d’enregistrement :

	azure network dns record-set show myresourcegroup contoso.com www A


## Liste des jeux d'enregistrements

Vous pouvez répertorier tous les enregistrements dans une zone DNS à l’aide de la commande `azure network dns record-set list` :

### Option 1 : 
Liste de tous les jeux d'enregistrements. Ceci renverra tous les jeux d'enregistrements, quel que soit le nom ou le type d'enregistrement :

	azure network dns record-set list myresourcegroup contoso.com

### Option 2 : 

Liste des jeux d'enregistrements pour un type d'enregistrement donné. Ceci renverra tous les jeux d'enregistrements correspondant au type d'enregistrement donné (dans ce cas, les enregistrements A) :


	azure network dns record-set list myresourcegroup contoso.com A 

Dans les deux cas, vous spécifierez le nom du groupe de la ressource et le nom de la zone.

## Ajout d’un enregistrement à un jeu d'enregistrements

Les enregistrements sont ajoutés aux jeux d’enregistrements à l’aide de la commande `azure network dns record-set add-record`.

Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type de jeu d'enregistrements. Par exemple, lors de l’utilisation d’un jeu d’enregistrements de type « A », vous ne pourrez spécifier que les enregistrements avec le paramètre « -a `<IPv4 address>` ».

Les exemples suivants montrent comment créer un jeu d'enregistrements de chaque type d'enregistrement contenant un seul enregistrement.

### Création d’un jeu d’enregistrements avec un seul enregistrement

Pour créer un jeu d’enregistrements, utilisez `azure network dns record-set create`, en spécifiant le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements, le type d’enregistrement et la durée de vie (TTL) :
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE] Si le paramètre --ttl n’est pas défini, la valeur par défaut est 4 (en secondes).


Après avoir créé le jeu d’enregistrements A, ajoutez l’adresse IPv4 au jeu d’enregistrements avec `azure network dns record-set add-record` :

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Création d’un jeu d’enregistrements AAAA avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Création d’un jeu d’enregistrements CNAME avec un seul enregistrement

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE] Les enregistrements CNAME n’admettent qu’une seule valeur de chaîne.

### Création d’un jeu d’enregistrements MX avec un seul enregistrement

Dans cet exemple, nous utilisons le nom de jeu d'enregistrements "@" pour créer l'enregistrement MX à l'apex de la zone (par exemple, « contoso.com »). Cela est courant pour les enregistrements MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Création d’un jeu d’enregistrements NS avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Création d’un jeu d’enregistrements SRV avec un seul enregistrement

Si vous créez un enregistrement SRV à la racine de la zone, indiquez simplement le \_service et le \_protocol dans le nom de l'enregistrement. Il est inutile d'inclure également « .@ » dans le nom de l'enregistrement

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Création d’un jeu d’enregistrements TXT avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Modification des jeux d'enregistrements existants


Ceci est illustré dans les exemples suivants :

### Mise à jour d’un enregistrement dans un jeu d'enregistrements existant

Pour cet exemple, nous ajouterons une autre adresse IP (1.2.3.4) à un jeu d’enregistrements A (www) existant :

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


Utilisez `azure network dns record-set delete-record` pour supprimer une valeur existante d’un jeu d’enregistrements :
 
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
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Suppression d’un enregistrement d’un jeu d'enregistrements existant

Les enregistrements peuvent être supprimés d’un jeu d’enregistrements à l’aide de `azure network dns record-set delete-record`. Notez que l’enregistrement qui est supprimé doit correspondre précisément à un enregistrement existant, sur tous les paramètres.

La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements. Consultez [Suppression d’un jeu d'enregistrements](#delete-a-record-set) ci-dessous pour en savoir plus.


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Suppression de l'enregistrement AAAA d'un jeu d'enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Suppression de l'enregistrement CNAME d'un jeu d'enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Suppression de l'enregistrement MX d'un jeu d'enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Suppression de l’enregistrement NS du jeu d'enregistrements
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Suppression de l'enregistrement SRV d'un jeu d'enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Suppression de l'enregistrement TXT d'un jeu d'enregistrements

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Suppression d’un jeu d'enregistrements
Les jeux d'enregistrements peuvent être supprimés à l'aide de la cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE] Vous ne pouvez pas supprimer les jeux d’enregistrements SOA et NS à l’extrémité de la zone (nom = « @ ») qui sont créés automatiquement lorsque la zone est créée. Ils seront automatiquement supprimés lors de la suppression de la zone.

Dans l’exemple ci-dessous, le jeu d’enregistrements A « test-a » sera supprimé de la zone DNS contoso.com :

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

Le commutateur facultatif « -q » peut être utilisé pour supprimer l’invite de confirmation.


## Étapes suivantes

Après avoir créé la zone DNS et les enregistrements, vous pouvez [déléguer votre domaine à Azure DNS](dns-domain-delegation.md).<BR> Découvrez comment [gérer des zones DNS](dns-operations-dnszones-cli.md) à l’aide de l’interface de ligne de commande.<BR> Vous pouvez également [automatiser les opérations à l'aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md) pour coder les opérations Azure DNS dans votre application.

 

<!---HONumber=AcomDC_0128_2016-->