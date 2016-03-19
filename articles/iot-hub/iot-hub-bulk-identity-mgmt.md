<properties
 pageTitle="Importer/exporter des identités d’appareil IoT Hub | Microsoft Azure"
 description="Concepts et extraits de code .NET pour la gestion en bloc des identités d’appareils IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Gestion en bloc des identités d’appareils IoT Hub

Chaque IoT Hub dispose d’un registre d’identité des appareils servant à créer des ressources par appareil dans le service, par exemple, une file d’attente contenant des messages actuels envoyés du cloud vers l’appareil. En outre, il autorise l’accès aux points de terminaison des appareils. Cet article explique comment importer et exporter les identités des appareils en bloc vers et à partir d’un registre d’identité d’appareil.

Les opérations d’importation et d’exportation se déroulent dans le cadre de *Tâches*, qui permettent aux utilisateurs d’exécuter des opérations de service en bloc par rapport à un IoT Hub.

La classe **RegistryManager** comprend les méthodes **ExportDevicesAsync** et **ImportDevicesAsync**, qui utilisent l’infrastructure des **tâches**. Ces méthodes vous permettent d’exporter, d’importer et de synchroniser l’intégralité d’un registre d’appareil IoT hub.

## Qu’est-ce que les tâches ?

Les opérations de registre d’identité des appareils utilisent le système de **tâches** lorsque l’opération :

*  présente un temps d’exécution long par rapport à une opération de runtime standard ou
*  renvoie une grande quantité de données à l’utilisateur.

Dans ces cas, au lieu d’avoir une seule API d’appel en attente ou qui se bloque sur le résultat de l’opération, l’opération crée de façon asynchrone une **tâche** pour cet IoT Hub. Puis, l’opération renvoie immédiatement un objet **JobProperties**.

L’extrait de code C# suivant indique comment créer une tâche d’exportation :

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Vous pouvez utiliser la classe **RegistryManager** pour interroger l’état de la **tâche** à l’aide des métadonnées **JobProperties** renvoyées.

L’extrait de code C# suivant indique comment envoyer des interrogations toutes les cinq secondes pour voir si la tâche a terminé son exécution :

```
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

## Exporter des appareils

Utilisez la méthode **ExportDevicesAsync** pour exporter l’intégralité d’un registre d’appareil IoT Hub vers un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à l’aide d’une [signature d’accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

Cette méthode vous permet de créer des sauvegardes fiables de vos informations d’appareil dans un conteneur d’objets blob que vous contrôlez.

La méthode **ExportDevicesAsync** requiert deux paramètres :

*  Une *chaîne* qui contient l’URI d’un conteneur d’objets blob. Cet URI doit contenir un jeton SAP, qui accorde l’accès en écriture au conteneur. La tâche crée un objet blob de blocs dans ce conteneur pour stocker les données d’exportation d’appareil sérialisées. Le jeton SAP doit inclure ces autorisations :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  Un *booléen* qui indique si vous souhaitez exclure les clés d’authentification de vos données d’exportation. Si la valeur est **false**, des clés d’authentification sont incluses dans la sortie d’exportation ; dans le cas contraire, les clés sont exportées sous forme de valeur **null**.

L’extrait de code C# suivant montre comment lancer une tâche d’exportation, puis effectuer une interrogation de fin d’exécution :

```
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

Voici un exemple de données de sortie :

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si vous avez besoin d’accéder à ces données sous forme de code, vous pouvez facilement désérialiser ces données à l’aide de la classe **ExportImportDevice**. L’extrait de code C# suivant montre comment lire les informations d’appareil qui ont été exportées précédemment vers un objet blob de bloc :

