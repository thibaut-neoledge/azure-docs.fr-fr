<properties
   pageTitle="Prise en main d’Azure DNS | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS. Cette procédure étape par étape vous permettra de créer votre première zone DNS pour pouvoir héberger votre domaine DNS à l’aide de PowerShell."
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Prise en main des DNS Azure avec PowerShell


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)

Le domaine « contoso.com » peut contenir un certain nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier.<BR><BR> Avant d’héberger votre domaine, vous devez créer une zone DNS. Les enregistrements DNS créés pour un domaine particulier sont situés dans une zone DNS pour le domaine.<BR><BR> Ces instructions utilisent Microsoft Azure PowerShell. Veillez à effectuer une mise à jour vers la version la plus récente d’Azure PowerShell pour utiliser les applets de commande Azure DNS. Ces mêmes étapes peuvent également être exécutées à l’aide de l’interface de ligne de commande Microsoft Azure, de l’API REST ou du Kit de développement logiciel (SDK).<BR><BR>

## Configuration de PowerShell pour Azure DNS

Les étapes suivantes doivent être effectuées avant de pouvoir gérer DNS Azure à l’aide d’Azure PowerShell.

### Étape 1
Azure DNS utilise Azure Resource Manager (ARM). Veillez à passer en mode PowerShell pour utiliser les applets de commande ARM. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](powershell-azure-resource-manager.md).<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

Un message d’avertissement indiquant « l’applet de commande Switch-AzureMode est déconseillée et sera supprimée dans une future version » peut s’afficher. Ignorez-le.

### Étape 2
 Connectez-vous à votre compte Azure.<BR><BR>

		PS C:\> Add-AzureAccount

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Pour afficher la liste des abonnements disponibles, utilisez l’applet de commande « Get-AzureSubscription ».<BR>

### Étape 4
Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.<BR>

### Étape 5

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être inscrit auprès de ce fournisseur de ressources pour pouvoir utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois pour chaque abonnement.

	PS c:> Register-AzureProvider -ProviderNamespace Microsoft.Network



## Balises et Etags
### Etags
Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il que vous venez de remplacer les modifications créées par quelqu’un d’autre ?<BR><BR> Azure DNS utilise les Etags pour gérer les modifications simultanées de la même ressource en toute sécurité. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son Etag l’est également. Lors de la mise à jour d’une ressource, vous pouvez transmettre l’Etag afin qu’Azure DNS vérifie que l’Etag du serveur correspond. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les ETags sont également utilisés lorsque vous créez une ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, Azure DNS PowerShell utilise les Etags pour bloquer les modifications simultanées apportées à des zones et des jeux d’enregistrements. Le commutateur « -Overwrite » facultatif peut être utilisé pour supprimer les vérifications Etag, auquel cas toutes les modifications simultanées qui se sont produites sont remplacées.<BR><BR> Au niveau de l’API REST Azure DNS, les Etags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est indiqué dans le tableau suivant :

|En-tête|Comportement|
|------|--------|
|Aucun|PUT réussit toujours (aucune vérification Etag)|
|If-match <etag>|PUT ne réussit que si la ressource existe et que l’Etag correspond|
|If-match * |PUT réussit uniquement si des ressources existent|
|If-none-match |* PUT réussit uniquement si aucune ressource n’existe|

### Balises
Les balises sont différentes des Etags. Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, consultez la page relative à l’utilisation de balises pour organiser vos ressources Azure. Azure DNS PowerShell prend en charge les balises dans les zones et les jeux d’enregistrements spécifiés à l’aide du paramètre « -Tag ». L’exemple suivant montre comment créer une zone DNS avec deux balises, « projet = demo » et « env = test » :

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Création d’une zone DNS
Une zone DNS est créée à l’aide de l’applet de commande New-AzureDnsZone. Dans l’exemple ci-dessous, nous allons créer une zone DNS appelée « contoso.com » dans le groupe de ressources « MyResourceGroup » :<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]Dans Azure DNS, les noms de zone doivent être spécifiés sans point à la fin. Par exemple, spécifiez « contoso.com » plutôt que « contoso.com. ».<BR>


Votre zone DNS vient d’être créée dans Azure DNS. La création d’une zone DNS crée également les enregistrements DNS suivants :<BR>



- L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
- Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](dns-domain-delegation.md).<BR>

Pour afficher ces enregistrements, utilisez Get-AzureDnsRecordSet :

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

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

>[AZURE.NOTE]Les jeux d’enregistrements à la racine (ou au « sommet ») d’une zone DNS utilisent « @ » comme nom du jeu d’enregistrements.


Après avoir créé votre première zone DNS, vous pouvez la tester à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/fr-FR/library/jj590781.aspx).<BR>

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, comme indiqué par Get-AzureDnsRecordSet ci-dessus. Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.<BR>

		C:\> nslookup
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


[Prise en main de la création de jeux d'enregistrements et d'enregistrements](dns-getstarted-create-recordset.md)<BR> [Gestion des zones DNS](dns-operations-dnszones.md)<BR> [Gestion des enregistrements DNS](dns-operations-recordsets.md)<BR> [Automatisation des opérations Azure avec le Kit de développement logiciel (SDK) .NET](dns-sdk.md)<BR> [Référence de l'API REST d'Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=Sept15_HO4-->