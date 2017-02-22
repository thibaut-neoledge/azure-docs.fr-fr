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
translationtype: Human Translation
ms.sourcegitcommit: c7f552825f3230a924da6e5e7285e8fa7fa42842
ms.openlocfilehash: 541709ca17b96f8334e67dbdbbd9a10eefffa06b


---
# <a name="azure-metadata-service---scheduled-events"></a>Azure Metadata Service - Événements planifiés

Azure Metadata Service vous permet d'obtenir des informations relatives à votre machine virtuelle hébergée dans Azure. Événements planifiés, une des catégories présentées, affiche des informations concernant les événements à venir (par exemple, un redémarrage) afin que votre application puisse s’y préparer et limiter ainsi l’interruption. Elle est disponible pour tous les types de machines virtuelles Azure, notamment PaaS et IaaS. Le service laisse à votre machine virtuelle le temps d'effectuer des tâches préventives et de réduire l'impact d’un événement. Par exemple, votre service peut vider des sessions, élire un nouveau leader ou copier des données après avoir observé qu’une instance est planifiée pour redémarrer afin d'éviter toute interruption.



## <a name="introduction---why-scheduled-events"></a>Introduction - Pourquoi utiliser Événements planifiés ?

Événements planifiés vous permet de prendre connaissance (découvrir) des événements à venir qui peuvent avoir un impact sur la disponibilité de votre machine virtuelle et d'effectuer des opérations proactives pour limiter cet impact sur votre service.
Les charges de travail à instances multiples, qui utilisent des techniques de réplication pour maintenir l’état, peuvent être vulnérables si des pannes fréquentes affectent plusieurs instances. Ces pannes peuvent entraîner de tâches coûteuses (par exemple, la reconstruction des index) ou même une perte de réplica.
Dans de nombreux autres cas, l'utilisation d'une séquence d’arrêt appropriée améliore la disponibilité globale du service. Par exemple, si vous terminez (ou annulez) des transactions en cours, réaffectez d'autres tâches à d’autres machines virtuelles du cluster (basculement manuel), la machine virtuelle est supprimée d’un pool d’équilibrage de charge.
Dans certains cas, le fait d'avertir un administrateur d'un événement à venir, ou même de consigner un tel événement, facilite la gestion des applications hébergées dans le cloud.

Azure Metadata Service traite les événements planifiés dans les cas d’utilisation suivants :
-   La plateforme a lancé une maintenance avec un fort impact (par exemple, le déploiement du système d’exploitation hôte)
-   La plateforme a lancé une maintenance sans réel impact (par exemple, la migration d'une machine virtuelle sur place)
-   Appels interactifs (par exemple, un utilisateur redémarre ou redéploie une machine virtuelle)



## <a name="scheduled-events---the-basics"></a>Événements planifiés - Concepts de base  

Azure Metadata Service présente des informations sur les machines virtuelles en cours d'exécution en utilisant un point de terminaison REST au sein de la machine virtuelle. Les informations sont disponibles via une adresse IP non routable pour ne pas s'afficher en dehors de la machine virtuelle.

### <a name="scope"></a>Étendue 
Les événements planifiés sont présentés à toutes les machines virtuelles dans un service cloud ou à toutes les machines virtuelles dans un groupe à haute disponibilité. Par conséquent, vous devez vérifier le champ **Ressources** de l’événement pour identifier les machines virtuelles qui seront affectées.

### <a name="discover-the-endpoint"></a>Découvrir le point de terminaison
Si une machine virtuelle est créée au sein d’un réseau virtuel (VNet), le service de métadonnées est disponible à partir de l’adresse IP non routable : 169.254.169.254

Dans le cas où une machine virtuelle est utilisée pour les services cloud (PaaS), le point de terminaison du service de métadonnées peut être découvert à l'aide du registre.

    {HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure\DeploymentManagement}

### <a name="versioning"></a>Contrôle de version 
Le service de métadonnées utilise une API dont la version est au format suivant : http://{ip}/metadata/{version}/scheduledevents. Il est recommandé que votre service utilise la dernière version disponible à l’adresse : http://{ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>Utilisation d'en-têtes
Lorsque vous interrogez le service de métadonnées, vous devez fournir l’en-tête suivant *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Activer des événements planifiés
La première fois que vous appelez des événements planifiés, Azure active implicitement la fonctionnalité sur votre machine virtuelle. Par conséquent, attendez-vous à un retard pouvant atteindre une minute dans la réponse à votre premier appel. 


## <a name="using-the-api"></a>Utilisation de l’API

### <a name="query-for-events"></a>Rechercher des événements
Vous pouvez rechercher des événements planifiés simplement en effectuant l’appel suivant

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

Une réponse contient un tableau d’événements planifiés. Un tableau vide signifie qu’il n’y a actuellement aucun événement planifié.
S'il existe des événements planifiés, la réponse contient un tableau d’événements : 

    {
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Pause",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType indique l’impact attendu sur la machine virtuelle, où :
- Pause : La machine virtuelle est planifiée pour se mettre en pause pendant quelques secondes. Il n’a aucun impact sur la mémoire, les fichiers ouverts ou les connexions réseau
- Reboot : la machine virtuelle est planifiée pour redémarrer (la mémoire est effacée).
- Redeploy : la machine virtuelle est planifiée pour être déplacée vers un autre nœud (tout disque éphémère est perdu). 

Lorsqu’un événement est planifié (état = planifié), Azure indique le délai après lequel l’événement peut démarrer (spécifié dans le champ NotBefore).

### <a name="starting-an-event-expedite"></a>Démarrage d’un événement (accélérer)

Une fois que vous avez pris connaissance d’un événement à venir et effectué votre logique d’arrêt approprié, vous pouvez indiquer à Azure d'accélérer le déplacement (si possible) en effectuant un appel **POST**  
 

## <a name="powershell-sample"></a>Exemple de code PowerShell 

L’exemple suivant recherche sur le serveur de métadonnées les événements planifiés et les enregistre dans le journal des événements de l'application avant d’accuser réception.

```PowerShell
$localHostIP = "169.254.169.254"
$ScheduledEventURI = "http://"+$localHostIP+"/metadata/latest/scheduledevents"

# Call Azure Metadata Service - Scheduled Events 
$scheduledEventsResponse =  Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $ScheduledEventURI -Method get 

if ($json.Events.Count -eq 0 )
{
    Write-Output "++No scheduled events were found"
}

for ($eventIdx=0; $eventIdx -lt $scheduledEventsResponse.Events.Length ; $eventIdx++)
{
    if ($scheduledEventsResponse.Events[$eventIdx].Resources[0].ToLower().substring(1) -eq $env:COMPUTERNAME.ToLower())
    {    
        # YOUR LOGIC HERE 
         pause "This Virtual Machine is scheduled for to "+ $scheduledEventsResponse.Events[$eventIdx].EventType

        # Acknoledge the event to expedite
        $jsonResp = "{""StartRequests"" : [{ ""EventId"": """+$scheduledEventsResponse.events[$eventIdx].EventId +"""}]}"
        $respbody = convertto-JSon $jsonResp
       
        Invoke-RestMethod -Uri $ScheduledEventURI  -Headers @{"Metadata"="true"} -Method POST -Body $jsonResp 
    }
}


``` 


## <a name="c-sample"></a>Exemple de code C\# 
Le code ci-dessous désigne un client qui présente des API afin de communiquer avec le service de métadonnées
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
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

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
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
[Maintenance planifiée des machines virtuelles dans Azure](./virtual-machines-linux-planned-maintenance.md)



<!--HONumber=Jan17_HO1-->


