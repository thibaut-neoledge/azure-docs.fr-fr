---
title: "Événements planifiés avec Azure Metadata Service | Microsoft Docs"
description: "Réagissez aux événements qui risquent d&quot;affecter votre machine virtuelle avant même qu’ils se produisent."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 7f0613285bc548e1329be3c33c30939f5998f379
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Service de métadonnées Azure - Événements planifiés (préversion)

> [!NOTE] 
> Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation. Pour plus d’informations, consultez la page [Conditions d’utilisation supplémentaires Microsoft Azure pour les préversions Microsoft Azure] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).
>

Les événements planifiés constituent l’un des sous-services du service de métadonnées Azure, qui affiche des informations concernant les événements à venir (par exemple, un redémarrage) afin que votre application puisse s’y préparer et limiter ainsi l’interruption. Il est disponible pour tous types de machines virtuelles Azure, notamment PaaS et IaaS. Les événements planifiés laissent à votre machine virtuelle le temps d’effectuer des tâches préventives et de réduire l’impact d’un événement. 


## <a name="introduction---why-scheduled-events"></a>Introduction - Pourquoi utiliser Événements planifiés ?

Avec les événements planifiés, vous pouvez prendre des mesures pour limiter l’impact sur votre service. Les charges de travail à instances multiples, qui utilisent des techniques de réplication pour maintenir l’état, peuvent être vulnérables si des pannes fréquentes affectent plusieurs instances. Ces pannes peuvent entraîner de tâches coûteuses (par exemple, la reconstruction des index) ou même une perte de réplica. Dans de nombreux autres cas, l'utilisation d'une séquence d’arrêt appropriée améliore la disponibilité globale du service. Par exemple, si vous terminez (ou annulez) des transactions en cours, réaffectez d'autres tâches à d’autres machines virtuelles du cluster (basculement manuel), la machine virtuelle est supprimée d’un pool d’équilibrage de charge. Dans certains cas, le fait d'avertir un administrateur d'un événement à venir, ou même de consigner un tel événement, facilite la gestion des applications hébergées dans le cloud.
Le service de métadonnées Azure s’appuie sur les événements planifiés dans les cas d’utilisation suivants :
-    La plateforme a lancé une maintenance (par exemple, le déploiement du système d’exploitation hôte)
-    L’utilisateur a lancé des appels (par exemple, un utilisateur redémarre ou redéploie une machine virtuelle)


## <a name="scheduled-events---the-basics"></a>Événements planifiés - Concepts de base  

Azure Metadata Service présente des informations sur les machines virtuelles en cours d'exécution en utilisant un point de terminaison REST au sein de la machine virtuelle. Les informations sont disponibles via une adresse IP non routable pour ne pas s'afficher en dehors de la machine virtuelle.

### <a name="scope"></a>Étendue
Les événements planifiés sont présentés à toutes les machines virtuelles dans un service cloud ou à toutes les machines virtuelles dans un groupe à haute disponibilité. Par conséquent, vous devez vérifier le champ **Ressources** de l’événement pour identifier les machines virtuelles qui seront affectées. 

### <a name="discover-the-endpoint"></a>Découvrir le point de terminaison
Si une machine virtuelle est créée au sein d’un réseau virtuel (VNet), le service de métadonnées est disponible à partir de l’adresse IP non routable 169.254.169.254. Sinon, dans les cas par défaut des services cloud et des machines virtuelles classiques, une logique supplémentaire est requise pour détecter le point de terminaison à utiliser. Consultez cet échantillon pour apprendre à [détecter le point de terminaison hôte] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>Contrôle de version 
Le service de métadonnées Instance fait l’objet d’une gestion de version. Les versions sont obligatoires et la version actuelle est 2017-03-01.

> [!NOTE] 
> Les préversions précédentes des événements planifiés prenaient en charge {dernière version} en tant que version de l’api. Ce format n’est plus pris en charge et sera déconseillé à l’avenir.
>


### <a name="using-headers"></a>Utilisation d'en-têtes
Lorsque vous interrogez le service de métadonnées, vous devez fournir l’en-tête suivant *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Activer des événements planifiés
La première fois que vous appelez des événements planifiés, Azure active implicitement la fonctionnalité sur votre machine virtuelle. Par conséquent, attendez-vous à un retard pouvant atteindre deux minutes dans la réponse à votre premier appel.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Tester votre logique avec des opérations lancées par l’utilisateur
Afin de tester votre logique, vous pouvez utiliser le Portail Azure, l’API, l’interface CLI ou PowerShell pour lancer des opérations aboutissant à des événements planifiés. Le redémarrage d’une machine virtuelle aboutit à un événement planifié de type Redémarrage. Le redéploiement d’une machine virtuelle aboutit à un événement planifié de type Redéploiement.
Dans les deux cas, l’opération lancée par l’utilisateur prend plus longtemps dans la mesure où les événements planifiés laissent plus de temps à une application pour qu’elle s’arrête correctement. 

## <a name="using-the-api"></a>Utilisation de l’API

### <a name="query-for-events"></a>Rechercher des événements
Vous pouvez rechercher des événements planifiés simplement en effectuant l’appel suivant

    curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01


Une réponse contient un tableau d’événements planifiés. Un tableau vide signifie qu’il n’y a actuellement aucun événement planifié.
S'il existe des événements planifiés, la réponse contient un tableau d’événements : 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType indique l’impact attendu sur la machine virtuelle, où :
- Freeze : la machine virtuelle est planifiée pour se mettre en pause pendant quelques secondes. Il n’a aucun impact sur la mémoire, les fichiers ouverts ou les connexions réseau
- Reboot : la machine virtuelle est planifiée pour redémarrer (la mémoire est effacée).
- Redeploy : la machine virtuelle est planifiée pour être déplacée vers un autre nœud (tout disque éphémère est perdu). 

Lorsqu’un événement est planifié (état = planifié), Azure indique le délai après lequel l’événement peut démarrer (spécifié dans le champ NotBefore).

### <a name="starting-an-event-expedite"></a>Démarrage d’un événement (accélérer)

Une fois que vous avez pris connaissance d’un événement à venir et effectué votre logique d’arrêt approprié, vous pouvez indiquer à Azure d'accélérer le déplacement (si possible) en effectuant un appel **POST**  
 

## <a name="powershell-sample"></a>Exemple de code PowerShell 

L’exemple suivant lit le serveur de métadonnées pour les événements planifiés et approuve les événements.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $uri)
{    
    # Create the Scheduled Events Approval Json
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Exemple de code C\# 
L’exemple suivant désigne un client qui présente des API afin de communiquer avec le service de métadonnées
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
Les événements planifiés peuvent être analysés à l’aide des structures de données suivantes 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

Exemple de programme qui utilise le client pour récupérer, gérer et accuser réception des événements :   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Exemple de code Python 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>Étapes suivantes 
[Maintenance planifiée des machines virtuelles dans Azure](linux/planned-maintenance.md)
[Service de métadonnées Instance](virtual-machines-instancemetadataservice-overview.md)

