---
title: "Création d’une règle personnalisée dans Azure IoT Suite | Microsoft Docs"
description: "Comment créer une règle personnalisée dans une solution IoT Suite préconfigurée."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 3d24c0c29da8c97e5fcff524dc88746982fda77c
ms.openlocfilehash: a34a30a301e213ad227c21196a0b6299dbc4d357


---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Création d’une règle personnalisée dans la solution de surveillance à distance préconfigurée

## <a name="introduction"></a>Introduction

Dans les solutions préconfigurées, vous pouvez configurer [des règles qui se déclenchent lorsqu’une valeur de télémétrie d’un périphérique atteint un seuil spécifique][lnk-builtin-rule]. La page [Utilisation de la télémétrie dynamique avec la solution préconfigurée de surveillance à distance][lnk-dynamic-telemetry] vous explique comment ajouter des valeurs de télémétrie personnalisées, telles que *ExternalTemperature*, à votre solution. Cet article vous montre comment créer une règle personnalisée pour les types de télémétrie dynamiques dans votre solution.

Ce didacticiel utilise un appareil simulé Node.js simple pour générer la télémétrie dynamique à envoyer vers le serveur principal de la solution préconfigurée. Vous ajoutez ensuite des règles personnalisées dans la solution Visual Studio **RemoteMonitoring** et déployez ce serveur principal personnalisé sur votre abonnement Azure.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].
* [Node.js] [ lnk-node] version 0.12.x ou ultérieure pour créer un appareil simulé.
* Microsoft Visual Studio 2015 pour modifier le serveur principal de la solution de préconfigurée avec vos nouvelles règles.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

Notez le nom de solution que vous avez choisi pour votre déploiement. Vous aurez besoin de ce nom plus tard dans ce didacticiel.

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

Vous pouvez arrêter l’application de console Node.js lorsque vous avez vérifié qu’il envoie la télémétrie **ExternalTemperature** à la solution préconfigurée. Gardez la fenêtre de console ouverte car vous exécuterez à nouveau cette application de console Node.js après avoir ajouté la règle personnalisée à la solution.

## <a name="rule-storage-locations"></a>Emplacements de stockage des règles

Les informations sur les règles sont conservées dans deux emplacements :

* Table **DeviceRulesNormalizedTable** : cette table stocke une référence normalisée aux règles définies par le portail de la solution. Lorsque le portail de la solution affiche les règles des appareils, il interroge cette table sur les définitions de règles.
* Objet blob **DeviceRules** : cet objet blob stocke toutes les règles définies pour tous les appareils inscrits et est défini comme une entrée de référence pour les travaux Azure Stream Analytics.
 
Lorsque vous mettez à jour une règle existante ou définissez une nouvelle règle dans le portail de la solution, la table et l’objet blob sont mis à jour pour refléter les modifications. La définition des règles affichée dans le portail provient du magasin de tables et la définition des règles référencée par les travaux Stream Analytics provient de l’objet blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Mise à jour de la solution Visual Studio RemoteMonitoring

Les étapes suivantes vous montrent comment modifier la solution Visual Studio RemoteMonitoring pour inclure une nouvelle règle qui utilise la télémétrie **ExternalTemperature** envoyée depuis l’appareil simulé :

1. Si vous ne l'avez pas déjà fait, clonez le référentiel **azure-iot-remote-monitoring** dans un emplacement approprié sur votre ordinateur local à l’aide de la commande Git suivante :

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Dans Visual Studio, ouvrez le fichier RemoteMonitoring.sln à partir de votre copie locale du référentiel **azure-iot-remote-monitoring**.

3. Ouvrez le fichier Infrastructure\Models\DeviceRuleBlobEntity.cs et ajoutez une propriété **ExternalTemperature** comme suit :

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Dans le même fichier, ajoutez une propriété **ExternalTemperatureRuleOutput** comme suit :

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Ouvrez le fichier Infrastructure\Models\DeviceRuleDataFields.cs et ajoutez la propriété **ExternalTemperature** suivante après la propriété **Humidity** existante :

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Dans le même fichier, mettez à jour la méthode **_availableDataFields** pour inclure **ExternalTemperature** comme suit :

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Ouvrez le fichier Infrastructure\Repository\DeviceRulesRepository.cs et modifiez la méthode **BuildBlobEntityListFromTableRows** comme suit :

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Procédez maintenant à la régénération et au redéploiement de la solution.

Vous pouvez maintenant déployer la solution mise à jour sur votre abonnement Azure.

1. Ouvrez une invite de commandes avec élévation de privilèges et accédez à la racine de votre copie locale du référentiel azure-iot-remote-monitoring.

2. Pour déployer votre solution mise à jour, exécutez la commande suivante en remplaçant **{deployment name}** par le nom du déploiement de votre solution préconfigurée que vous avez noté précédemment :

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Mise à jour du travail Stream Analytics

Lorsque le déploiement est terminé, vous pouvez mettre à jour le travail Stream Analytics pour utiliser les nouvelles définitions de règles.

1. Dans le portail Azure, accédez au groupe de ressources contenant les ressources de votre solution préconfigurée. Ce groupe de ressources possède le même nom que vous avez spécifié pour la solution lors du déploiement.

2. Accédez au travail Stream Analytics {deployment name}-Rules. 

3. Cliquez sur **Arrêter** pour arrêter l’exécution du travail Stream Analytics. (Vous devez attendre l’arrêt du travail de diffusion avant de pouvoir modifier la requête).

4. Cliquez sur **Requête**. Modifiez la requête afin d’inclure l’instruction **SELECT** pour **ExternalTemperature**. L’exemple suivant montre la requête complète avec la nouvelle instruction **SELECT** :

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Cliquez sur **Enregistrer** pour modifier la requête de la règle mise à jour.

6. Cliquez sur **Démarrer** pour redémarrer le travail Stream Analytics.

## <a name="add-your-new-rule-in-the-dashboard"></a>Ajout de votre nouvelle règle dans le tableau de bord

Vous pouvez maintenant ajouter la règle **ExternalTemperature** à un appareil dans le tableau de bord de la solution.

1. Accédez au portail de la solution.

2. Accédez au volet **Appareils**.

3. Recherchez l’appareil personnalisé que vous avez créé qui envoie la télémétrie **ExternalTemperature**, et dans le volet **Informations sur l’appareil**, cliquez sur **Ajouter une règle**.

4. Sélectionnez **ExternalTemperature** dans **Champ de données**.

5. Définissez le **Seuil** sur 56. Cliquez ensuite sur **Enregistrer et afficher les règles**.

6. Retournez au tableau de bord pour afficher l’historique des alertes.

7. Dans la fenêtre de console que vous avez laissée ouverte, démarrez l’application de console Node.js pour commencer l’envoi des données de télémétrie **ExternalTemperature**.

8. Notez que la table **Historique des alertes** affiche les nouvelles alertes lorsque la nouvelle règle est déclenchée.
 
## <a name="additional-information"></a>Informations supplémentaires

La modification de l’opérateur **>** est plus complexe et dépasse le cadre de ce didacticiel. Alors que vous pouvez modifier le travail Stream Analytics pour utiliser l’opérateur souhaité, l’action de refléter cet opérateur dans le portail de la solution représente une tâche plus complexe. 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez vu comment créer des règles personnalisées, vous pouvez en apprendre plus sur les solutions préconfigurées :

- [Connecter Logic App à la solution préconfigurée de surveillance à distance Azure IoT Suite][lnk-logic-app]
- [Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#add-a-rule-for-the-new-device
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md


<!--HONumber=Dec16_HO3-->