```
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

> [AZURE.NOTE]  Vous pouvez également utiliser la méthode **GetDevicesAsync** de la classe **RegistryManager** pour extraire une liste de vos appareils. Toutefois, cette approche a une capacité stricte de 1 000 en ce qui concerne le nombre d’objets d’appareil renvoyés. La méthode **GetDevicesAsync** est surtout envisagée pour l’aide au débogage lors de scénarios de développement. Elle n’est pas recommandée pour les charges de travail de production.

## Importer des appareils

La méthode **ImportDevicesAsync** de la classe **RegistryManager** vous permet d’effectuer des opérations d’importation et de synchronisation en bloc dans un registre d’appareil IoT Hub. À la manière de la méthode **ExportDevicesAsync**, la méthode **ImportDevicesAsync** utilise l’infrastructure de Tâches.

Faites attention lors de l’utilisation de la méthode **ImportDevicesAsync**. Celle-ci peut, en plus d’approvisionner de nouveaux appareils dans le registre d’identité des appareils, mettre à jour et supprimer des appareils existants.

> [AZURE.WARNING]  Il est impossible d’annuler une opération d’importation. Vous devez toujours sauvegarder vos données existantes vers un autre conteneur d’objets blob, à l’aide de la méthode **ExportDevicesAsync**, avant de faire des modifications en bloc dans le registre d’identité des appareils.

La méthode **ImportDevicesAsync** requiert deux paramètres :

*  Une *chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) constituant l’*entrée* de la tâche. Cet URI doit contenir un jeton SAP qui accorde l’accès en lecture au conteneur. Ce conteneur doit inclure un objet blob sous le nom **devices.txt** contenant les données d’appareil sérialisées pour importation dans le registre d’identité des appareils. Les données d’importation doivent contenir des informations sur l’appareil au même format JSON que celui créé par la tâche **ExportImportDevice**. Le jeton SAP doit inclure ces autorisations :

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Une *chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) constituant la *sortie* de la tâche. La tâche crée un objet blob de blocs dans ce conteneur pour stocker toute information d’erreur à partir de la **tâche** d’importation terminée. Le jeton SAP doit inclure ces autorisations :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Les deux paramètres peuvent pointer vers le même conteneur d’objets blob. Les paramètres distincts permettent simplement davantage de contrôle sur vos données, étant donné que le conteneur de sortie requiert des autorisations supplémentaires.

L’extrait de code C# suivant indique comment créer une tâche d’importation :

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Comportement d’importation

Vous pouvez utiliser la méthode **ImportDevicesAsync** pour effectuer les opérations en bloc suivantes dans le registre d’identité des appareils :

-   Inscription en bloc de nouveaux appareils
-   Suppression en bloc d’appareils existants
-   Modifications d’état en bloc (activer ou désactiver des appareils)
-   Attribution en bloc de nouvelles clés d’authentification d’appareils
-   Auto-régénération en bloc des clés d’authentification d’appareils

Vous pouvez effectuer n’importe quelle combinaison des opérations ci-dessus en un seul appel **ImportDevicesAsync**. Vous pouvez, par exemple, inscrire de nouveaux appareils, tout en supprimant ou mettant à jour des appareils existants. Lorsqu’il est utilisé avec la méthode **ExportDevicesAsync**, tous les appareils peuvent être migrés complètement à partir d’un IoT Hub vers un autre.

Vous pouvez contrôler le processus d’importation par appareil en utilisant la propriété facultative **importMode** dans les données d’importation sérialisées pour chaque appareil. La propriété **importMode** propose les options suivantes :

| importMode | Description |
| -------- | ----------- |
| **createOrUpdate** | Si un appareil n’existe pas avec l’**id** spécifié, ce dernier a été inscrit récemment. <br/>Si l’appareil existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, sans tenir compte de la valeur **ETag**. |
| **create** | Si un appareil n’existe pas avec l’**id** spécifié, ce dernier a été inscrit récemment. <br/>Si l’appareil existe déjà, une erreur est consignée dans le fichier journal. |
| **update** | Si un appareil avec l’**id** spécifié existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, sans tenir compte de la valeur **ETag**. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. |
| **updateIfMatchETag** | Si un appareil avec l’**id** spécifié existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, mais uniquement si la valeur **ETag** correspond. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. <br/>En cas d’incompatibilité de valeur **ETag**, une erreur est consignée dans le fichier journal. |
| **createOrUpdateIfMatchETag** | Si un appareil n’existe pas avec l’**id** spécifié, ce dernier a été inscrit récemment. <br/>Si un appareil existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, mais uniquement si la valeur **ETag** correspond. <br/>En cas d’incompatibilité de valeur **ETag**, une erreur est consignée dans le fichier journal. |
| **delete** | Si un appareil avec l’**id** spécifié existe déjà, il est supprimé sans tenir compte de la valeur **ETag**. <br/>Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. |
| **deleteIfMatchETag** | Si un appareil avec l’**id** spécifié existe déjà, il est supprimé, mais uniquement si la valeur **ETag** correspond. Si l’appareil n’existe pas, une erreur est consignée dans le fichier journal. <br/>En cas d’incompatibilité de valeur ETag, une erreur est consignée dans le fichier journal. |

> [AZURE.NOTE] Si les données de sérialisation ne définissent pas explicitement un indicateur **importMode** pour un appareil donné, sa valeur par défaut sera **createOrUpdate** pendant l’opération d’importation.

## Exemple d’importation d’appareils : approvisionnement d’appareils en bloc 

L’exemple de code C# suivant illustre comment générer plusieurs identités d’appareils comprenant des clés d’authentification, comment écrire ces informations d’appareil dans un objet blob de blocs Azure Storage et comment importer les appareils dans le registre d’identité des appareils :

```
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

// Write this list to the Azure storage blob
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

// Call import using the same storage blob to add new devices!
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

## Exemple d’importation d’appareils : suppression en bloc

L’exemple de code suivant montre comment supprimer les appareils ajoutés à l’aide de l’exemple de code précédent :

```
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

// Step 3: Call import using the same storage blob to delete all devices!
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

## Obtenir l’URI de SAP de conteneur


L’exemple de code suivant montre comment générer un [URI de SAP](../storage/storage-dotnet-shared-access-signature-part-2.md) avec des autorisations de lecture, d’écriture et de suppression pour un conteneur d’objets blob :

```
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

## Étapes suivantes

Dans cet article, vous avez appris comment effectuer des opérations en bloc sur le registre d’identité des appareils dans un IoT Hub. Vous pouvez continuer à explorer les fonctionnalités d’IoT Hub et d’autres scénarios IoT dans les articles suivants :

- [Créer un IoT Hub par programme](iot-hub-rm-template.md)
- [Mesures d’utilisation d’IoT Hub](iot-hub-metrics.md)
- [Surveillance des opérations IoT Hub](iot-hub-operations-monitoring.md)

<!---HONumber=AcomDC_0211_2016-->