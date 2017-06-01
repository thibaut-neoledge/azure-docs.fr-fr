---
title: "Importer/exporter des identités d’appareil Azure IoT Hub | Microsoft Docs"
description: "Utilisation du Kit de développement logiciel (SDK) de service Azure IoT pour effectuer des opérations en bloc dans le registre des identités pour importer et exporter les identités des appareils. Les opérations d’importation vous permettent de créer, de mettre à jour et de supprimer des identités d’appareils en bloc."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 9ea3896f73e0e97b89743d8b17c8fd1e723153c3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Gestion de vos identités d’appareil IoT Hub en bloc

Chaque IoT Hub dispose d’un registre des identités servant à créer des ressources par appareil dans le service, par exemple, une file d’attente contenant des messages actuels envoyés du cloud vers l’appareil. Le registre des identités vous permet également de contrôler l’accès aux points de terminaison orientés appareil. Cet article explique comment importer et exporter les identités des appareils en bloc vers et à partir d’un registre des identités.

Les opérations d’importation et d’exportation se déroulent dans le cadre de *Tâches* , qui vous permettent d’exécuter des opérations de service en bloc par rapport à un IoT Hub.

La classe **RegistryManager** comprend les méthodes **ExportDevicesAsync** et **ImportDevicesAsync**, qui utilisent l’infrastructure des **tâches**. Ces méthodes vous permettent d’exporter, d’importer et de synchroniser l’intégralité d’un registre des identités IoT hub.

## <a name="what-are-jobs"></a>Que sont les tâches ?

Les opérations de registre des identités utilisent le système de **tâches** lorsque l’opération :

* a un temps d’exécution potentiellement long par rapport à une opération d’exécution standard.
* renvoie une grande quantité de données à l’utilisateur.

Dans ces cas, au lieu d’avoir une seule API d’appel en attente ou qui se bloque sur le résultat de l’opération, l’opération crée de façon asynchrone un **travail** pour cet IoT Hub. L’opération renvoie alors immédiatement un objet **JobProperties**.

L’extrait de code C# suivant indique comment créer une tâche d’exportation :

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Pour utiliser la classe **RegistryManager** dans votre code C#, ajoutez le package NuGet **Microsoft.Azure.Devices** à votre projet. La classe **RegistryManager** se trouve dans l’espace de noms **Microsoft.Azure.Devices**.


Vous pouvez utiliser la classe **RegistryManager** pour interroger l’état de la **tâche** à l’aide des métadonnées **JobProperties** retournées.

L’extrait de code C# suivant indique comment envoyer des interrogations toutes les cinq secondes pour voir si la tâche a terminé son exécution :

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exporter des appareils

