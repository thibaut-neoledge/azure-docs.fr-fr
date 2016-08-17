<properties pageTitle="Télémétrie Azure Media Services avec .NET | Microsoft Azure" 
	description="Cet article vous montre comment utiliser la télémétrie d’Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# Télémétrie Azure Media Services avec .NET
 
## Vue d'ensemble

La télémétrie et la surveillance Media Services permettent aux clients Media Services d’accéder aux données de métriques pour ces services. La version actuelle prend en charge les données de télémétrie des entités « Channel » et « StreamingEndpoint ». Vous pouvez configurer la télémétrie sur une granularité au niveau du composant. Il existe deux niveaux de détail : « Normal » et « Détaillé ». La version actuelle prend uniquement en charge « Normal ».

La télémétrie est écrite sur une table de stockage dans un compte Azure Storage fourni par le client (le compte de stockage doit être attaché au compte Media Services). Le système de télémétrie crée une table distincte pour chaque nouveau jour basé sur l’heure UTC 00:00. Par exemple « TelemetryMetrics20160321», où « 20160321» est la date de création de la table. Une table distincte est créée pour chaque jour.

Notez que le système de télémétrie ne gère pas la rétention des données. Vous pouvez supprimer les anciennes données de télémétrie en supprimant les tables de stockage.

Vous pouvez utiliser les données de télémétrie ainsi :

- Lire les données à partir d’Azure Table Storage directement (par exemple en utilisant le SDK Storage). Pour obtenir la description des tables de stockage de télémétrie, consultez les **informations sur l’utilisation de la télémétrie** dans [cette](https://msdn.microsoft.com/library/mt742089.aspx) rubrique.

Ou

- Utiliser le support dans le SDK .NET Media Services pour lire les données de stockage. Cette rubrique montre comment activer la télémétrie pour le compte AMS spécifié et comment interroger les métriques à l’aide du SDK .NET Azure Media Services.

## Configuration de la télémétrie pour un compte Media Services

Les étapes suivantes sont nécessaires pour activer la télémétrie :

- Obtenez les informations d’identification du compte de stockage associé au compte Media Services.
- Créez un point de terminaison de notification avec **EndPointType** défini sur **AzureTable** et endPointAddress pointant vers la table de stockage.

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Créez des paramètres de configuration de la surveillance pour les services que vous souhaitez surveiller. Pas plus d’un paramètre de configuration de la surveillance n’est autorisé.
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## Utilisation des informations de télémétrie

La télémétrie est écrite dans une table de stockage Azure dans le compte de stockage spécifié lors de la configuration de la télémétrie pour le compte Media Services. Le système de télémétrie crée une table distincte pour chaque nouveau jour basé sur l’heure UTC 00:00. Par exemple « TelemetryMetrics20160321», où « 20160321» est la date de création de la table. Une table distincte est créée pour chaque jour.

Vous pouvez interroger les tables de télémétrie pour les métriques suivantes. L’exemple présenté plus loin dans cette rubrique illustre l’utilisation du SDK .NET Media Services pour interroger les métriques.

### Journal StreamingEndpoint

Vous pouvez effectuer une requête pour obtenir les mesures StreamingEndPoint suivantes.

Propriété|Description|Exemple de valeur
---|---|---
**PartitionKey**|Obtient la clé de partition de l’enregistrement.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|Obtient la clé de ligne de l’enregistrement.|00959\_00000
**AccountId**|Obtient l’ID de compte Media Services.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Obtient l’ID du point de terminaison de diffusion en continu Media Services.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|Obtient l’heure observée de la mesure.|1/20/16 23:44:01
**HostName**|Obtient le nom d’hôte du point de terminaison de diffusion en continu.|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|Obtient le code d’état.|200
**ResultCode**|Obtient le code de résultat.|S\_OK
**RequestCount**|Obtient le nombre de requêtes.|3
**Octets envoyés**|Obtient les octets envoyés.|2987358
**ServerLatency**|Obtient la latence du serveur (notamment le stockage).|129
**EndToEndLatency**|Obtient l’heure de la requête de bout en bout.|250


### Pulsation du canal en direct

Vous pouvez effectuer une requête pour obtenir les mesures de canal en direct suivantes.

Propriété|Description|Exemple de valeur
---|---|---
**PartitionKey**|Obtient la clé de partition de l’enregistrement.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|Obtient la clé de ligne de l’enregistrement.|13872\_00005
**AccountId**|Obtient l’ID de compte Media Services.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Obtient l’ID de canal Media Services.|
**ObservedTime**|Obtient l’heure observée de la mesure.|1/21/2016 20:08:49
**CustomAttributes**|Obtient les attributs personnalisés.|
**TrackType**|Obtient le type de suivi.|video
**TrackName**|Obtient le nom du suivi.|video
**Bitrate**|Obtient le débit binaire.|785000
**IncomingBitrate**|Obtient le débit binaire entrant.|784548
**OverlapCount**|Obtient le nombre de chevauchements.|0
**DiscontinuityCount**|Obtient le nombre de discontinuité.|0
**LastTimestamp**|Obtient le dernier horodatage.|1800488800
 
## Exemple  
	
L’exemple suivant montre comment activer la télémétrie pour le compte AMS spécifié et comment interroger les métriques à l’aide du SDK .NET Azure Media Services.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Étape suivante
 
Consultez les parcours d’apprentissage Azure Media Services pour en savoir plus sur les fonctionnalités exceptionnelles offertes par AMS.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->