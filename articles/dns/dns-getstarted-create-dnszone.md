<properties
   pageTitle="Prise en main d’Azure DNS | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS. Cette procédure étape par étape vous permettra de créer votre première zone DNS pour pouvoir héberger votre domaine DNS à l’aide de PowerShell."
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
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Créer une zone DNS en utilisant PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)

Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide de l’interface de ligne de commande. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou du portail Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>À propos des ETags et des étiquettes

### <a name="etags"></a>ETags

Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’il vient de remplacer les modifications créées par quelqu’un d’autre ?

Azure DNS utilise les Etags pour gérer les modifications simultanées de la même ressource en toute sécurité. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son Etag l’est également. Lors de la mise à jour d’une ressource, vous pouvez transmettre l’Etag afin qu’Azure DNS vérifie que l’Etag du serveur correspond. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les ETags sont également utilisés lorsque vous créez une ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, Azure DNS PowerShell utilise les Etags pour bloquer les modifications simultanées apportées à des zones et des jeux d’enregistrements. Le commutateur facultatif *-Overwrite* peut être utilisé pour supprimer les vérifications d’ETags, auquel cas toutes les modifications simultanées qui se sont produites sont remplacées.

Au niveau de l’API REST Azure DNS, les Etags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est indiqué dans le tableau suivant :

|En-tête|Comportement|
|------|--------|
|Aucun|PUT réussit toujours (aucune vérification Etag)|
|If-match <etag>|PUT ne réussit que si la ressource existe et que l’Etag correspond|
|If-match * | PUT réussit seulement si la ressource existe|
|If-none-match * |	PUT réussit seulement si la ressource n’existe pas|

### <a name="tags"></a>Étiquettes

Les balises sont différentes des Etags. Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

Azure DNS PowerShell prend en charge les étiquettes sur les zones et sur les jeux d’enregistrements spécifiés avec le paramètre `-Tag` des options.


## Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.
	
- Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
	
- Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager (version 1.0 ou ultérieure). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## Étape 1 : Connexion

Ouvrez la console PowerShell et connectez-vous à votre compte. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

Utilisez l’exemple suivant pour faciliter votre connexion :

	Login-AzureRmAccount

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription 

Spécifiez l’abonnement à utiliser.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Étape 2 : Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## Étape 3 : S’inscrire

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être inscrit auprès de ce fournisseur de ressources pour pouvoir utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois pour chaque abonnement.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## Étape 4 : Créer une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzureRmDnsZone`. Vous pouvez trouver ci-dessous des exemples de création d’une zone DNS avec ou sans étiquettes. Pour plus d’informations sur les étiquettes, consultez la section sur les [étiquettes](#tags) de cet article.

>[AZURE.NOTE] Dans Azure DNS, les noms de zone doivent être spécifiés sans point à la fin (**« . »**). Par exemple, spécifiez « **contoso.com**» au lieu de « **contoso.com.** ».

### Création d’une zone DNS

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### Pour créer une zone DNS avec des étiquettes

L’exemple suivant montre comment créer une zone DNS avec deux étiquettes, *projet = demo* et *env = test*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements DNS suivants :

- Enregistrement *SOA* (Start of Authority). Il est présent à la racine de chaque zone DNS.

- Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez `Get-AzureRmDnsRecordSet` :

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


Les jeux d’enregistrements à la racine (ou *apex*) d’une zone DNS utilisent **@** comme nom de jeu d’enregistrements.


## Test

Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, dig ou l’[applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, répertoriés ci-dessus par `Get-AzureRmDnsRecordSet`. Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## Étapes suivantes

Après avoir créé une zone DNS, créez des [jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution des noms pour votre domaine Internet.

<!---HONumber=AcomDC_0525_2016-->