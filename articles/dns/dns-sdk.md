<properties 
   pageTitle="Opérations d'automatisation DNS et de jeux d’enregistrement à l'aide du Kit de développement logiciel (SDK) .net | Microsoft Azure" 
   description="Utilisation du Kit de développement logiciel (SDK) .NET pour l’automatisation de toutes les opérations dans Azure DNS." 
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
   ms.date="05/20/2015"
   ms.author="joaoma"/>
# Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET
Vous pouvez automatiser les opérations de création, de suppression ou de mise à jour des zones DNS, des recordsets et des enregistrements à l'aide du Kit de développement logiciel (SDK) DNS avec la bibliothèque de gestion DNS de .NET. Un projet Visual Studio complet est disponible [ici.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Déclarations des packages NuGet et des espaces de noms
Pour utiliser le client DNS, il est nécessaire d’installer le package NuGet « Bibliothèque de gestion Azure DNS » et d’ajouter des espaces de noms de gestion DNS à votre projet. Accédez à Visual Studio, ouvrez un projet ou un nouveau projet et accédez à Outils, Console de gestionnaire de package Nuget. Télécharger la bibliothèque de gestion Azure DNS :

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Initialisation du client de gestion DNS

La DnsManagementClient contient les méthodes et les propriétés nécessaires à la gestion des zones DNS et des recordsets. Afin que le client puisse accéder à votre abonnement, il est nécessaire de configurer les autorisations appropriées et de générer un jeton AWT. Pour plus d’informations, consultez « Demandes d’authentification Azure Resource Manager ».

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Création ou mise à jour d'une zone DNS

Pour créer une zone DNS, un objet de zone est créé puis transmis à dnsClient.Zones.CreateOrUpdate. Étant donné que les zones DNS ne sont pas liées à une région spécifique, l'emplacement est défini sur « global ».<BR>

Création d’une zone DNS :

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


Azure DNS prend en charge l'accès concurrentiel optimiste appelé [Etags](../dns-getstarted-create-dnszone#Etags-and-tags). L'Etag est une propriété de la zone et IfNoneMatch est une propriété dans ZoneCreateOrUpdateParameters.

## Création ou mise à jour d'enregistrements DNS
Les enregistrements DNS sont gérés en tant que jeu d'enregistrements. Un jeu d'enregistrements est l’ensemble d'enregistrements ayant le même nom et le même type d'enregistrement dans une zone. Pour créer ou mettre à jour un jeu d'enregistrements, un objet RecordSet est créé et transmis à dnsClient.RecordSets.CreateOrUpdate. Notez que le nom du jeu d'enregistrements est lié au nom de zone et ne constitue pas le nom DNS complet. Une fois de plus, l'emplacement est défini sur « global ».
    
Réalisation de certains jeux d'enregistrements

	// make some records sets
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
	
    
Azure DNS prend en charge l'accès concurrentiel optimiste [Etags](../dns-getstarted-create-dnszone#Etags-and-tags). L'Etag est une propriété de RecordSet et IfNoneMatch est une propriété dans RecordSetCreateOrUpdateParameters.

## Obtention de zones et de jeux d'enregistrements
Les collections de zones et de recordsets permettent d'obtenir respectivement des zones et des jeux d'enregistrements. Les jeux d'enregistrements sont identifiés par leur type, leur nom et la zone (ainsi que le groupe de ressources) dans laquelle ils sont présents. Les zones sont identifiées par leur nom et le groupe de ressources dans lequel elles sont présentes.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Liste des zones et des recordsets

Pour répertorier des zones, utilisez la méthode List dans la collection de zones. Pour répertorier les jeux d'enregistrements, utilisez les méthodes List ou ListAll dans la collection de recordsets. La méthode List diffère de la méthode ListAll car elle retourne uniquement les jeux d'enregistrement du type spécifié.

L'exemple suivant montre comment obtenir une liste de zones DNS et des jeux d’enregistrements :


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## Voir aussi 
[Vue d’ensemble de Traffic Manager](../traffic-manager-overview)

[Vue d'ensemble d’Azure DNS](../dns-overview)

[Exemple de projet du Kit de développement logiciel (SDLK) Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=July15_HO3-->