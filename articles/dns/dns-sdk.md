<properties 
   pageTitle="Créer des zones et des jeux d’enregistrements DNS pour Azure DNS à l’aide du SDK .NET | Microsoft Azure" 
   description="Comment créer des zones et des jeux d’enregistrements DNS pour Azure DNS à l’aide du SDK .NET" 
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


# Créer des zones et des jeux d’enregistrements DNS à l’aide du Kit de développement logiciel (SDK) .NET

Vous pouvez automatiser les opérations de création, de suppression ou de mise à jour des zones , des jeux d’enregistrements et des enregistrements DNS à l’aide du Kit de développement logiciel (SDK) DNS avec la bibliothèque de gestion DNS de .NET. Un projet Visual Studio complet est disponible [ici.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Packages NuGet et déclaration des espaces de noms

Pour pouvoir utiliser le client DNS, vous devez installer le package NuGet **Bibliothèque de gestion Azure DNS** et ajouter les espaces de noms de la gestion DNS à votre projet.
 
1. Dans **Visual Studio**, ouvrez un projet existant ou un nouveau projet.

2. Accédez à **Outils** **>** **Gestionnaire de package NuGet** **>** **Console du gestionnaire de package**.

3. Téléchargez la bibliothèque de gestion Azure DNS.

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## Initialiser le client de gestion DNS

Le *DnsManagementClient* contient les méthodes et les propriétés nécessaires à la gestion des zones et des jeux d’enregistrements DNS. Pour que le client puisse accéder à votre abonnement, vous devez configurer les autorisations appropriées et générer un jeton AWT. Pour plus d’informations, consultez [Demandes d’authentification Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790557.aspx).

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Créer ou mettre à jour une zone DNS

Pour créer une zone DNS, un objet « Zone » est créé puis passé à *dnsClient.Zones.CreateOrUpdate*. Comme les zones DNS ne sont pas liées à une région spécifique, l’emplacement est défini sur « global ».<BR> Azure DNS prend en charge l’accès simultané optimiste, appelé [ETags](dns-getstarted-create-dnszone.md). L’« ETag » est une propriété de la zone. « IfNoneMatch » est une propriété de ZoneCreateOrUpdateParameters.

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## Créer ou mettre à jour des enregistrements et des jeux d’enregistrements DNS

Les enregistrements DNS sont gérés en tant que jeu d'enregistrements. Un jeu d’enregistrements est un ensemble d’enregistrements ayant le même nom et le même type d’enregistrement au sein d’une zone. Pour créer ou mettre à jour un jeu d’enregistrements, un objet « RecordSet » est créé et passé à *dnsClient.RecordSets.CreateOrUpdate*. Notez que le nom du jeu d’enregistrements est relatif au nom de zone, au contraire du nom DNS complet. L’emplacement est défini sur « global ».<BR> Azure DNS prend en charge l'accès concurrentiel optimiste [Etags](dns-getstarted-create-dnszone.md). L’« ETag » est une propriété du RecordSet. « IfNoneMatch » est une propriété de RecordSetCreateOrUpdateParameters.
    


	// To create record sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
## Obtenir des zones et des jeux d’enregistrements

Les collections *Zones* et *RecordSets* permettent d’obtenir respectivement des zones et des jeux d’enregistrements. Les jeux d’enregistrements sont identifiés par leur type et leur nom, ainsi que la zone et le groupe de ressources où ils sont présents. Les zones sont identifiées par leur nom et le groupe de ressources dans lequel elles sont présentes.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## Répertorier les zones et les jeux d’enregistrements

Pour répertorier les zones, utilisez la méthode *List* dans la collection Zones. Pour répertorier les jeux d’enregistrements, utilisez les méthodes *List* ou *ListAll* dans la collection RecordSets. La méthode *List* diffère de la méthode *ListAll* en ceci qu’elle retourne seulement les jeux d’enregistrements d’un type spécifié.

L’exemple suivant montre comment obtenir une liste de zones et de jeux d’enregistrements DNS.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## Étapes suivantes

[Exemple de projet du Kit de développement logiciel (SDLK) Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0817_2016-->