Utilisez la méthode **ExportDevicesAsync** pour exporter l’intégralité d’un registre des identités IoT Hub vers un conteneur d’objets blob [Azure Storage](../storage/index.md) à l’aide d’une [signature d’accès partagé](../storage/storage-security-guide.md#data-plane-security).

Cette méthode vous permet de créer des sauvegardes fiables de vos informations d’appareil dans un conteneur d’objets blob que vous contrôlez.

La méthode **ExportDevicesAsync** requiert deux paramètres :

* Une *chaîne* qui contient l’URI d’un conteneur d’objets blob. Cet URI doit contenir un jeton SAP, qui accorde l’accès en écriture au conteneur. La tâche crée un objet blob de blocs dans ce conteneur pour stocker les données d’exportation d’appareil sérialisées. Le jeton SAP doit inclure ces autorisations :

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* Un *booléen* qui indique si vous souhaitez exclure les clés d’authentification de vos données d’exportation. Si la valeur est **false**, des clés d’authentification sont incluses dans la sortie d’exportation. Dans le cas contraire, les clés sont exportées sous forme de valeur **null**.

L’extrait de code C# suivant montre comment lancer une tâche d’exportation qui inclut des clés d’authentification de l’appareil dans les données d’exportation, puis comment interroger l’exécution :

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

La tâche stocke sa sortie dans le conteneur d’objets blob fourni en tant qu’objet blob de blocs portant le nom **devices.txt**. Les données de sortie consistent en des données d’appareil JSON sérialisées, avec un appareil par ligne.

L’exemple ci-après illustre les données de sortie :

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si un appareil comporte des données de représentation, elles sont également exportées avec les données de l’appareil. L’exemple ci-après illustre ce format. Toutes les données à partir de la ligne « twinETag » jusqu'à la fin sont des données de représentation.
```
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Si vous avez besoin d’accéder à ces données sous forme de code, vous pouvez facilement désérialiser ces données à l’aide de la classe **ExportImportDevice** . L’extrait de code C# suivant montre comment lire les informations d’appareil qui ont été exportées précédemment vers un objet blob de bloc :

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> Vous pouvez également utiliser la méthode **GetDevicesAsync** de la classe **RegistryManager** pour extraire une liste de vos appareils. Toutefois, cette approche a une capacité stricte de 1 000 en ce qui concerne le nombre d’objets d’appareil renvoyés. La méthode **GetDevicesAsync** est surtout envisagée pour l’aide au débogage lors de scénarios de développement. Elle n’est pas recommandée pour les charges de travail de production.

## <a name="import-devices"></a>Importer des appareils

La méthode **ImportDevicesAsync** de la classe **RegistryManager** vous permet d’effectuer des opérations d’importation et de synchronisation en bloc dans un registre des identités IoT Hub. À la manière de la méthode **ExportDevicesAsync**, la méthode **ImportDevicesAsync** utilise l’infrastructure de **Tâches**.

Faites attention lors de l’utilisation de la méthode **ImportDevicesAsync**. Celle-ci peut, en plus d’approvisionner de nouveaux appareils dans le registre des identités, mettre à jour et supprimer des appareils existants.

> [!WARNING]
> Il est impossible d’annuler une opération d’importation. Sauvegardez toujours vos données existantes vers un autre conteneur d’objets blob, à l’aide de la méthode **ExportDevicesAsync**, avant de faire des modifications en bloc dans le registre des identités.

La méthode **ImportDevicesAsync** requiert deux paramètres :

* Une *chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](../storage/index.md) à utiliser comme *entrée* de la tâche. Cet URI doit contenir un jeton SAP qui accorde l’accès en lecture au conteneur. Ce conteneur doit inclure un objet blob du nom de **devices.txt** contenant les données d’appareil sérialisées pour importation dans le registre des identités. Les données d’importation doivent contenir des informations sur l’appareil au même format JSON que celui utilisé par la tâche **ExportImportDevice** lors de la création d’un objet blob **devices.txt**. Le jeton SAP doit inclure ces autorisations :

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* Une *chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à utiliser comme *sortie* de la tâche. La tâche crée un objet blob de blocs dans ce conteneur pour stocker toute information d’erreur à partir de la **tâche**d’importation terminée. Le jeton SAP doit inclure ces autorisations :

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Les deux paramètres peuvent pointer vers le même conteneur d’objets blob. Les paramètres distincts permettent simplement davantage de contrôle sur vos données, étant donné que le conteneur de sortie requiert des autorisations supplémentaires.

L’extrait de code C# suivant indique comment créer une tâche d’importation :

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Cette méthode peut également être utilisée pour importer les données pour la représentation de l’appareil. Le format des données d’entrée est le même que celui indiqué dans la section **ExportDevicesAsync**. De cette façon, les données exportées peuvent aussi être réimportées. Le $metadata est facultatif.

## <a name="import-behavior"></a>Comportement d’importation

Vous pouvez utiliser la méthode **ImportDevicesAsync** pour effectuer les opérations en bloc suivantes dans le registre des identités :

* Inscription en bloc de nouveaux appareils
* Suppression en bloc d’appareils existants
* Modifications d’état en bloc (activer ou désactiver des appareils)
* Attribution en bloc de nouvelles clés d’authentification d’appareils
* Auto-régénération en bloc des clés d’authentification d’appareils
* Mise à jour en bloc de données de représentation

Vous pouvez effectuer n’importe quelle combinaison des opérations précédentes en un seul appel **ImportDevicesAsync** . Vous pouvez, par exemple, inscrire de nouveaux appareils, tout en supprimant ou mettant à jour des appareils existants. Lorsqu’il est utilisé avec la méthode **ExportDevicesAsync** , tous les appareils peuvent être migrés complètement à partir d’un IoT Hub vers un autre.

Si le fichier d’importation spécifie les métadonnées de représentation, ces métadonnées remplacent les métadonnées existantes de la représentation. Si ce n’est pas le cas, seuls les métadonnées `lastUpdateTime` sont mises en utilisant l’heure en cours. 

Vous pouvez contrôler le processus d’importation par appareil en utilisant la propriété facultative **importMode** dans les données d’importation sérialisées pour chaque appareil. La propriété **importMode** propose les options suivantes :

| importMode | Description |
| --- | --- |
| **createOrUpdate** |Si un appareil n’existe pas avec l’ **ID**spécifié, ce dernier a été inscrit récemment. <br/>Si l’appareil existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, sans tenir compte de la valeur **ETag** . <br> L’utilisateur peut éventuellement spécifier des données de représentation avec les données de l’appareil. L’etag de la représentation, si elle est spécifiée, est traitée indépendamment de l’etag de l’appareil. S’il existe une incompatibilité avec l’etag existant de la représentation, une erreur est consignée dans le fichier journal. |
| **create** |Si un appareil n’existe pas avec l’ **ID**spécifié, ce dernier a été inscrit récemment. <br/>Si l’appareil existe déjà, une erreur est consignée dans le fichier journal. <br> L’utilisateur peut éventuellement spécifier des données de représentation avec les données de l’appareil. L’etag de la représentation, si elle est spécifiée, est traitée indépendamment de l’etag de l’appareil. S’il existe une incompatibilité avec l’etag existant de la représentation, une erreur est consignée dans le fichier journal. |
| **update** |Si un appareil avec **l’ID** spécifié existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, sans tenir compte de la valeur **ETag**. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. |
| **updateIfMatchETag** |Si un appareil avec **l’ID** spécifié existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, mais uniquement si la valeur **ETag** correspond. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. <br/>En cas d’incompatibilité de valeur **ETag** , une erreur est consignée dans le fichier journal. |
| **createOrUpdateIfMatchETag** |Si un appareil n’existe pas avec l’ **ID**spécifié, ce dernier a été inscrit récemment. <br/>Si un appareil existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, mais uniquement si la valeur **ETag** correspond. <br/>En cas d’incompatibilité de valeur **ETag** , une erreur est consignée dans le fichier journal. <br> L’utilisateur peut éventuellement spécifier des données de représentation avec les données de l’appareil. L’etag de la représentation, si elle est spécifiée, est traitée indépendamment de l’etag de l’appareil. S’il existe une incompatibilité avec l’etag existant de la représentation, une erreur est consignée dans le fichier journal. |
| **delete** |Si un appareil avec **l’ID** spécifié existe déjà, il est supprimé sans tenir compte de la valeur **ETag**. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. |
| **deleteIfMatchETag** |Si un appareil avec **l’ID** spécifié existe déjà, il est supprimé, mais uniquement si la valeur **ETag** correspond. Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. <br/>En cas d’incompatibilité de valeur ETag, une erreur est consignée dans le fichier journal. |

> [!NOTE]
> Si les données de sérialisation ne définissent pas explicitement un indicateur **importMode** pour un appareil donné, sa valeur par défaut sera **createOrUpdate** pendant l’opération d’importation.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exemple d’importation d’appareils : approvisionnement d’appareils en bloc

L’exemple de code C# suivant illustre comment générer plusieurs identités d’appareil qui :

* Comprennent des clés d’authentification.
* Écrivent ces informations d’appareil sur un objet blob de blocs.
* Importent les appareils dans le registre des identités.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemple d’importation d’appareils : suppression en bloc

L’exemple de code suivant montre comment supprimer les appareils ajoutés à l’aide de l’exemple de code précédent :

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="get-the-container-sas-uri"></a>Obtenir l’URI SAS du conteneur

L’exemple de code suivant montre comment générer un [URI de SAP](../storage/storage-dotnet-shared-access-signature-part-2.md) avec des autorisations de lecture, d’écriture et de suppression pour un conteneur d’objets blob :

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment effectuer des opérations en bloc dans le registre des identités dans un IoT Hub. Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Métriques d’IoT Hub][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Simulation d’un appareil avec IoT Edge][lnk-iotedge]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

