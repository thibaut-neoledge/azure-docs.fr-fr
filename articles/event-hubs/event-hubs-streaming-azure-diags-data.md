<properties 
   pageTitle="Diffusion des données de diagnostics Azure dans le chemin réactif à l’aide d’Event Hubs"
   description="Cet article montre la procédure complète permettant de configurer des diagnostics Azure avec Event Hubs et inclut des conseils relatifs aux scénarios courants."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="tarcher" />

# Diffusion des données de diagnostics Azure dans le chemin réactif à l’aide d’Event Hubs

## Vue d’ensemble
Les diagnostics Azure fournissent des méthodes flexibles pour collecter des mesures et des journaux à partir de machines virtuelles de calcul et les transférer dans Azure Storage. Depuis mars 2016 (Kit de développement logiciel 2.9), il est possible de recevoir les diagnostics Azure pour personnaliser entièrement les sources de données et transférer des données de chemin réactif en quelques secondes à l’aide d’Azure Event Hubs.

Les types de données pris en charge sont les suivants :

- Événements ETW
- Compteurs de performance
- Journaux des événements Windows 
- Journaux d’application
- Journaux d’infrastructure de diagnostics Azure
 
Cet article vous montre la procédure complète permettant de configurer des diagnostics Azure avec Event Hubs. Il fournit également des conseils pour des scénarios courants tels que la personnalisation des journaux et mesures envoyés à Event Hubs, la modification de la configuration de chaque environnement, un exemple d’affichage de données de diffusion Event Hubs et la résolution des problèmes de connexion.

## Composants requis
La réception Event Hubs dans les diagnostics Azure est prise en charge dans tous les types de calcul (service cloud, machines virtuelles, VMSS et Service Fabric) qui prennent en charge WAD, dont le Kit de développement logiciel (SDK) 2.9 Azure et les outils Azure correspondants pour Visual Studio.
  
- Extension Azure Diagnostics 1.6 (ciblée par défaut par le [Kit de développement logiciel (SDK) Azure pour .NET 2.9 ou ultérieur](https://azure.microsoft.com/downloads/))
- [Visual Studio 2013 ou une version ultérieure](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Paramétrage réussi de la configuration des diagnostics Azure dans l’application à l’aide d’un fichier *.wadcfgx* en utilisant l’une des méthodes suivantes :
	- Visual Studio : [Configuration de Diagnostics pour les services cloud et les machines virtuelles Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell : [Activer les diagnostics dans Azure Cloud Services à l’aide de PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espace de noms Event Hubs approvisionné tel que décrit dans l’article [Prise en main d’Event Hubs](./event-hubs-csharp-ephcs-getstarted.md)

## Connexion des diagnostics Azure au récepteur Event Hubs
Les diagnostics Azure transmettent toujours par défaut des journaux et des mesures à un compte Azure Storage. Une application peut en outre est transmise à Event Hubs en ajoutant une nouvelle section **Sinks** à l’élément **WadCfg** dans la section **PublicConfig** du fichier *.wadcfgx*. Dans Visual Studio, le fichier *.wadcfgx* est stocké dans le fichier *Projet de service cloud > Rôles > (RoleName) > diagnostics.wadcfgx*.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

Dans cet exemple, l’URL Event Hubs est définie sur l’espace de noms complet du hub d’événements (espace de noms ServiceBus + « / » + nom du hub d’événements).

L’URL Event Hubs s’affiche dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) dans le tableau de bord Event Hubs.

Le nom **Sink** peut être défini sur n’importe quelle chaîne valide tant que cette même valeur est utilisée de manière cohérente tout au long du fichier de configuration.

**Remarque :** des récepteurs supplémentaires peuvent être configurés dans cette section, tels que « applicationInsights ». Les diagnostics Azure permettent de définir un ou plusieurs récepteurs, à condition que chaque récepteur soit également déclaré dans la section **PrivateConfig**.

Le récepteur Event Hubs doit également être déclaré et défini dans la section **PrivateConfig** du fichier de configuration *.wadcfgx*.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

L’élément **SharedAccessKeyName** doit correspondre à une clé SAS et une stratégie qui a été définie dans l’espace de noms **ServiceBus/EventHub**. Pour cela, accédez au tableau de bord Event Hubs dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez l’onglet **Configurer** et définissez une stratégie nommée (par exemple « SendRule ») qui a des autorisations d’*envoi*. L’élément **StorageAccount** est également déclaré dans le fichier **PrivateConfig**. Il est inutile de modifier les valeurs ici, surtout si elles fonctionnent correctement. Dans cet exemple, nous ne renseignons pas les valeurs, car c’est une ressource en aval qui va les définir. Par exemple, le fichier de configuration d’environnement *ServiceConfiguration.Cloud.cscfg* définit les noms et clés appropriés pour l’environnement.

>[AZURE.WARNING] Notez que la clé SAS Event Hubs est stockée en texte brut dans le fichier *.wadcfgx*. Elle est souvent intégrée au contrôle du code source ou en tant que ressource dans votre serveur de builds. Vous devez donc la protéger en conséquence. Il est recommandé d’utiliser une clé SAS ici avec les autorisations *Envoyer uniquement* afin qu’un utilisateur malveillant puisse au plus écrire dans le hub d’événements, mais sans jamais l’écouter ou le gérer.

## Configuration des mesures et journaux de diagnostics Azure à des fins de réception avec Event Hubs
Comme indiqué précédemment, toutes les données de diagnostic par défaut et personnalisées (par exemple, les mesures et journaux) sont automatiquement réceptionnées dans Azure Storage à des intervalles configurés. Avec Event Hubs (et tout récepteur supplémentaire), vous pouvez spécifier n’importe quel nœud racine ou terminal de la hiérarchie à transmettre avec le hub d’événements. Cela inclut les événements ETW, les compteurs de performances, les journaux d’application et les journaux des événements Windows.

Il est important de prendre en compte le nombre de points de données qui doit réellement être transféré vers Event Hubs. Généralement, les développeurs utilisent ceci pour les données de chemin réactif à faible latence qui doivent être consommées et interprétées rapidement (par exemple en surveillant les systèmes d’alerte ou les règles de mise à l’échelle automatique). Cela peut également servir à configurer un autre magasin d’analyse ou de recherche, par exemple, Stream Analytics, ElasticSearch, un système de surveillance personnalisé ou vos systèmes de surveillance tiers favoris.

Voici quelques exemples de configurations :

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

Dans l’exemple suivant, le récepteur est appliqué au nœud parent **PerformanceCounters** dans la hiérarchie, ce qui signifie que tous les enfants **PerformanceCounters** sont envoyés à Event Hubs.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

Dans l’exemple ci-dessus, le récepteur est appliqué à uniquement trois compteurs : Demandes en attente, Demandes rejetées et % de temps processeur.

L’exemple suivant montre comment un développeur peut contrôler et limiter le volume de données envoyé comme mesures critiques utilisées pour l’intégrité de ce service.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

Dans cet exemple, le récepteur est appliqué aux journaux et filtré uniquement pour le suivi au niveau de l’erreur.
 
## Déploiement et mise à jour d’une configuration de diagnostics et d’application de service cloud

Le moyen le plus facile de déployer l’application et de configurer le récepteur Event Hubs est d’utiliser Visual Studio. Pour afficher et effectuer les modifications souhaitées, ouvrez le fichier *.wadcfgx* dans Visual Studio, qui est stocké dans le fichier *Projet de service cloud -> Rôles -> (RoleName) -> diagnostics.wadcfgx*, puis enregistrez-le lorsque vous avez terminé.

À ce stade, toutes les actions de déploiement et de mise à jour de déploiement dans les commandes ou scripts Visual Studio, Visual Studio Team System ou MSBUILD (à l’aide de la cible / t:), incluent le fichier *.wadcfgx* dans le processus d’empaquetage et vérifient qu’il est déployé dans Azure à l’aide de l’extension d’agent WAD appropriée associée à vos machines virtuelles.
  
Une fois que le déploiement de l’application et la configuration des diagnostics Azure ont réussi, l’activité correspondante s’affiche dans le tableau de bord Event Hubs. Cela vous indique que vous pouvez maintenant afficher les données de chemin réactif dans l'outil client d’écoute ou d’analyse de votre choix.
 
Dans l’illustration suivante, le tableau de bord Event Hubs affiche l’envoi sain des données de diagnostic au hub d’événements un peu après 23 h 00, heure à laquelle l’application a été déployée avec un fichier *.wadcfgx* mis à jour et le récepteur correctement configuré.

![][0]
  
>[AZURE.NOTE] Lorsque vous effectuez des mises à jour dans le fichier de configuration des diagnostics Azure (.wadcfgx), il est recommandé de les publier dans l’ensemble de l’application ainsi que la configuration à l’aide de la publication Visual Studio ou d’un script Windows PowerShell.

## Affichage des données de chemin réactif

Comme indiqué précédemment, il existe plusieurs scénarios d’utilisation pour écouter et traiter les données Event Hubs.
  
Une approche simple consiste à créer une petite application console de test pour écouter le hub d’événements et imprimer le flux de sortie. Le code suivant (expliqué plus en détail dans l’article [Prise en main d’Event Hubs](./event-hubs-csharp-ephcs-getstarted.md)) peut être placé dans une application console.

Notez que l’application console doit inclure le [package Nuget EventProcessor](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

N’oubliez pas de remplacer les valeurs entre crochets de la fonction **Main** ci-dessous par les valeurs de vos ressources.

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## Dépannage du récepteur Event Hubs

- Le hub d’événements n’affiche pas l’activité attendue relative à des événements entrants ou sortants

	Vérifiez que le hub d’événements a été correctement approvisionné. Toutes les informations de connexion de la section **PrivateConfig** du fichier *.wadcfgx* doivent correspondre aux valeurs de votre ressource, comme indiqué dans le portail. Vérifiez qu’une stratégie SAS est définie (« SendRule » dans l’exemple) dans le portail et que l’autorisation d’envoi a été octroyée.

- Après une mise à jour, le hub d’événements n’affiche plus d’activité relative aux événements entrants ou sortants

	Commencez par vérifier que les informations de configuration et du hub d’événements correspondent à celles indiquées ci-dessus. Certains problèmes sont causés par la réinitialisation de **PrivateConfig** lors d’une mise à jour du déploiement. La solution recommandée consiste à apporter toutes les modifications dans le projet *.wadcfgx*, puis à transmettre une mise à jour complète de l’application. Si ce n’est pas possible, vérifiez que la mise à jour des diagnostics transmet un fichier **PrivateConfig** complet, y compris la clé SAS.

- J’ai essayé les solutions ci-dessus, mais le hub d’événements ne fonctionne toujours pas

	Consultez la table Azure Storage **WADDiagnosticInfrastructureLogsTable** qui contient les journaux et les erreurs des diagnostics Azure. Une option consiste à utiliser un outil tel que l’[explorateur de stockage Azure](http://www.storageexplorer.com) pour vous connecter à ce compte de stockage, consulter cette table et ajouter une requête pour l’horodatage dans les dernières 24 heures. Vous pouvez utiliser l’outil pour exporter le fichier CSV et l’ouvrir dans une application telle que Microsoft Excel, qui vous permet de rechercher facilement des chaînes de carte d’appel comme « EventHubs » pour voir quelle erreur est signalée.

## Étapes suivantes
• [En savoir plus sur Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## Annexe : Exemple de fichier de configuration de diagnostics Azure (.wadcfgx)
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

Le fichier complémentaire *ServiceConfiguration.Cloud.cscfg* pour cet exemple se présente comme suit.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0608_2016-->