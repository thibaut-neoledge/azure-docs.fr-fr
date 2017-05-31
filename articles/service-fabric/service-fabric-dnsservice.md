---
title: "Service DNS dans Azure Service Fabric | Microsoft Docs"
description: "Utilisez le service DNS de Service Fabric pour détecter des microservices depuis le cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e0f6a3a91207b73320d60a498d635262ef730d89
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="dns-service-in-azure-service-fabric"></a>Service DNS dans Azure Service Fabric
Le service DNS est un service système facultatif que vous pouvez activer dans votre cluster pour détecter d’autres services utilisant le protocole DNS.

De nombreux services, en particulier les services en conteneur, peuvent avoir un nom d’URL existant, et il est très pratique de pouvoir utiliser le protocole DNS standard (plutôt que le protocole d’affectation de noms), notamment dans les scénarios impliquant le développement et le transfert d’applications. Le service DNS vous permet de mapper les noms DNS à un nom de service et, par conséquent, de résoudre les adresses IP des points de terminaison. 

Comme le montre l’illustration suivante, le service DNS (exécuté dans le cluster Service Fabric), mappe les noms DNS aux noms de service qui sont ensuite résolus par le service d’affectation de noms pour renvoyer les adresses de point de terminaison auxquelles se connecter. Le nom DNS du service est fourni au moment de la création. 

![points de terminaison de service][0]

## <a name="enabling-the-dns-service"></a>Activation du service DNS
Vous devez d’abord activer le service DNS dans votre cluster. Récupérez le modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez soit utiliser les [exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype), soit créer un modèle Resource Manager. Vous pouvez activer le service DNS en procédant comme suit :

1. Tout d’abord, vérifiez que `apiversion` est défini sur `2017-07-01-preview` pour la ressource `Microsoft.ServiceFabric/clusters`, comme indiqué dans l’extrait de code suivant. Si ce n’est pas le cas, vous devez mettre à jour `apiVersion` en définissant la valeur `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le service DNS en ajoutant la section `addonFeatures` suivante après la section `fabricSettings`, comme indiqué ci-dessous

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Après avoir mis à jour votre modèle de cluster avec ces modifications, appliquez-les et laissez la mise à niveau s’accomplir. Une fois celle-ci terminée, vous pouvez voir, sous la section des services système du Service Fabric Explorer, que le service système DNS appelé `fabric:/System/DnsService` s’exécute dans votre cluster. 

## <a name="setting-the-dns-name-for-your-service"></a>Configuration du nom DNS de votre service
Maintenant que le service DNS est en cours d’exécution dans votre cluster, vous pouvez définir un nom DNS pour vos services, soit de manière déclarative pour les services par défaut dans le fichier `ApplicationManifest.xml`, soit à l’aide de Powershell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Définition du nom DNS pour un service par défaut du paramètre dans le fichier ApplicationManifest.xml
Ouvrez votre projet dans Visual Studio ou dans votre éditeur favori, puis ouvrez le fichier `ApplicationManifest.xml`. Accédez à la section des services par défaut puis, pour chaque service, ajoutez l’attribut `ServiceDnsName`. L’exemple suivant montre comment définir le nom DNS du service sur `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Déployez maintenant votre application. Une fois l’application déployée, accédez à l’instance de service dans Service Fabric Explorer pour voir le nom DNS de cette instance, comme illustré ci-dessous. 

![points de terminaison de service][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Définition du nom DNS d’un service à l’aide de Powershell
Vous pouvez définir le nom DNS d’un service lors de sa création à l’aide de la commande Powershell `New-ServiceFabricService`. L’exemple suivant permet de créer un service sans état avec le nom DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Utilisation de DNS dans vos services
Si vous déployez plusieurs services, vous pouvez utiliser un nom DNS pour trouver les points de terminaison d’autres services avec lesquels communiquer. Cela s’applique uniquement aux services sans état, puisque le protocole DNS ne sait pas comment communiquer avec les services avec état. Pour les services avec état, vous pouvez utiliser le proxy inverse intégré pour les appels http afin d’appeler une partition de service particulière.

Le code suivant montre comment appeler un autre service, sous la forme d’un simple appel http standard. Notez que vous devez spécifier le port et un chemin d’accès facultatif dans l’URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la communication de service au sein du cluster, consultez l’article [Se connecter aux services et communiquer avec eux dans Service Fabric](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG

