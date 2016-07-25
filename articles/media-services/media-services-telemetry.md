<properties pageTitle="Télémétrie Azure Media Services avec .NET | Microsoft Azure" 
	description="Cet article vous montre comment utiliser la télémétrie d’Azure Media Services." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Télémétrie Azure Media Services avec .NET

## Vue d'ensemble

La télémétrie et la surveillance Media Services permettent aux clients Media Services d’accéder aux données de métriques pour ces services. La version actuelle prend en charge les données de télémétrie pour les entités « Channel » et « StreamingEndpoint ». Vous pouvez configurer la télémétrie sur la granularité du niveau du composant. Il existe deux niveaux de détail : « Normal » et « Détaillé ». La version actuelle prend uniquement en charge « Normal ».

La télémétrie est écrite dans un compte Azure Storage fourni par le client (un compte de stockage doit être attaché au compte Media Services). La télémétrie est écrite dans une table Azure Storage dans le compte de stockage spécifié. Le système de télémétrie crée une table distincte pour chaque nouveau jour basé sur l’heure UTC 00:00. Par exemple « TelemetryMetrics20160321», où « 20160321» est la date de création de la table. Une table distincte est créée pour chaque jour.

Le système de télémétrie ne conserve pas les données et ne supprime pas automatiquement les anciens enregistrements. Pour cette raison, c’est à vous de les gérer et de les supprimer. La présence de tables distinctes pour chaque jour facilite la suppression des anciens enregistrements. Il vous suffit de supprimer les anciennes tables.

Cette rubrique montre comment activer la télémétrie pour les services AMS spécifiés et comment interroger les mesures à l’aide de .NET.

## Configuration de la télémétrie pour un compte Media Services

Les étapes suivantes sont nécessaires pour activer la télémétrie :

- Obtenez les informations d’identification du compte de stockage associé au compte Media Services.
- Créez un point de terminaison de notification avec **EndPointType** défini sur **AzureTable** et endPontAddress pointant vers la table de stockage.

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


## Journal StreamingEndpoint

###Mesures disponibles

Vous pouvez effectuer une requête pour obtenir les mesures StreamingEndPoint suivantes.

- **PartitionKey** obtient la clé de partition de l’enregistrement.
- **RowKey** obtient la clé de ligne de l’enregistrement.
- **AccountId** obtient l’ID de compte Media Services.
- **AccountId** obtient l’ID du point de terminaison de diffusion en continu Media Services.
- **ObservedTime** obtient l’heure observée de la mesure.
- **HostName** obtient le nom d’hôte du point de terminaison de diffusion en continu.
- **StatusCode** obtient le code d’état.
- **ResultCode** obtient le code de résultat.
- **RequestCount** obtient le nombre de requêtes.
- **BytesSent** obtient les octets envoyés.
- **BytesSent** obtient la latence du serveur.
- **BytesSent** obtient l’heure de la requête de bout en bout.

###Exemple de résultat de requête de point de terminaison de diffusion en continu

![Requête du point de terminaison de diffusion en continu](media/media-services-telemetry/media-services-telemetry01.png)


## Pulsation du canal en direct

###Mesures disponibles

Vous pouvez effectuer une requête pour obtenir les mesures de canal en direct suivantes.

- **PartitionKey** obtient la clé de partition de l’enregistrement.
- **RowKey** obtient la clé de ligne de l’enregistrement.
- **AccountId** obtient l’ID de compte Media Services.
- **AccountId** obtient l’ID de canal Media Services.
- **ObservedTime** obtient l’heure observée de la mesure.
- **CustomAttributes** obtient les attributs personnalisés.
- **TrackType** obtient le type de suivi.
- **TrackName** obtient le nom du suivi.
- **Bitrate** obtient le débit binaire.
- **IncomingBitrate** obtient le débit binaire entrant.
- **OverlapCount** obtient le nombre de chevauchements.
- **DiscontinuityCount** obtient le nombre de discontinuités.
- **LastTimestamp** obtient le dernier horodatage.
 
###Exemple de résultat de requête de canal en direct

![Requête du point de terminaison de diffusion en continu](media/media-services-telemetry/media-services-telemetry01.png)

## Exemple de mesures StreamingEndpoint
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0713_2016-->