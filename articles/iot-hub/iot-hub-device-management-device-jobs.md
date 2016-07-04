<properties
	pageTitle="Gestion des appareils IoT Hub - Travaux d’appareils | Microsoft Azure"
	description="Didacticiel sur la gestion des appareils Azure IoT Hub décrivant l’utilisation des travaux d’appareils pour effectuer des opérations telles que la mise à jour du microprogramme à distance."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Didacticiel : Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils (version préliminaire)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Introduction
La gestion des appareils IoT Azure vous permet d’interagir avec les appareils physiques à l’aide de travaux d’appareil. Une fois que vous avez identifié la représentation d’appareil (la représentation de service d’un appareil physique), vous pouvez interagir avec son appareil physique correspondant à l’aide de travaux d’appareil. Les travaux d’appareil activent la coordination de processus complexes sur plusieurs appareils. Ce processus peut inclure plusieurs étapes et des opérations longues.

Pour le moment, il existe six types de travaux d’appareil qui sont fournis par la gestion des appareils Azure IoT Hub (nous allons ajouter des travaux supplémentaires en fonction des besoins des clients) :

- **Mise à jour du microprogramme** : met à jour le microprogramme (ou l’image de système d’exploitation) sur l’appareil physique.
- **Redémarrage** : redémarre l’appareil physique.
- **Réinitialisation aux paramètres d’usine** : rétablit le microprogramme (ou l’image du système d’exploitation) de l’appareil physique vers une image de sauvegarde d’usine, stockée sur l’appareil.
- **Mise à jour de la configuration** : configure l’agent client IoT Hub en cours d’exécution sur l’appareil physique.
- **Lecture de la propriété de l’appareil** : obtient la dernière valeur d’une propriété de l’appareil sur l’appareil physique.
- **Écriture de la propriété de l’appareil** : modifie une propriété de l’appareil sur l’appareil physique.

Pour plus d’informations sur l’utilisation de chacun de ces travaux, consultez la [Documentation sur les API][lnk-apidocs].

Vous pouvez interroger l’historique des travaux afin de comprendre l’état des travaux que vous avez démarrés. Pour des exemples de requêtes, consultez [notre bibliothèque d’expressions de requête][lnk-query-samples].

## Utilisation des travaux d’appareils pour effectuer la mise à jour du microprogramme : architecture

Le diagramme ci-dessous illustre un travail d’appareil de mise à jour du microprogramme interagissant avec un seul appareil physique.

![][img-architecture]

Étapes du travail d’appareil de mise à jour du microprogramme :

1.  La solution IoT cloud démarre le travail d’appareil de mise à jour du microprogramme et fournit l’URI de l’emplacement du package du microprogramme. Le rôle de la solution IoT est de placer le package du microprogramme dans un emplacement où l’appareil pourra le télécharger.

2.  Lorsque l’appareil physique reçoit cet URI, il lance automatiquement le téléchargement à partir de l’URI fourni.

3.  Votre code sur l’appareil reçoit la demande de IoT Hub et télécharge le package du microprogramme à partir de l’URI d’emplacement fourni.

4.  Après avoir reçu le message d’état de fin du téléchargement sur l’appareil, le travail d’appareil demande à l’appareil d’appliquer l’image de microprogramme téléchargée.

5.  Une fois que l’appareil a appliqué l’image de microprogramme, il redémarre, se reconnecte à l’IoT Hub et informe l’IoT Hub que le nouveau microprogramme a été appliqué, puis que le travail d’appareil est terminé.

## Exécution de l’exemple de mise à jour du microprogramme

