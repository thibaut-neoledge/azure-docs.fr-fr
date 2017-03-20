---
title: "Utiliser une capture de paquets pour effectuer une surveillance proactive du réseau avec Azure Functions | Microsoft Docs"
description: "Cet article décrit la création d’une capture de paquets déclenchée par des alertes avec Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Utiliser une capture de paquets pour effectuer une surveillance proactive du réseau avec Azure Functions

La fonctionnalité de capture des paquets Network Watcher crée des sessions de capture afin d’effectuer le suivi du trafic en direction et en provenance d’une machine virtuelle. Le fichier de capture peut contenir un filtre défini pour suivre uniquement le trafic que vous souhaitez analyser. Ces données sont ensuite stockées dans un objet blob de stockage ou localement sur l’ordinateur invité. Cette fonctionnalité peut être démarrée à distance à partir d’autres scénarios d’automatisation comme Azure Functions. La capture de paquets permet d’exécuter des captures proactives en fonction des anomalies définies sur le réseau. Elle permet aussi de collecter des statistiques réseau, d’obtenir des informations sur les intrusions, de déboguer des communications client-serveur, etc.

Les ressources déployées dans Azure s’exécutent 24 heures sur 24, 7 jours sur 7. Ni votre équipe ni vous ne pouvez surveiller activement l’état de toutes les ressources 24 heures sur 24, 7 jours sur 7. Que se passe-t-il si un problème se produit à 2 h ?

En utilisant Network Watcher, Alerte et Fonctions dans l’écosystème Azure, vous pouvez répondre de façon proactive aux problèmes de votre réseau avec les données et les outils permettant de résoudre le problème.

## <a name="before-you-begin"></a>Avant de commencer

Dans cet exemple, votre machine virtuelle envoie plus de segments TCP que d’habitude, et vous souhaitez être alerté. Les segments TCP sont utilisés à titre d’exemple, et vous pouvez utiliser une condition d’alerte. Une fois alerté, vous souhaitez disposer des données du niveau des paquets pour comprendre pourquoi la communication a augmenté afin de pouvoir prendre des mesures pour rétablir une communication normale sur la machine.
Ce scénario suppose que vous possédez une instance existante de Network Watcher ainsi qu’un groupe de ressources avec une machine virtuelle valide à utiliser.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scénario

Pour automatiser ce processus, nous créons et connectons une alerte à notre machine virtuelle pour qu’elle se déclenche lorsque l’incident se produit et pour qu’une fonction Azure rappelle Network Watcher.

Ce scénario va :

* créer une fonction Azure qui démarrera une capture de paquets ;
* créer une règle d’alerte sur une machine virtuelle ;
* configurer la règle d’alerte pour appeler la fonction Azure.

## <a name="creating-an-azure-function-and-overview"></a>Création d’une fonction Azure et vue d’ensemble

La première étape consiste à créer une fonction Azure pour traiter l’alerte et créer une capture de paquets. 

La liste suivante est une vue d’ensemble du flux de travail qui a lieu.

1. Une alerte est déclenchée sur votre machine virtuelle.
1. L’alerte appelle votre fonction Azure via un webhook.
1. Votre fonction Azure traite l’alerte et démarre une session de capture de paquets Network Watcher.
1. La capture de paquets s’exécute sur la machine virtuelle et collecte le trafic. 
1. Le fichier de capture est chargé vers un compte de stockage pour la révision et le diagnostic 

Il est possible de créer une fonction Azure dans le portail en suivant l’article [Créer votre première fonction Azure](../azure-functions/functions-create-first-azure-function.md). Pour cet exemple, nous avons choisi une fonction de type HttpTrigger-CSharp. 

> [!NOTE]
> D’autres langages sont fournis avec les fonctions Azure, mais ils peuvent être expérimentaux et ne pas être entièrement pris en charge comme PowerShell et Python.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Traitement de l’alerte et démarrage d’une session de capture de paquets

À présent, il est temps d’appeler Network Watcher à partir de la fonction Azure. Selon les besoins, l’implémentation de cette fonction est différente. Toutefois, le flux général du code est ainsi :

1. Traiter les paramètres d’entrée
2. Interroger les captures de paquets existantes, vérifier les limites et résoudre les conflits de noms
3. Créer une capture de paquets avec les paramètres appropriés
4. Sonder régulièrement la capture de paquets jusqu’à la fin
5. Informer l’utilisateur de la fin de la session de capture de paquets

L’exemple suivant est en C#, langage qui peut être utilisé dans la fonction Azure. Certaines valeurs doivent être remplacées pour l’abonnement, l’ID client, l’ID de locataire et la clé secrète client.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

Une fois que vous avez créé votre fonction, vous devez configurer l’alerte pour appeler l’URL associée à la fonction. Pour obtenir cette valeur, copiez l’URL de la fonction à partir de votre application de fonctions.

![recherche de l’url de la fonction][2]

Si vous avez besoin de propriétés personnalisées dans la charge utile de la demande POST du webhook, consultez l’article [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurer une alerte sur une machine virtuelle

Des alertes peuvent être configurées pour informer qu’une mesure spécifique dépasse un seuil qui lui a été affecté. Dans cet exemple, l’alerte est définie sur les segments TCP envoyés, mais elle peut être déclenchée pour plusieurs autres mesures. Dans cet exemple, une alerte est configurée pour appeler un webhook afin qu’il appelle la fonction.

### <a name="create-the-alert-rule"></a>Créer la règle d’alerte

Accédez à une machine virtuelle existante et ajoutez une règle d’alerte. Une documentation plus détaillée sur la configuration des alertes se trouve dans l’article [Créer des alertes dans Azure Monitor pour les services Azure - Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![ajouter une règle d’alerte à une machine virtuelle][1]

> [!NOTE]
> Certaines mesures ne sont pas activées par défaut. Pour en savoir plus sur l’activation de mesures supplémentaires, consultez l’article [Activation de la surveillance et des diagnostics](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

Pour finir, collez l’URL de l’étape précédente dans la zone de texte Webhook de votre alerte. Cliquez sur **OK** pour enregistrer la règle d’alerte.

![collage de l’url dans la règle d’alerte][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Téléchargement et affichage du fichier de capture

Si vous enregistrez la capture dans un compte de stockage, le fichier de capture peut être téléchargé via le portail ou par programme. S’il est stocké localement, vous pouvez le récupérer en vous connectant à la machine virtuelle. 

Pour obtenir des instructions de téléchargement des fichiers à partir des comptes de stockage Azure, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/storage-dotnet-how-to-use-blobs.md). L’explorateur de stockage peut aussi être utilisé. Pour en savoir plus sur l’explorateur de stockage, cliquez sur le lien suivant : [Explorateur de stockage](http://storageexplorer.com/)

Une fois la capture téléchargée, vous pouvez l’afficher dans tout outil en mesure de lire un fichier **.cap**. Les liens de deux de ces outils sont indiqués ci-après :

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment afficher vos captures de paquets en consultant l’article [Packet capture analysis with Wireshark (Analyse de la capture de paquets avec Wireshark)](network-watcher-alert-triggered-packet-capture.md).

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