L’exemple suivant étend la fonctionnalité du didacticiel [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. Une fois les différents appareils simulés en cours d’exécution, il démarrera un travail pour mettre à jour le microprogramme sur chacun d’entre eux.

### Configuration requise 

Avant d’exécuter cet exemple, vous devez avoir terminé les étapes de [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. Cela signifie que vos appareils simulés doivent être en cours d’exécution. Si vous avez terminé le processus auparavant, redémarrez immédiatement vos appareils simulés.

### Démarrage de l’exemple

Pour démarrer l’exemple, vous devez exécuter le processus **FirmwareUpdate.exe**. Celui-ci démarre le processus de mise à jour du microprogramme sur tous les appareils simulés. Procédez comme suit pour démarrer l’exemple :

1.  À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au dossier **azure-iot-sdks\\csharp\\service\\samples\\bin**.  

2.  Exécutez `FirmwareUpdate.exe <IoT Hub Connection String>`.

Dans la fenêtre de ligne de commande, vous devriez voir la sortie illustrant sept travaux d’appareils suivant la mise à jour de microprogramme simulé sur les six appareils simulés.

### Démarrage d’un travail d’appareil

En règle générale, les travaux d’appareils sont démarrés à l’aide d’un certain nombre de méthodes **Schedule&lt;Nom\_Travail&gt;Async** sur l’instance **JobClient**. Dans l’exemple de mise à jour du microprogramme, nous appelons la méthode **ScheduleFirmwareUpdateAsync**. Étant donné que nous transmettons un tableau avec 6 ID d’appareils, 7 travaux d’appareils sont démarrés, un pour chaque appareil mis à jour et un travail d’appareil parent utilisé pour suivre les 6 autres.

Dans l’extrait de code ci-dessous, à partir du fichier **Program.cs** dans le projet **FirmwareUpdate**, un travail de mise à jour du microprogramme est démarré. L’appel prend un tableau de chaînes de valeurs **deviceId** comme paramètre, représentant les appareils que nous souhaitons mettre à jour.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### Interrogation de l’historique des travaux

Dans l’exemple, une liste des appareils qui exécutent activement un travail d’appareil s’affiche régulièrement. Lorsqu’un travail d’appareil est terminé, l’appareil associé est supprimé de la liste affichée. La figure suivante est une capture d’écran de l’exécution de **FirmwareUpdate.exe** :

![][img-output1]

La liste ci-dessus est générée en lançant une requête pour tous les travaux actifs, comme illustré dans l’extrait de code ci-dessous à partir du fichier **Program.cs** du projet **FirmwareUpdate** :

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

Une fois le travail d’appareil parent terminé, l’exemple génère une liste de tous les travaux d’appareils, comme illustré dans la figure ci-dessous. Le travail parent se termine seulement une fois les travaux enfants associés terminés. Dans la capture d’écran ci-dessous, le travail parent est le dernier travail de la liste. Vous pouvez la voir car **ParentJobId** vaut **''**. Le travail parent comporte également le champ **Reason** défini sur une chaîne qui indique le résultat agrégé de la requête. Dans ce cas, il indique que 6 appareils ont été mis à jour et que tous ont réussi.

![][img-output2]

La liste ci-dessus est générée par l’interrogation de tous les travaux et le tri par l’heure de démarrage. Les propriétés de l’objet **JobResponse**, illustrées ci-dessous, peuvent être utilisées pour interroger l’historique des travaux d’appareils.

![][img-properties]

Pour plus d’exemples d’interrogation de l’historique des travaux, consultez [notre bibliothèque d’expressions de requête][lnk-query-samples].

### Détails d’implémentation d’un simulateur d’appareil

Dans les sections précédentes, nous vous avez montré les détails d’implémentation de la mise à jour du microprogramme du point de vue du service (comment démarrer un travail d’appareil et lancer une requête sur son état). Maintenant, nous allons étudier l’implémentation correspondante côté appareil.

La bibliothèque cliente de gestion des appareils Azure IoT Hub gère la communication entre l’appareil et le service, si bien qu’il ne reste plus qu’à implémenter la logique spécifique à l’appareil. Cette étape est en deux parties :

- Implémenter le processus de mise à jour du microprogramme spécifique à l’appareil : cela implique d’écrire la logique spécifique à l’appareil pour télécharger le package du microprogramme et appliquer la mise à jour dans les rappels appropriés répertoriés ci-dessous. Dans l’exemple simulé, ceci est implémenté dans [l’exemple][lnk-github-firmware] \:

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- Informer le service du processus de mise à jour du microprogramme : cela implique de modifier les champs d’état de mise à jour du microprogramme et de résultat de mise à jour du microprogramme. Pour cela, vous devez appeler les API **set\_firmwareupdate\_state** et **set\_firmwareupdate\_updateresult**. Dans l’exemple simulé, ceci est implémenté dans [l’exemple][lnk-github-firmware].

## Étapes suivantes

Pour en savoir plus sur les fonctionnalités de la gestion des appareils Azure IoT Hub, vous pouvez parcourir les didacticiels suivants :

- La bibliothèque cliente Azure IoT Hub DM fournit un exemple de bout en bout utilisant un [appareil Intel Edison][lnk-edison].

- [Utilisation des représentations d’appareil physique][lnk-twin-tutorial]

- [Recherche de représentations d’appareil physique à l’aide de requêtes][lnk-tutorial-queries]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0622_2016-->